---
layout: post
title: "FreeNAS, ZFS, User Quota, Samba"
date: 2018-04-19 00:26:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2018/04/freenas-zfs-user-quota-samba.html
---

[![](/assets/images/blog/96ef86a68c68d73f-6f00d6952ce66190.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEietF8AKkU5VUBxp-NFVQNWVsXcmULdyIVXJqOXX_9WNZ3IJg6Q9j0_qPZ6HJnzYw8AnWJ-x2Yy2nHihBUxDBqTYquMykcuwpSYmKLmtzhGhp7pphgDdnzk-NOp6PjeGdq1eA2j6kn6t315/s1600/q1.PNG)

### Task: Apply ZFS user quota to Smaba

#### Source:

zpool with samba user home folders: poolz2/samba/users  
  
Allow request about quota for everyone:   

```
zfs allow everyone userquota,userused poolz2/samba/users
```

  
Set usrer quota 120G for domain user : DOMAIN\username  

```
zfs set userquota@"DOMAIN\username"=120G poolz2/samba/users
```

  
Tune SAMBA,  /usr/local/etc/smb4.conf:  

```
get quota command = /mnt/poolz2/home/scripts/quota_get.sh %U %D
```

  
Reload SAMBA:
  

```
killall -HUP smbd 
```

Or change via GUI of FreeNAS

[![](/assets/images/blog/63422dc9aee7f569-086e717e6c54ef3e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbmo5EXZRiKQ4dTlTpKwPRqLzi4uM-BMPgcmbmmGhxBF81L4FPD4Rc3zGyyags4_AKgvMXxUBauXiPjl2MKc_A_RULu4dJ3klVoVOkvDzeb7jJBjDEyfM9tsRUFFlyzXnU8gwTsyLI4FJZ/s1600/freenas-smb-quota.PNG)  
*Example of GUI of SMB Services (FreeNAS11.2), add quota.*

#### Soulution:

Script for get quota data: /mnt/poolz2/home/scripts/quota\_get.sh  

```
#!/bin/sh
rtype=$4
if [ "${rtype}" == -1 ]; then
 exit
fi
username=$1
if [ ! -z  "$username" ]; then
  domainname=$2
  requestpath=${PWD}
  DATASETNAME=`/bin/df -l ${requestpath} | /usr/bin/tail -n 1 | /usr/bin/awk '{ print $1 };'`
  info=`/sbin/zfs userspace -Hp ${DATASETNAME}  | /usr/bin/grep -i ${domainname}'\\\\'${username}`
  usedbytes=`echo ${info}| /usr/bin/awk '{ printf "%.f", $4/1024 };'`;
  quotabytes=`echo ${info}| /usr/bin/awk '{ if ( $5 == "none" ) { print "0"} else { printf "%.f", $5/1024 }  };'`;
  if [ "$quotabytes" != 0 ]; then
   echo 2 $usedbytes $quotabytes $quotabytes $usedbytes $quotabytes $quotabytes
  fi
fi
exit
```

  

[![](/assets/images/blog/fb2da968d9c392aa-54311ee5f4452c90.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhW9v9eKdlnAkTWjLdvf6hyphenhyphengrsjypwt8GpNMKzwGdsE8Zchz1Scb43_v2THb16zsCduq4iZG2pFRYt9cTihQsorXaAcmvdbfMSwjKWWMenr8GKI2sRp6yZ5qm14PaqcPPao-BXxRLq1Y3UZ/s1600/freenas-smb-quota1.PNG)  
*Example of GUI of Sharing SMB (FreeNAS11.2).*

Easy script for define default quota for all users. I plan run it just by cron.  

```
#!/bin/sh

USERS_DATASET=poolz2/samba/users
DOMAINNAME=SOMEDOMAIN
DEFAULT_QUOTA=80G

/sbin/zfs allow everyone userquota,userused ${USERS_DATASET}
mountpoint=`/bin/df  -l ${USERS_DATASET} | /usr/bin/tail -n 1 | /usr/bin/awk '{ print $6 };'`
for file in ${mountpoint}/*; do
    USERNAME=$(/usr/bin/basename "$file")
    LOGON=${DOMAINNAME}\\${USERNAME}
    QUOTA=`/sbin/zfs get -H "userquota@${LOGON}" ${USERS_DATASET} | /usr/bin/awk '{ print $3 };'`
    #echo ${LOGON} : ${QUOTA}
    if [ "$QUOTA" == "none" ];then
        /sbin/zfs set "userquota@${LOGON}=${DEFAULT_QUOTA}" ${USERS_DATASET}
        #echo Was set default quota ${DEFAULT_QUOTA} for ${LOGON}
    fi
done

#/sbin/zfs userspace ${USERS_DATASET}
```

  
More:  

* [Samba and ZFS' User Quotas](https://www.xigmanas.com/forums/viewtopic.php?t=12000)
