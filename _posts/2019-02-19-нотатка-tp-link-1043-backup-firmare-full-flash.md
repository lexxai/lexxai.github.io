---
layout: post
title: "Нотатка. TP-LINK 1043 backup firmare / full flash"
date: 2019-02-19 23:10:00 +0000
tags: ["firmware", "open-wrt", "tp-link", "TP-Link 1043"]
blogger_orig_link: https://lexxai.blogspot.com/2019/02/tp-link-1043-backup-firmare.html
---

Не знаю чи працює, але занотую для себе як зробити резервну копію прошивки роутера.  
  

[![](/assets/images/blog/b9ed0562c8fb8cc3-98bbcd532fae8a73.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipMI1YgCV3W65mYq2K66cpIuY7qFUkPHCKK0KWpFEvloKfO8rUCeUXRvAMPqbQ-WW_JuXyl-6qSduCAfF0vWAev6kpD8J242t7B_3aissolYr4LqOSiwXjMcW2jf6vNR18lIBaR3E7rBNe/s1600/1043firware.PNG)

  
Повний дамп для прошивання програматором:
  

```
cat /dev/mtd0 > /tmp/backup_fullflash.bin
cat /dev/mtd5 >> /tmp/backup_fullflash.bin
cat /dev/mtd4 >> /tmp/backup_fullflash.bin
```

Окремі часини:  
**u-boot**
  

```
cat /dev/mtd0 > /tmp/backup_u-boot.bin 
```

**ART**
  

```
cat /dev/mtd4 > /tmp/backup_art.bin
```

**firmware**
  

```
cat /dev/mtd5 > /tmp/backup_firmware.bin
```

  
За матеріалами:
[Маршрутизатор TP-Link TL-WR1043ND Atheros, 3x3 MIMO, 2.4GHz, 802.11n Draft 2 (частина 5) [111] - Конференція iXBT.com](https://forum.ixbt.com/topic.cgi?id=14:58841:3126#3126)
