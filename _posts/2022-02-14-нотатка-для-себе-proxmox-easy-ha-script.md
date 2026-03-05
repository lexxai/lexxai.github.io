---
layout: post
title: "Нотатка для себе. Proxmox easy HA script."
date: 2022-02-14 21:45:00 +0000
tags: ["administration", "Proxmox", "script", "Virtualization", "VM"]
blogger_orig_link: https://lexxai.blogspot.com/2022/02/proxmox-manual-easy-ha-proxmox-script.html
---

В деяких ситуаціях HA ([High Availability](https://pve.proxmox.com/wiki/High_Availability)) системи [Proxmox](https://pve.proxmox.com/wiki/Main_Page) для віртуальних машин не допомагає.

Такою ситуацією є випадок коли [резервна копія віртуальної машини](https://pve.proxmox.com/wiki/Backup_and_Restore#_backup_modes) налаштована як **stop mode**, а не **snapshot mode**.

І бувають ситуації на кшталт цієї:

```
INFO: timeout waiting on systemd
command 'qm start 101 --skiplock' failed: exit code 255
ERROR: Backup of VM 101 failed - timeout waiting on systemd
INFO: Failed at 2022-02-11 01:15:48
INFO: Backup job finished with errors
```

Тому простий скрипт котрий запускає за розкладом crond вирішує цю задачу:

```
#!/bin/sh

vmlist="101 102"
for i in $vmlist; do
 status=$(/usr/sbin/qm status $i)
 echo $status
  if [ ! "${status}" = "status: running" ];then
   echo "BAD"
   /usr/sbin/qm start $i
  else
   echo "OK"
  fi
done
```

corontab -e

```
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

*/30 * * * *      /home/check-vm.sh 2>1 >/dev/null
```

[![](/assets/images/blog/8321bd847cb4b411-1878d26a7a93882f.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEg2MUcJMrOL44pXmGvXrlPmMFqRJhUuBrS75fxjJo8p3ACXSQF5HXL0O-7NX9Wv02C4U5mrkojtP8-T2-MV5Ml8VgWouvAY9vxUdUz-6NB-3HoNeM5tlinfWStv3wwGtbOkZsyw1nrFWc_WwGJARQo5GHeZzsqq5Z5cnoQ2N1mLi_nW_cyWRPpnItC4Wg=s321)  
*Результат роботи скрипту*
