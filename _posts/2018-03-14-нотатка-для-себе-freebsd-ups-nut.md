---
layout: post
title: "Нотатка для себе. FreeBSD, UPS, NUT."
date: 2018-03-14 23:07:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2018/03/freebsd-ups-nut.html
---

Є сервер FreeBSD 11.1, ДБЖ (UPS) Cyber Power Systems CP1500AVRLCD.  
Програма NUT  має підтримку даного пристою.  
  

[![](/assets/images/blog/a23b51f8c15f876f-f64976299f30963f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGMUv9X4azyfCjok4uFOq2Z202iKw6ibyJNW3MTuNg9EJ4rOMuNTXvsvhgxB4ucG3x0FyVVVsn2fRtYEnt3aUJEddcfmh4Q_q93RF2dVli75shaqQlD8ABDVtTZJHDMR6XIrsT4U2hTNNa/s1600/cyber-ups.PNG)  
*Підтримка програмою NUT ДБЖ  "Cyber Power Systems CP1500AVRLCD"*

Пошук підключеного пристрою через USB.  
#dmesg | grep usbus  
ugen0.2: <CPS CP 1500C> at usbus0  
  
Надаємо доступ до "/dev/ugen0.2" групі користувачів uucp  

##### /etc/devfs.conf

```
own     /dev/ugen0.2    root:uucp
```

Затіяти зміни: service devfs restart

##### /usr/local/etc/nut/nut.conf

```
MODE=standalone
```

##### ups.conf

```
[CP1500AVRLCD]
    driver = usbhid-ups
    port = /dev/ugen0.2
    desc = "cyberpowersystems CP1500AVRLCD"
```

##### upsd.conf

```
LISTEN 127.0.0.1 3493
```

##### upsd.users

```
[upsmon]
        password  = pass
        upsmon master

[admin]
        password = passwordsecure
        actions = SET
        instcmds = ALL
```

##### upsmon.conf

```
MONITOR CP1500AVRLCD@localhost 1 admin passwordsecure master
MINSUPPLIES 1
SHUTDOWNCMD "/sbin/shutdown -h +0"
NOTIFYCMD /usr/local/etc/nut/nut-notify.sh
POLLFREQ 5
POLLFREQALERT 5
HOSTSYNC 15
DEADTIME 15
POWERDOWNFLAG /etc/killpower
NOTIFYFLAG ONLINE  SYSLOG+WALL+EXEC
NOTIFYFLAG ONBATT  SYSLOG+WALL+EXEC
NOTIFYFLAG LOWBATT SYSLOG+WALL+EXEC
NOTIFYFLAG FSD             SYSLOG+WALL+EXEC
NOTIFYFLAG COMMOK  SYSLOG+WALL+EXEC
NOTIFYFLAG COMMBAD SYSLOG+WALL+EXEC
NOTIFYFLAG SHUTDOWN        SYSLOG+WALL+EXEC
NOTIFYFLAG REPLBATT        SYSLOG+WALL+EXEC
NOTIFYFLAG NOCOMM  SYSLOG+WALL+EXEC
NOTIFYFLAG NOPARENT        SYSLOG+WALL+EXEC
RBWARNTIME 43200
NOCOMMWARNTIME 300
FINALDELAY 5
```

  
*Особливості* "upsmon.conf": треба під'єднуватися як admin, у іншому випадку програма не зможе передати драйверу ДБЖ команду (shutdown.return) про необхідність  вимкнути  ДБЖ по завершенню, а випадку коли з'явиться напруга раніше то перевантажити сервер через цикл вимкнути-ввімкнути. Перелік підтримуваних команд драйвером:
  

```
#upscmd -l -u admin -p passwordsecure CP1500AVRLCD@localhost
Instant commands supported on UPS [CP1500AVRLCD]:

beeper.disable - Disable the UPS beeper
beeper.enable - Enable the UPS beeper
beeper.mute - Temporarily mute the UPS beeper
beeper.off - Obsolete (use beeper.disable or beeper.mute)
beeper.on - Obsolete (use beeper.enable)
load.off - Turn off the load immediately
load.off.delay - Turn off the load with a delay (seconds)
load.on - Turn on the load immediately
load.on.delay - Turn on the load with a delay (seconds)
shutdown.return - Turn off the load and return when power is back
shutdown.stayoff - Turn off the load and remain off
shutdown.stop - Stop a shutdown in progress
test.battery.start.deep - Start a deep battery test
test.battery.start.quick - Start a quick battery test
test.battery.stop - Stop the battery test
```

  
Для організації сповіщень поштою про зміну стану роботи ДБЖ додано до NOTIFYFLAG +EXEC, і файл скрипт NOTIFYCMD nut-notify.sh у upsmon.conf.
  

##### nut-notify.sh

```
#!/bin/sh
echo "$@" | mail -s "UPS [NUT] Notice on fileserver1" root
```

##### А для виконання періодичної самодіагностики ось скрипт для планувальника cron. self-test.sh

```
#!/bin/sh
state=`/usr/local/bin/upscmd -u admin -p passwordsecure CP1500AVRLCD@localhost test.battery.start.quick 2>&1`
result=`/usr/local/bin/upsc CP1500AVRLCD@localhost ups.test.result`
/usr/local/etc/nut/nut-notify.sh "Run self diagnostic: ${state} result of UPS (`date`) on `/bin/hostname` is: ${result}"
```
