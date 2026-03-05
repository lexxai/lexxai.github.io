---
layout: post
title: "Mount SMB FreeBSD, монтування мережевих тек віндовс"
date: 2013-04-02 11:07:00 +0000
tags: ["FreeBSD", "samba"]
blogger_orig_link: https://lexxai.blogspot.com/2013/04/mount-smb-freebsd.html
---

[lissyara :: mount\_smbfs - монтування шар віндовс](http://www.lissyara.su/articles/freebsd/file_system/mount_smbfs/)  
  
З коментарів:  
*Yanis, 2006-06-16 в 12:41:06:*  

```
mount_smbfs -N //BACKUP@SERVER2/Bases /mnt
```

зробити це при авто завантаженні системи можливо так у файлі /etc/fstab:  

```
//BACKUP@SERVER2/BASES    /mnt    smbfs    rw,noauto    0    0
```

  
Щоб у ~/.nsmbrc не писати пароль в відкриому вигляді, можно викоистати команду  

```
smbutil crypt password_usera_backup
```

і потім  використати у ~/.nsmbrc:
  

```
password=$178465324253e0c07dcf8fbd8d2aa8eb18588171717
```
