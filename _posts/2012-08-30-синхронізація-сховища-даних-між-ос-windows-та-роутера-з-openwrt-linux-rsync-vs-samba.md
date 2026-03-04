---
layout: post
title: "синхронізація сховища даних між ОС Windows та роутера з OpenWRT (Linux), rsync vs samba"
date: 2012-08-30 20:41:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/08/windows-openwrt-linux-rsync.html
---

Для того щоб дивитися фільми на телевізорі у час коли комп'ютер вимкнено, змонтував зовнішній накопичувач на роутері. Але фільми знаходяться на робочому комп'ютері що роздаються торентами, тому я роблю повну синхронізацію між комп'ютером та роутером за допомогою rsync, як альтернативу синхронізації через SMB. І потім порівняємо.  
  
  
  
  
Роутер TP-Link 1043, OpenWRT trunc r3...   
#opkg install rsync , (rsync - 3.0.9-1)  
*/etc/rsyncd.conf:*# /etc/rsyncd.conf  
# Minimal configuration for rsync daemon  
  
# Next line required for init script  
pid file = /var/run/rsyncd.pid  
use chroot = yes  
uid = nobody  
gid = nogroup  
read only = no  
  
# Simple example to configure server  
#[openwrt-etc]  
#path = /etc  
#comment = OpenWrt Configuration Files  
#exclude = /init.d  
  
[movie]  
path = /mnt/hddstore/movie  
comment = movie  
  

#/etc/init.d/rsyncd enable

#/etc/init.d/rsyncd start

  
ss  
  
  
Windows 7  
[cwRsync](https://www.itefix.no/i2/cwrsync), [download](http://ignum.dl.sourceforge.net/project/sereds/cwRsync/4.0.3/cwRsync_4.0.3_Installer.zip)  
  
*sync\_router.cmd:*   

cd /d "C:\Program Files (x86)\cwRsync\bin"  
rsync -v -au --delete-after "/cygdrive/e/public/videos/serials" rsync://192.168.0.3:873/movie

  
  
building file list ... done  
serials/  
serials/Thumbs.db  
serials/Franklin.and.Bash/  
serials/Franklin.and.Bash/Franklin.and.Bash.s01e10.WEB-DL.Ukr.Eng.uateam.txt  
serials/Game.of.Thrones/  
serials/Game.of.Thrones/Game.of.Thrones.S02E10.HDTV.Ukr.Eng.uateam.txt  
serials/House M.D (Season 6) Ukr/  
serials/House M.D (Season 6) Ukr/House.M.D.s06e01.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e02.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e03.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e04.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e05.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e06.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e07.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e08.BDRip.Ukr(stb).Eng\_0dayTeam.avi  
serials/House M.D (Season 6) Ukr/House.M.D.s06e09.BDRip.Ukr(stb).Eng\_0dayTeam.avi   
  
  
Завантаження процесора роутера:  
#top  

PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND

 3126  3125 nobody   S     1928   7%  70% /usr/bin/rsync --daemon  
 2767     2 root     SW       0   0%   2% [usb-storage]  
  
  
  

[![](/assets/images/blog/a5ad9b6a416171f7-80078acc9a05c1a1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaBWO9TN3IqLcPh6XtIMFfqB065Y6D4xf_JPd6J8UdGrZsV5B0bye33dgf2OEYeDhTs7Uo91JBpYGljzmHE_oXr1rfn0YJPCdNlokuSxssYE7hY105te_OFdz-llAM0G3mK-bFJltTrDSP/s1600/rsync-1.png)  
*Завантаження каналупри синхронізації RSYNC*

### Порівняємо зі швидкістю запису через SMB та через RSYNC

  
Завантаження процесора роутера:  
PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND  
 3117  2244 nobody   R     3780  13%  54% /usr/sbin/smbd -D  
 3075     2 root     SW       0   0%   4% [jbd2/sdb2-8]  
 2767     2 root     SW       0   0%   3% [usb-storage]  
  

[![](/assets/images/blog/24f0104ad527d408-993e9875e24de50d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrI_BpJhGzV64Dn0s8l6nUPqiznUNoUBrV-qavIs1nwFxRKx5FYyztyooKCKxFin7no_zChdqVohQ-IdAzk9FV4UmU-Qs-6gRRfgMhV8tfMu-z1JwP8m-K4o6c1bQEB9zHkbrsF-I0iq-j/s1600/rsync-2..PNG)  
*Завантаження каналупри синхронізації SMB*

[![](/assets/images/blog/2acc893ad5ecb91c-756bfe0d4dbd06a4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8ToyyMJra3sDmj7apo_gtT008rvrU157ohVgZLEy1eu3kdu2PZNtcfCWYGVVFRWexJ-HBHzsA_e-SQU1S-zRkq1e8O5KvxeuHIHKix8Be1yVZvcuSDrsDNfKkp99e3ZjzaqqmVrXepi3Z/s1600/rsync-3.PNG)  
*Швидкістьпри синхронізації SMB*

При такому розкладі, SMB явно виграє у RSYNC.  
Залишилось найти програму для синхронізації тек та файлів для Windows.  
Зупинився на [GoodSync](http://www.goodsync.com/)   
  

[![](/assets/images/blog/c907e9f8df3a5eb7-41c3eb8136866072.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3C8wDJRo5f0fu-sJqB8UPxgBjlO4t_S9WEGRbdapYvKpC_Vgnqvcti1Cc4YsNSWAbRi6OZcKygqg2wmJSBqGym_U-DTV95WDSwxtO20LF73BW_lSHZC9dtXHjLIcIf2vgXCDMx_XicL8o/s1600/rsync-4.PNG)  
*Синхронізація за допомогою програми GoodSync*

  
  
Все не погано, але вона потребує оплати, можемо користуватися 30 днів тільки.  
  
Найшовся безкоштовний варіант, і дуже пристойний варіант [FreeFileSync](http://freefilesync.sourceforge.net/)  
  

[![](/assets/images/blog/1cad20ef10693ec3-68cbdb243572d555.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi34b8LL8igDkAWvJpc7bfs8rzsHvVoVS0PzauZdkiSYT-C8JanBGfAlJap6TGJ8shiJ9XTv_ZJKLXgsvhBpBOLmK6FTMBG-rePj2RSjO40jSgRs1j1DwADQu71oZwDi4ABnT3FFQBWjVPw/s1600/rsync-5.PNG)  
*Синхронізація за допомогою програми FreeFileSync*
