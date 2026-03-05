---
layout: post
title: "pfsense синхронізація pf таблиці snort2c на інший firewall"
date: 2019-11-08 02:27:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/11/pfsense-pf-snort2c-firewall.html
---

[![](/assets/images/blog/2e85b51bb5193863-1e0320b3b90b936a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVrpBGgibZpXBUne-DaS0E9EUXkLtZof5QvFkbP9vMxXJRoDrROMzSxqB1SJPiT-6Nly6_fFpP7Dw28qyPeVolf-GK6YtL0k4_wjTPDWL6du4xEGNF-UO9E7SiRIabQeHYZDk_En_xCZhH/s1600/pfsence-logo.PNG)

Є фаєрвол [pfsense](https://www.pfsense.org/) (192.168.0.2) з встановленим пакунком [snort](https://www.snort.org/).  
Snort аналізує проток даних що приходить від мережевого інтерфейсу котрий під'єднаний до [mirror port](https://en.wikipedia.org/wiki/Port_mirroring) мережевого комутатора HP 2920.  
Програма [Snort](https://www.snort.org/) аналізує мережевий потік що йде від [ISP](https://uk.wikipedia.org/wiki/%D0%9F%D0%BE%D1%81%D1%82%D0%B0%D1%87%D0%B0%D0%BB%D1%8C%D0%BD%D0%B8%D0%BA_%D0%BF%D0%BE%D1%81%D0%BB%D1%83%D0%B3_%D0%86%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82%D1%83). На основі правил, отриманих за підписками, [snort](https://www.snort.org/) блокує IP адреси заносячи їх до відповідної таблиці фаєрволу [pf](https://en.wikipedia.org/wiki/PF_(firewall)) - '*snort2c*'.  
Так як для аналізу мережевого потоку використовується не основний фаєрвол, а інший сервер, то за таблицею '*snort2c*' нічого реально не блокується.  
Фаєрвол pfsense запущено у віртуальній машині сервера [PROXMOX](https://www.proxmox.com/en/) у приватній підмережі 192.168.0.0/24.  

[![](/assets/images/blog/af988112b8ef172e-7d7955bd2bbe1661.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCYCwSWSrSsPTZRELa2lWnDyuLHbHwDF03KKVzMS8NgISnHTYE9ebyoVLWVBn6li6MqLFvmM96ixVtZlPGi1ifmG6ptqZCDLeC34DoY0TH78Wbx4tNJyecTQ4Rj3n4AowHZOQr5ACl0ci-/s1600/pfsense-proxmox.png)  
*Діаграма підключень*

  
Для реального блокування IP адрес (ipv4, ipv6), необхідно передати інформацію про заблоковані IP адреси на інший сервер з FreeBSD (192.168.0.1) додаючи адреси до локальної таблиці '*snort2c*' фаєрволу pf, і також зупиняючи усі з'єднання що існували з цими IP адресами.  
  
  

#### Сервер pfsense

Для синхронізації таблиці '*snort2c*' фаєрволу pf було використано простий скрипт сценарію sh для запуску на стороні pfsense.  
  
/root/scripts/pfdiff-core.sh  

```
#!/bin/sh
statefile=/var/run/pftable.hash
pftable='/sbin/pfctl -t snort2c -T show'
pftablehash='sha384'
delaytime=2
MIB_INIT=1
MIB_ADD=2
MIB_DEL=3
if [ -f  ${statefile} ]; then
 rm  ${statefile}
fi
touch ${statefile}

sendcommand()
{
 /usr/local/bin/snmpinform -v 2c -c snort2c 192.168.0.1 "" 1.3.6.1.5.7.5.1.$1 1.3.6.1.5.7.5.1.99 s  "$2"
 returncode=$?
 if [ $returncode -gt 0 ];then
  logger " ERROR of TRAP inform by SNMP "
 fi
}

#MAIN
#initial flush
sendcommand  ${MIB_INIT} 0

#loop
while true; do
 lasthash=$(/sbin/pfctl -t snort2c -T show|sha384)
 if [ "${lasthash}" != "${storedhash}" ];then
#  echo $lasthash
   /sbin/pfctl -t snort2c -T show | /usr/bin/diff  ${statefile} -  | while IFS=" " read f1 f2
   do
    if [ "$f1" == ">" ] ;then
      sendcommand ${MIB_ADD}  "$f2"
    elif [ "$f1" == "<" ] ;then
      sendcommand  ${MIB_DEL}  "$f2"
    fi
  done
  /sbin/pfctl -t snort2c -T show > ${statefile}
  storedhash=$(cat ${statefile}|sha384)
 fi
 sleep ${delaytime}
done
```

  
Для автоматичного запуску створено скрипт:  
/usr/local/etc/rc.d/pfdiff.sh  

```
#!/bin/sh

pidfile="/var/run/pfdiff.pid"

rc_start() {
 /root/scripts/pfdiff-core.sh &
 echo $! > ${pidfile}
}

rc_stop() {
        kill `cat  ${pidfile}`
}

case $1 in
        start)
                rc_start
                ;;
        stop)
                rc_stop
                ;;
        restart)
                rc_stop
                rc_start
                ;;
esac
```

  
Для /root/scripts/pfdiff-core.sh необхідно додатково встановити пакунок net-snmp.  

[![](/assets/images/blog/d52b99699466aa23-84fbd3664b392238.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3vNhGAEDnq6tw5TH4lKGz8Zhz5SqwaRv7huVSLVyO5imh-00uVdcwVFrbwfKHuIT1hia1MmN8CmpUNg64YXH8RYKxECMVyPpqPqvfJyb0ysxUEmMIT73_sxbJQZX6bq87gLPkYI5VKqAs/s1600/pf-packages-01.PNG)  
*Пакунки pfSense*

  
Скрипт аналізуючи таблицю '*snort2c*' фаєрволу pf заходить зміни і формує snmp trap на інший сервер з відповідними OID для різних команд INIT,ADD IP, DEL IP.  
Для передачі IP адреси формату ipv6, у snmp trap, використовується текстовий формат повідомлення 's'.   
Видаляються адреси з таблиці snort2 за часом існування у таблиці, за
розкладом cron, кожні 5 хв самим pfsrense за опцією у snort, наприклад 1 година: /sbin/pfctl -q -t snort2c -T expire 3600  

#### Сервер FreeBSD

На іншому сервер використовується фаєрвол pf, де налаштовані правила блокування за таблицею snort2c.  
Також встановлено snmptrapd з пакунку net-snmp. Файл конфігурації: /usr/local/etc/snmp/snmptrapd.conf
  

```
doNotLogTraps yes
authCommunity execute snort2c
traphandle .1.3.6.1.5.7.5.1.1 /usr/local/etc/snmp/pf_ip_init.sh
traphandle .1.3.6.1.5.7.5.1.2 /usr/local/etc/snmp/pf_ip_add.sh
traphandle .1.3.6.1.5.7.5.1.3 /usr/local/etc/snmp/pf_ip_del.sh
```

  
За подіями  traphandle виконуються відповідні скрипти: *pf\_ip\_init.sh*, *pf\_ip\_add.sh*, *pf\_ip\_del.sh* котрі використовують спільну бібліотеку logger.sh.  
  
/usr/local/etc/snmp/logger.sh
  

```
#!/bin/sh
 read host
 read ip
 SendIP=
 while read oid val
 do
   if [ "${oid}" == ".1.3.6.1.5.7.5.1.99" ];then
    SendIP="${val%\"}"
    SendIP="${SendIP#\"}"
    break
   fi
 done
```

  
/usr/local/etc/snmp/pf\_ip\_init.sh
  

```
#!/bin/sh

. /usr/local/etc/snmp/logger.sh

if [ -z "${SendIP}"];then
 logger "empty SendIP, exit"
 exit
fi
logger -p local3.info -t SNORT2C  "INIT SNORT2C IP : ${SendIP}"
/sbin/pfctl   -t snort2c -T flush
```

/usr/local/etc/snmp/pf\_ip\_add.sh
  

```
#!/bin/sh

. /usr/local/etc/snmp/logger.sh

if [ -z "${SendIP}"];then
 logger "empty SendIP, exit"
 exit
fi

/sbin/pfctl -t snort2c -T add ${SendIP}
/sbin/pfctl -k 0.0.0.0/0 -k ${SendIP}
/sbin/pfctl -k ${SendIP} -k 0.0.0.0/0

logger -p local3.info -t SNORT2C "ADD SNORT2C IP : ${SendIP}"
```

/usr/local/etc/snmp/pf\_ip\_del.sh
  

```
#!/bin/sh

. /usr/local/etc/snmp/logger.sh

if [ -z "${SendIP}"];then
 logger "empty SendIP, exit"
 exit
fi
/sbin/pfctl -t snort2c -T del ${SendIP}

logger  -p local3.info -t SNORT2C "DEL SNORT2C IP : ${SendIP}"
```

  
Наприклад за  traphandle  OID = .1.3.6.1.5.7.5.1.2 виконуватися скрипт *pf\_ip\_add.sh* котрий шукає об'єкт OID = .1.3.6.1.5.7.5.1.99 з IP адресою і додає новознайдену IP адресу до таблиці snort2c і також припиняє будь які встановленні сесії за цією IP адресою. По закінченню, скрипт, протоколює результат роботи у лог файл.  
  

[![](/assets/images/blog/57153bc2cc3b9934-5cc82570fd5ca1fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwMEkyIJr6lFuQm0v6hgns-vVg4GcL0_FbjEQ3iYafFo4-ZAOHMvLYMiUShbMXh0pDjmBobx2rdgtFoC7Ulvm9H1N000Kb_01rBCimDTOwQnpL7wc05_2sBryoyVWwebd4rz25Qw_fHKmN/s1600/pf-packages-02.PNG)  
*Результат роботи на сервері що блокує адреси.*

Була ідея використати openbgpd для синхронізації таблиць за статтею  [Using OpenBGPD to distribute pf table updates to your servers | Echothrust Solutions](https://www.echothrust.com/blogs/using-openbgpd-distribute-pf-table-updates-your-servers), але ця ідея не вирішувала задачу припинення усіх з'єднань.  
  
Зараз працює скрипт котрий у постійному циклі з затримкою у 2 секунди аналізує таблицю snort2c на стороні pfSense і передає команди у разі виявлення змін на сервер FreeBSD використовуючи протокол snmp і команду snmpinform.
