---
layout: post
title: "Script for detect changing UID of SAMBA user"
date: 2019-03-15 00:38:00 +0000
tags: ["FreeBSD", "FreeNAS", "samba", "script", "shell", "unix"]
blogger_orig_link: https://lexxai.blogspot.com/2019/03/script-for-detect-changing-uid-of-samba.html
---

FreeNAS 11.2, SAMBA with AD  
  
Sript detect wrong user with sid begin with 900  
  
*wbinfo -i EDOMAIN\\user1   
**EDOMAIN\user1:\*:90000012:90000015:test user:/home/EDOMAIN/user1:/bin/sh***  
  

```
#!/bin/sh
wbinfoexe=/usr/local/bin/wbinfo
for user in $(${wbinfoexe} -u)
do
 uid=$(${wbinfoexe} -i "$user" | /usr/bin/awk -F: '{print $3}' | /usr/bin/egrep "^900")
 if [ ! -z "${uid}" ];then
  echo "Abnormal user is $user $uid"
  ${wbinfoexe} --logoff-user="${user}"
  net cache flush
  /etc/ix.rc.d/ix-activedirectory restart
  break
 fi
done
```
