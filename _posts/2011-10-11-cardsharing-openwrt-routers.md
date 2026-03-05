---
layout: post
title: "cardsharing & openwrt routers"
date: 2011-10-11 23:39:00 +0000
tags: ["ar71xx", "Atheros", "firmware", "kernel", "mpcs", "openwrt", "oscam", "package", "router", "RS232", "TP-Link 1043"]
blogger_orig_link: https://lexxai.blogspot.com/2011/10/cardsharing-openwrt.html
---

**1.MPCS**  

* forum [mpcs (card server) on kamikaze 7.09 - How](https://forum.openwrt.org/viewtopic.php?pid=110326) [en]
* forum [mpcs для ADSL модемов и роутеров](http://sat-forum.su/index.php/board,79.0.html) [ru]
* [Прошивки для TP-LINK с mpcs](http://sat-forum.su/index.php/topic,8863.0.html%20) (DANtes) [ru]
* Source: [tags/mpcs-0.9e](http://streamboard.gmc.to/oscam/browser/tags/mpcs-0.9e) Start repository for OSCam (Open Source Cam). The start is from the MpCS source code. (start OSCam version 0.99)

  
  
  
  
**2. OSCam**  
OSCam is an **O**pen **S**ource **C**onditional **A**ccess **M**odule software, based on the very good MpCS version 0.9d created by [dukat](http://streamboard.gmc.to/wbb2/profile.php?userid=33325).   
  
NAME  
       OSCam - SC server  
  
  
DESCRIPTIONS  
       The OSCam software is an open source multi-protocol/multi-plattform SC server.  
  
  
      OSCam supports the following protocols:  
       ·  camd 3.3x  
       ·  camd 3.5x UDP and TCP  
       ·  newcamd with cascading/remote server ECM support  
       ·  radegast  
       ·  serial (HSIC, SSSP, BOMBA, DSR 9500)  
       ·  camd 3.57x with cascading/remote server ECM support, ECM and EMM support with size > 256 bytes  
       ·  camd 3.78x with cascading/remote server ECM support, ECM and EMM support with size > 256 bytes  
       ·  gbox with cascading/remote server ECM support  
       ·  CCcam with cascading/remote server ECM support, ECM and EMM support with size > 256 bytes  
       ·  DVB API with multi tuner and PIP support  
  
  
       OSCam works on the following plattforms:  
      ·  Linux (tuxbox, arm, mips, mipsel, sh4, powerpc, ...)  
      ·  Windows (based on cygwin1.dll)  
      ·  Mac OS X  
  

* [OSCam Wiki](http://streamboard.gmc.to:8001/)
* [OSCam Wiki](http://streamboard.gmc.to/wiki/OSCam/en)  [en] [[ua](http://translate.google.com.ua/translate?hl=uk&sl=auto&tl=uk&u=http%3A%2F%2Fstreamboard.gmc.to%2Fwiki%2FOSCam%2Fen&anno=2)]
* Forum: [oscam na openwrt (ar71xx) i tomato k2.6](http://openlinksys.pl/forum/viewthread.php?thread_id=11124&rowstart=0) [pl]
* oscam packages for  ar71xx:  [oscam\_XXXXX\_ar71xx.ipk](http://openwrt.groov.pl/projekty/oscam)
* Forum [OSCam Binaries Mips Fritzbox 7390 / TP-Link WR1043ND](http://streamboard.gmc.to/wbb2/thread.php?threadid=27723&hilight=openwrt%20) [de]
* [How To: (Cross-) compiling and toolchains](http://streamboard.gmc.to:8001/wiki/crosscompiling) [en]
* Source of [OSCam](http://streamboard.gmc.to/oscam/browser?order=name)
* Мастер компіляції OSCam [forum - OSCam Easy Build Script (script only!)](http://streamboard.gmc.to/wbb2/thread.php?threadid=29019)
* [OpenWrt For All: OSCam](http://openwrt.pedode.com/p/oscam.html)
* [OSCam for Android - WeTek Play - Android Files](http://www.sat-universe.com/showthread.php?t=282027)

**Власний досвід:**  

Що до мене використовую зараз "[Прошивки для TP-LINK с mpcs](http://sat-forum.su/index.php/topic,8863.0.html%20) (DANtes)", mpcs має менший розмір, та поки що влаштовує.  
Якщо хто використовує прошивки  DANtes з mpcs, то може буде цікаво знати, що цей пакунок насправді відсутній у самій прошивці, існує тільки модуль завантаження з мережі, а сам модуль mpcs завантажується при включенні роутера. Та існує Luci інтерфейс для можливості налаштувань mpcs.  
Але прийшлося написати  маленький скрипт *pl2303-watchdog.sh* котрий перезавантажує mpcs коли іноді інтерфейс rs232 перезавантажується із-за помилки.  

*pl2303-watchdog.sh:*

  

```
#!/bin/sh
/sbin/logread -f  | while read LINE; do
 if  echo $LINE|grep 'pl2303 converter now attached to tty'>/dev/null; then
    echo watch dog repairing... |logger -t 'mpcs'; /etc/init.d/mpcs_init restart;
 fi;
 done
```

  
Версія з контролем повтору, була створена так як потрібно було перевантажити модуль ядра  pl2303 разом з перевантаженням mpcs і з'являвся циклічний повтор:  
*pl2303-watchdog.sh:*
#!/bin/sh  
count=0  
/sbin/logread -f  | while read LINE; do  
 if  echo $LINE|grep 'pl2303 converter now attached to tty'>/dev/null; then  
    count=`expr $count + 1`  
    if [ $count = 1 ]; then  
    echo watch dog repairing... |logger -t 'mpcs'; /etc/init.d/mpcs\_init restart;  
;  
    count=-1  
    fi;  
 fi;  
 done  
  
Для перевантаження модуля був модифікований */etc/init.d/mpcs\_init*  
#!/bin/sh /etc/rc.common  
# Copyright (C) 2006 OpenWrt.org  
START=99  
  
start() {  
echo starting...|logger -t 'mpcs'  
      mpcs\_ext &  
      insmod pl2303 >/dev/null 2>/dev/null  
}  
  
stop() {  
echo stoping...|logger -t 'mpcs'  
  killall -9 mpcs >/dev/null 2>/dev/null  
  rm /tmp/mcps.mem >/dev/null 2>/dev/null  
  rm /tmp/\*.fifo >/dev/null 2>/dev/null  
  rmmod pl2303  
}  
  
Для автоматичного завантаження mpcs\_watchdogдо виконання  створимо файл */etc/init.d/mpcs\_watchdog*:  
#!/bin/sh /etc/rc.common  
# Copyright (C) 2008-2010 OpenWrt.org  
  
START=97  
start() {  
 /root/pl2303-watchdog.sh&  
}  
  
stop() {   
kill $(ps |grep "/sbin/logread -f"|cut -d " " -f 2|head -n 1)  
}  
  
Зробимо цей файл виноуємим:   
сhmod +x /etc/init.d/mpcs\_watchdog  
  
  
ВДОСКОНАЛЕННЯ:   
В результаті роботи булу встановлено що скрипт *mpcs\_watchdog* працює не надійно. Виявлені проблеми з завантаженням процесора процесом 'mpcs' до високого рівня. Тому встановлено наступний скрипт що робить моніторинг завантаженості процесора і у випадку високого значення виконує перезавантаження процесу 'mpcs' .  
cpucalc-m.sh:  
  

#!/bin/bash  
# by Paul Colby (http://colby.id.au), no rights reserved ;)  
  
PREV\_TOTAL=0  
PREV\_IDLE=0  
  
while true; do  
  CPU=(`cat /proc/stat | grep '^cpu '`) # Get the total CPU statistics.  
  unset CPU[0]                          # Discard the "cpu" prefix.  
  IDLE=${CPU[4]}                        # Get the idle CPU time.  
  
  # Calculate the total CPU time.  
  TOTAL=0  
  for VALUE in "${CPU[@]}"; do  
    let "TOTAL=$TOTAL+$VALUE"  
  done  
  
  # Calculate the CPU usage since we last checked.  
  let "DIFF\_IDLE=$IDLE-$PREV\_IDLE"  
  let "DIFF\_TOTAL=$TOTAL-$PREV\_TOTAL"  
  let "DIFF\_USAGE=(1000\*($DIFF\_TOTAL-$DIFF\_IDLE)/$DIFF\_TOTAL+5)/10"

#MONITORING VALUE OF CPU LOAD

if [ $DIFF\_USAGE -ge 90 ];  
then  
echo Repairing by cpu $DIFF\_USAGE% overload...  |logger -t 'mpcs'  
/etc/init.d/mpcs\_init restart  
fi

  # Remember the total and idle CPU times for the next check.  
  PREV\_TOTAL="$TOTAL"  
  PREV\_IDLE="$IDLE"  
  
  # Wait before checking again.  
  sleep 10  
done  
  
Для роботи цього скрипту потртібен bash. Встановлюємо opkg update; opkg install bash.  
І замінемо файл  /root/pl2303-watchdog.sh файлом cpucalc-m.sh.   
  
  
  
  
  
розмір файлу mpcs 258340 байт, завантажується у архіві.  
  
Пробував [oscam\_XXXXX\_ar71xx.ipk](http://openwrt.groov.pl/projekty/oscam)  - працює  
oscam 1.00-r5542 має розмір понад 600 кбайт.  
  
Тестування:  
Для тестування роботи програми можливо використовувати програми монітори для віддалеенго перегляду файлів протоколів,  наприклад ось таку: [microMon](http://micromon.software.informer.com/)  

[![](/assets/images/blog/2800541bd5fffcc6-801c636b466058cc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipdqe6gf8UEjpH9vBJf3KggSLeHjWLjBM5Ch3j16lBX6sjWFEFtjjdBgJmoXOh45Z0ti6Jr4erM3Srca7w4oBTwdHnWAqRXG7GUzmrPjUFqMCUC-OlWE9-mLdqwC5rYabJzInevZCqwT7t/s1600/mmon-setting.PNG)

  

[![](/assets/images/blog/132f922ef127f824-1a1b63491387c6bc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjicTvGRmwPaf-SKOn3BiUSmltwua3LP-Sycm35TBaiZ_GPB7feP3fgSEqa5qr9HSw8kRnHrHW0AnYEzSybUUvOd5TOfOW9gg7oRXylGiYrb7HPVJ_0REiS-EJkWwWZEhA9097u2r_K7Gk-/s1600/mmon.png)
