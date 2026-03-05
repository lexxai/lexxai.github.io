---
layout: post
title: "FreeBSD шифрування файлової системи ZFS з використанням віддалених ключів"
date: 2018-03-13 00:56:00 +0000
tags: ["boot", "encript", "FreeBSD", "GELI", "rsync", "snapshot", "ZFS", "zfsroot"]
blogger_orig_link: https://lexxai.blogspot.com/2018/03/freebsd-zfs-root-with-encripted-disk-by.html
---

[![](/assets/images/blog/9d19c9975d4f078e-c2512129db205f00.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYhFP-fUW0k6D_cEuEXLZ3MSfX2udY4lNR6teurZCnatXtZHVfGF8KgmV45xrPyn2yMn6cJ6a7LYSX8lDuo0bwjhN77k_xU-Q6xW-Etc-3I_NJy24zDRRJUDwvnB0fu80Vy1eNs2vhCHJT/s1600/zfs-fe.PNG)

  
Нотатка для себе.   
Необхідно створити систему котра використовує шифрування дисків, zfs кореневу файлову систему, і для шифрування використовуються ключі що зберігаються на віддаленому сервері.  
  
Моя система має 3 диски, один диск (Flash/SSD) завантажувальний з файловою системою ZFS (ZROOT). Та 2 диски для шифрованого zfs пулу котрі  повинні замінити собою перший диск для запису  
  
На перший диск встановлено  було операційну систему FreeBSD 11.1 майстром встановлення з вибором ZFS Boot.  
  
  
Надалі в робочій системі вже виконувалися сценарії по:  
Створення zfs pool на основі шифрованих дисків за допомогою geli (ключ без паролю).  
zfs pool mirror з двох дисків /dev/ada0.eli та /dev/ada1.eli.  
Потім копіюється інформація з першого диску на створений пул.  
  
  

```
#!/bin/sh

kldload -n geom_eli
mkdir ./keys

dd if=/dev/random of=./keys/ada0.key bs=4096 count=1
dd if=/dev/random of=./keys/ada1.key bs=4096 count=1

zfspool=tank
zpool destroy ${zfspool}

rm -r /${zfspool}

geli detach /dev/ada0.eli
geli detach /dev/ada1.eli

gpart destroy -F /dev/ada0
gpart destroy -F /dev/ada1

geli init -P -l 256  -s 4096 -K ./keys/ada0.key /dev/ada0
geli init -P -l 256  -s 4096 -K ./keys/ada1.key /dev/ada1

geli attach -p -k ./keys/ada0.key /dev/ada0
geli attach -p -k ./keys/ada1.key /dev/ada1

ls /dev/ada*

zpool status

snapshot_string=DO_NOT_DELETE_
timestamp=$(/bin/date '+%Y%m%d%H%M%S')
source_pool=zroot
destination_pool=${zfspool}
new_snap="$source_pool"@"$snapshot_string""$timestamp"

mountpt=${zfspool}


zpool create -R /${zfspool} ${zfspool} mirror /dev/ada0.eli /dev/ada1.eli
zfs set compress=lz4 ${zfspool}
zfs set mountpoint=none ${zfspool}


zpool status
zfs list


# Create first recursive snapshot of the whole pool.
echo "zfs snapshot -r  "${new_snap}"    "
zfs snapshot -r  "${new_snap}"
zfs list -t snapshot


echo coping data from zroot
echo "--------"
zfs list -t snapshot

# Initial send:
echo "zfs send -vR ${new_snap} | zfs recv -vFu ${zfspool}/fromz"
zfs send -vR ${new_snap} | zfs recv -vFu ${zfspool}/fromz
echo "zfs destroy ${new_snap}"
zfs destroy -r ${new_snap}

zfs set mountpoint=/ROOTO ${zfspool}/fromz/ROOT/default
zfs set canmount=on ${zfspool}/fromz/ROOT/default
zfs set canmount=on ${zfspool}/fromz/usr
zfs set canmount=on ${zfspool}/fromz/var
zfs create -o mountpoint=/root ${zfspool}/fromz/root
```

```
zfs mount -a
```

```
 
```

```
#set mountpoint=none
```

```
MLIST="/tmp /usr /usr/home /usr/ports /usr/src /var /var/audit /var/crash /var/log /var/mail /var/tmp"
MDLIST="${MLIST} /root "
 for i in ${MDLIST}
 do
  cmdd="zfs set mountpoint=none ${zfspool}/fromz${i}"
  echo ${cmdd}
  `${cmdd}`
 done

cmdd="zfs  mountpoint=none ${zfspool}/fromz"
echo ${cmdd}
`${cmdd}`
```

```
 
```

```
zfspool=tank
MPLIST="usr var root"
DESTDIR=/${zfspool}/
SRCDIR=/${zfspool}/ROOTO/
for i in ${MPLIST}
do
    echo "--------${SRCDIR}${i}----${DESTDIR}${i}"
    command="rsync -ra ${SRCDIR}${i} ${DESTDIR}"
    echo ${command}
    `${command}`
done


zfs umount ${zfspool}/fromz/ROOT/default
zfs destroy -r  ${zfspool}/fromz/ROOT
rmdir /${zfspool}/ROOTO
zpool export ${zfspool}
echo "finish"
zfs get -s local all
```

  
На далі монтування при завантаженні системи.  
/etc/mntscript/get\_keys\_and\_mount.sh:
  

```
#!/bin/sh
REMOTESTORE="remoteuser@remotehost:~/remote_key_path/*"
REMOTESTORE_IDFILE="/root/.ssh/id_rsa"
KDIR="/root/keys"
ABORT=0
MAXCOUNTS=5
NEXIT=0
rootpool=zroot
zfspool=tank
zfspool_mountpoint=/

MPLIST="/tmp /usr/home /usr/ports /usr/src /var/audit /var/crash /var/log /var/mail /var/tmp"
U_MPLIST="${MPLIST} /usr /var"
M_MPLIST="/usr /var ${MPLIST}"

# *** create and mount TMP RAM DISK
/sbin/mdmfs -s 1m md1 ${KDIR}

# *** load remote keys via scp
while [ ${ABORT} -eq 0 ]; do
 echo "LOADING KEYS..."
 umask 027; /usr/bin/scp  -q -o ConnectTimeout=30 -i ${REMOTESTORE_IDFILE} ${REMOTESTORE} ${KDIR}
 if [ -s ${KDIR}/ada0.key ] && [ -s ${KDIR}/ada1.key ]; then
  echo "KEYS LOADED"
  ABORT=1
 else
  echo "Keys not loaded. Delays 5 sec for next retry #${MAXCOUNTS}"
  sleep 5
  MAXCOUNTS=`expr ${MAXCOUNTS} - 1`
  if [ ${MAXCOUNTS} -lt 0 ]; then
   echo "LIMIT OF CONECTIONS IS OUT"
   ABORT=1
   NEXIT=1
  fi
 fi
done

#check if need exit in case missing keys
if [ ${NEXIT} -eq 0 ]; then
 #use loaded keys

 #check mounted ZFS POOLS
 /sbin/zpool status -x ${zfspool}
 if [ $? -eq 0 ]; then
   echo Already imported pool ${zfspool}, try export pool
  /sbin/zpool export ${zfspool}
 fi

 #load ENCRIPTING MODULE
 kldload -n geom_eli
 for i in 0 1
 do
  ELIDISK="ada${i}"
  /sbin/geli status ${ELIDISK}.eli
  if [ $? -eq 0 ]; then
   /sbin/geli detach ${ELIDISK}.eli
  fi
  /sbin/geli attach -p -k ${KDIR}/${ELIDISK}.key /dev/${ELIDISK}
 done


# release keys
 rm ${KDIR}/*.key
 # release RAM disk
 umount -f ${KDIR}
 /sbin/mdconfig -du md1

 # mount ZFS POOLS

 /sbin/zpool status -x ${zfspool}
 if [ $? -eq 0 ]; then
  echo Already imported pool ${zfspool}, try export pool
 /sbin/zpool export ${zfspool}
 fi
 /sbin/zpool import -R ${zfspool_mountpoint} ${zfspool}

#check operation that zpool imported was ok?
if [ $? -eq 0 ]; then
 #check that need ummount root system pool
 if [ "${zfspool_mountpoint}" = "/" ];then
  #try unmount root mount points
  for i in ${U_MPLIST}
  do
   cmdd="/sbin/zfs umount -f ${rootpool}${i}"
 #  echo s ${cmdd}
   `${cmdd}`
  done
 fi

 #try mount pool in legacy mode, with  set mountpoint propery to none
 #add to mounting list more folders
 MDLIST="${M_MPLIST} /root"
 for i in ${MDLIST}
 do
  cmdd="/sbin/mount -t zfs ${zfspool}/fromz${i}  ${zfspool_mountpoint}${i}"
 # echo s ${cmdd}
  `${cmdd}`
 done
fi
echo "Finish mount lgeli"
#if check need exit
else
#exit w/o keys
  # release RAM disk
 /sbin/umount -f ${KDIR}
 /sbin/mdconfig -du md1
 echo "Finish w/o mount lgeli"
fi
```

/etc/rc.conf/lgeli
  

```
#!/bin/sh

# PROVIDE: lgeli
# REQUIRE: NETWORKING
```

```
# BEFORE: syslogd
# KEYWORD: nojail

. /etc/rc.subr

name="lgeli"
start_cmd="lgeli_start"
stop_cmd="lgeli_stop"
rcvar="lgeli_enable"

lgeli_start()
{
 /etc/mntscript/get_keys_and_mount.sh
}

lgeli_stop()
{
zfspool=tank
/sbin/zpool export ${zfspool}
geli detach /dev/ada0.eli
geli detach /dev/ada1.eli
}

load_rc_config $name
run_rc_command "$1"
```

  
Не претендую на манул для дії кого не будь, це результат експерименту занотований щоб не загубити і можливо отримати можливі рекомендації від інших користувачів.
