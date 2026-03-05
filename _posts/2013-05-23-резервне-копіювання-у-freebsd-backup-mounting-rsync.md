---
layout: post
title: "Резервне копіювання у FreeBSD. Backup, mounting, rsync"
date: 2013-05-23 19:57:00 +0000
tags: ["backup", "FreeBSD", "mount", "rsync"]
blogger_orig_link: https://lexxai.blogspot.com/2013/05/freebsd-backup-mounting-rsync.html
---

При копіюванні на зовнішні накопичувачі було з часом створенно невеликий скрипт, а зараз він був дорозвинутий до системи з сповіщенямі про проблеми та автоматичного "ремонту" USB накопичувача.  
  

```
#!/bin/sh 
 
#list devices and mount points
LOCAL_BACKUP=/backup
LOCAL_BACKUP_DEV="/dev/ada2s1d"
BOOT_BACKUP=/backup_boot
BOOT_BACKUP_DEV="/dev/ada3p3"

BOOT_BACKUP_USB=/backup_boot_usb
BOOT_BACKUP_USB_DEV="/dev/da6p3" 
 
#list devices for unmount 
MOUNT_DEVS="$LOCAL_BACKUP $BOOT_BACKUP $BOOT_BACKUP_USB"

ALERT_EMAIL="some@email.net"

LOG_DIR="/tmp"
SCRIPT_DIR="/usr/local/backup"
 
#functions 
 
#Logging messages
logged(){
 echo "`date`" | mail -s "[HOST-BACKUP] Log: ${1}" ${ALERT_EMAIL}
}
 
#Checking free space at volumes  
check_freespace(){
 DLABEL=${1}
 USEDSPACE=`/bin/df ${DLABEL} | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{ print $5}' | cut -d'%' -f1`
 if [ ${USEDSPACE} -ge 95 ]; then
   echo "Running out of space \"${DLABEL} (${USEDSPACE}%)\" on $(hostname) as on $(date)" |
     mail -s "[HOST-BACKUP] Backup Alert: Almost out of disk space \"${DLABEL} (${USEDSPACE}%)\"" ${ALERT_EMAIL}
 fi
 if [ ${USEDSPACE} -ge 100 ]; then
   echo "Running out of space \"${DLABEL} (${USEDSPACE}%)\" on $(hostname) as on $(date)" |
     mail -s "[HOST-BACKUP] Backup Critical Alert: File System is ${USEDSPACE}%) used on \"${DLABEL}\"" ${ALERT_EMAIL}
 fi
}



 
#Checking mounted volumes 
check_mounted(){
 /sbin/mount | grep "${1} on ${2}" >/dev/null
 if [ "$?" -eq "0" ]; then
    return 1
 else
    return 0
 fi
}
 
#Umounting all mounted volumes
unmount_all(){
for i in ${MOUNT_DEVS}
do
  eval lab="${i}"
  eval device="${i}_DEV"
  check_mounted "${device}" "${lab}"
  if [ "$?" -eq "1" ]; then
    /sbin/umount ${lab}
  fi
done
}

 
#log message and exit and emergency unmount  
logged_exit(){
 echo "`date`" | mail -s "[HOST-BACKUP] Alert, unexpected EXIT: ${1}" ${ALERT_EMAIL}
 unmount_all
 exit
}
#end functions 
 
#main

CURRDATE="`/bin/date "+%Y%m%d"`"
#echo "${ARCHIVE_NAME}"|
/sbin/mount | mail -s "[HOST-BACKUP] ${CURRDATE} - Backup process starting" ${ALERT_EMAIL}

############################
# backup local 
############################ 
#checking present device for mount

if [ -r ${LOCAL_BACKUP_DEV} ]
then
#checking directory for mount point  
 if [ ! -d ${LOCAL_BACKUP}]
 then
   /sbin/umount ${LOCAL_BACKUP} && logged  "Was pre ummount ${LOCAL_BACKUP_DEV} as ${LOCAL_BACKUP}"
   /sbin/mount -o noatime ${LOCAL_BACKUP_DEV} ${LOCAL_BACKUP} || logged_exit "Can't mount ${LOCAL_BACKUP_DEV} as ${LOCAL_BACKUP}"
    #checking free space
    check_freespace ${LOCAL_BACKUP}
    #run external script for backup 
    ${SCRIPT_DIR}/backup_local.sh
    /sbin/umount ${LOCAL_BACKUP} || logged  "Can't after unmount ${LOCAL_BACKUP_DEV} as ${LOCAL_BACKUP}"
 fi
else
 logged  "Device ${LOCAL_BACKUP_DEV}, not found. Check attaching of device. Backup to volume ${LOCAL_BACKUP} was skiped."
fi

 
############################
# backup usb
############################
if [ -r ${BOOT_BACKUP_USB_DEV} ]
then
 if [ -d ${BOOT_BACKUP_USB} ]
 then
   /sbin/umount ${BOOT_BACKUP_USB} && logged  "Was pre ummount ${BOOT_BACKUP_USB_DEV} as ${BOOT_BACKUP_USB}"

   check_mounted ${BOOT_BACKUP_USB_DEV} ${BOOT_BACKUP_USB}
   if [ $? -eq 0 ]; then
     #device unmounted
      /sbin/mount -o noatime ${BOOT_BACKUP_USB_DEV} ${BOOT_BACKUP_USB}
      if [ $? -ne 0 ]; then
        logged "Something went wrong with the mount (${BOOT_BACKUP_USB})... try recover and remount"
        /sbin/fsck -y -t ufs ${BOOT_BACKUP_USB_DEV} > ${LOG_DIR}/host-backup-fsck.log
        /sbin/mount -o noatime ${BOOT_BACKUP_USB_DEV} ${BOOT_BACKUP_USB} || logged_exit "Can't mount ${BOOT_BACKUP_USB_DEV} as ${BOOT_BACKUP_USB}"
        logged "mounted aftrer recovering (${BOOT_BACKUP_USB})"
      fi
   else
     #still mounted , use it
     logged  "Use already mounted point of ${BOOT_BACKUP_USB_DEV} as ${BOOT_BACKUP_USB}"
   fi
    check_freespace ${BOOT_BACKUP_USB}
    ${SCRIPT_DIR}/backup_boot_usb.sh
    /sbin/umount ${BOOT_BACKUP_USB} || logged  "Can't after ummount ${BOOT_BACKUP_USB_DEV} as ${BOOT_BACKUP_USB}"
 fi
else
 logged  "Device ${BOOT_BACKUP_USB_DEV}, not found. Check attaching of device. Backup to volume ${BOOT_BACKUP_USB} was skiped."
fi
############################ 

# end backup usb
############################  
# and finally unmount all
############################
unmount_all
```

 
External backup script  
  
backup\_boot\_usb.sh:
 
  

```
#!/bin/csh
# local backup to usb
/usr/bin/time -h rsync -v -rlptgoD --delete --stats --exclude=dumps --exclude=lost+found --exclude=found_imap --exclude=found_users --exclude=.sujournal  --exclude=.snap /data/ /backup_boot_usb/data/ >& /tmp/rsync_data_usb.log
```
