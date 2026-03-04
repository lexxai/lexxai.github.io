---
layout: post
title: "FreeBSD Sensors monitoring"
date: 2012-08-24 10:42:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/08/freebsd-sensors-monitoring.html
---

1. OLD AMD  

dmesg | grep CPU

CPU: AMD Athlon(tm) Processor (996.33-MHz 686-class CPU)

  
%cd /usr/ports/sysutils/k8temp && make install clean  
  

%k8temp -d

CPUID: Vendor: AuthenticAMD, 0x642: Model=04 Family=6+0 Stepping=2

k8temp: CPU lacks Advanced Power Management support

  
%cd /usr/ports/sysutils/mbmon/ && make install clean  
  

%mbmon -c1 -I

Temp.= 42.0, 46.0, 28.0; Rot.= 4856, 0, 0

Vcore = 1.76, 2.50; Volt. = 3.34, 4.97, 11.25, -12.18, -1.40

  
  
  
  
2.CPU AMD Athlon(tm) XP 2500+  
  

%dmesg | grep CPU

CPU: AMD Athlon(tm) XP 2500+ (1837.54-MHz 686-class CPU)

cpu0: <ACPI CPU> on acpi0

Correcting nForce2 C1 CPU disconnect hangs

CPU: AMD Athlon(tm) XP 2500+ (1837.54-MHz 686-class CPU)

cpu0: <ACPI CPU> on acpi0

Correcting nForce2 C1 CPU disconnect hangs

  
  
%kldload coretemp   

%sysctl -a | grep temper

hw.acpi.thermal.tz0.temperature: 29.5C

  
%cd /usr/ports/sysutils/k8temp && make install clean  
  

%k8temp -d

CPUID: Vendor: AuthenticAMD, 0x6a0: Model=0a Family=6+0 Stepping=0

Advanced Power Management=0x1

Temperature sensor: Yes

Frequency ID control: No

Voltage ID control: No

THERMTRIP support: No

HW Thermal control: No

SW Thermal control: No

100MHz multipliers: No

HW P-State control: No

TSC Invariant: No

  
  
3. Данні материнської плати зібранні за допомогою mbmon   
  
Данні материнської плат - температура, оберти вентиляторів, та данні напруги.   
  
cd /usr/ports/sysutils/mbmon/ && make install clean  
  
mbmon -d  
ioctl(smb0:open): No such file or directory  
SMBus[NVidia nForce2] found, but No HWM available on it!!  
Using ISA-IO access method!!  
\* Winbond Chip W83627HF/THF/THF-A found.  
  
По SMB інформацію не можемо тримати, тому використовую -I (ISA-IO access method).  
  
Спроба завантажити smb, нічогго не дала:   
  
%kldload smbus  
%kldload ichsmb  
%kldload smb  
  
  

%mbmon -c1 -I

Temp.= 208.0, 29.5, 32.5; Rot.= 0, 5273, 0

Vcore = 1.70, 1.54; Volt. = 3.41, 5.16, 12.22, -12.45, 3.60

  

%mbmon -r -I

TEMP0 : 208.0

TEMP1 : 29.5

TEMP2 : 31.0

FAN0 : 0

FAN1 : 5273

FAN2 : 0

VC0 : +1.71

VC1 : +1.54

V33 : +3.42

V50P : +5.16

V12P : +12.16

V12N : -12.45

V50N : +3.60

  
Якщо значення TEMP більше 120 то вважаю, такого значення не існує.  
  
4. Температура HDD, smartmontools   
  
%cd /usr/ports/sysutils/smartmontools && make install clean  
  
Створюю файл hddtemp.sh:  

#!/bin/sh

/usr/local/sbin/smartctl -a -s on $1 |grep " Temperature" | awk '{print $10}'

  
  
  
Дозвіл на виконання:  
  

%chmod +x hddtemp.sh

  
Виконуюємр hddtemp.sh з параметром імені необхідного накопичувача:  
  

%./hddtemp.sh /dev/ada0

29  
  
5. Підготовка даних для системи збору інформації та моніторингу CACTI  
  
Створюю файл mbmon.sh:  
  
#!/bin/sh  
RES=`/usr/local/bin/mbmon -c 1 -r -I |grep $1 -i |  sed -e "s/ \*: \*/:/g" | sed -e "s/+//g"`  
if [ $2 ]; then  
 RES="$RES TEMPHD1:`/usr/local/etc/snmpd/hddtemp.sh $2`";  
fi  
if [ $3 ]; then  
 RES="$RES TEMPHD2:`/usr/local/etc/snmpd/hddtemp.sh $3`";  
fi  
echo $RES  
  
  
Дозвіл на виконання:  
%chmod +x mbmon.sh  
  
  
Запит про температуру:   

./mbmon.sh temp /dev/ada0

TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

  

./mbmon.sh temp /dev/ada0 /dev/ada1

TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29 TEMPHD2:32

  
Запит про оберти вентиляторів:   

%./mbmon.sh fan

FAN0:0 FAN1:5273 FAN2:0

  
  
Запит про напругу:   

%./mbmon.sh v

VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

  
Виводяться значення напруги тільки позитивні, відкинувши знак.  
Для правильного збору значення напруги з урахуванням знаку, потрібно змістити значення додавши константу, наприклад 25. А потім, у системі моніторингу відняти цю константу.  
  
6. Передача збір та данних за допомогою SNMP  
  
%cd /usr/ports/net-mgmt/net-snmp && make install clean  
  
Створимо теку /usr/local/etc/snmpd  
Збережемо до неї створенні раніше файли mbmon.sh, hddtemp.sh.  
  
Створимо файл конфігурації snmpd.conf:  
  

rocommunity public 192.168.1.17

sysName Server-1

syslocation Datacenter, Row 31, Rack 1

syscontact UNIX Admin <root@some.net>

sh temp /bin/sh /usr/local/etc/snmpd/mbmon.sh temp /dev/ada0

sh fan /bin/sh /usr/local/etc/snmpd/mbmon.sh fan

sh voltage /bin/sh /usr/local/etc/snmpd/mbmon.sh v

includeAllDisks 1%

  
Дана конфігурація, обмежує підключення і дозволяє збирати інформацію тільки з адреси 192.168.1.17.  
  
Є можливість пере назначити назву сервера, та додати інформацію щодо розміщення.   
  
Розширюємо можливості snmpd даного сервера, додавши можливість запиту про данні моніторингу.   
  
Додаю до /etc/rc.conf інформацію щодо розміщення конфігураційного файлу:   
  

snmpd\_enable="YES"

snmpd\_flags="-a"

snmpd\_conffile="/usr/local/etc/snmpd/snmpd.conf"

  
Виконуємо запуск служби snmpd:  
  
%/usr/local/etc/rc.d/snmpd start  
  
Дивимося на результат:   
  
%tail -f /var/log/snmpd.log  
  
Тепер на сервері 192.168.1.17, можемо зробити запит до нашого серверу 192.168.1.1 "Server-1".  
  
Запит про температуру:  

%snmpwalk -On -v 1 -c public 192.168.1.1 .1.3.6.1.4.1.2021.8.1.101.1

.1.3.6.1.4.1.2021.8.1.101.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

  
Запит про оберти:  

%snmpwalk -On -v 1 -c public 192.168.1.1 .1.3.6.1.4.1.2021.8.1.101.2

.1.3.6.1.4.1.2021.8.1.101.2 = STRING: FAN0:0 FAN1:5273 FAN2:0

  
Запит про напругу:   

%snmpwalk -On -v 1 -c public 192.168.1.1 .1.3.6.1.4.1.2021.8.1.101.3

.1.3.6.1.4.1.2021.8.1.101.3 = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

  
  
Де параметри:  
-On - видавати не репрезентований вигляд результату;   
-v 1 - використовувати 1 версію snmp, для запиту;  
-c public - використовувати для запиту, ім'я community publiс;  
192.168.1.1 - ip адреса сервера у корого запитують інформацію.  
.1.3.6.1.4.1.2021.8.1.101.1 - OID, ідентифікатор для запиту необхідної інформації.  
  
OID розподілений на класи та группи.  
Так .1.3.6.1.4.1.2021.8.1 - це UCD-SNMP-MIB  
Якщо запитати у сервера 192.168.1.1 з репрезентованим виглядом результату, то отрмаємо  

%snmpwalk -v 1 -c public 192.168.1.1 .1.3.6.1.4.1.2021.8.1

UCD-SNMP-MIB::extIndex.1 = INTEGER: 1

UCD-SNMP-MIB::extIndex.2 = INTEGER: 2

UCD-SNMP-MIB::extIndex.3 = INTEGER: 3

UCD-SNMP-MIB::extNames.1 = STRING: temp

UCD-SNMP-MIB::extNames.2 = STRING: fan

UCD-SNMP-MIB::extNames.3 = STRING: voltage

UCD-SNMP-MIB::extCommand.1 = STRING: /bin/sh

UCD-SNMP-MIB::extCommand.2 = STRING: /bin/sh

UCD-SNMP-MIB::extCommand.3 = STRING: /bin/sh

UCD-SNMP-MIB::extResult.1 = INTEGER: 0

UCD-SNMP-MIB::extResult.2 = INTEGER: 0

UCD-SNMP-MIB::extResult.3 = INTEGER: 0

UCD-SNMP-MIB::extOutput.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

UCD-SNMP-MIB::extOutput.2 = STRING: FAN0:0 FAN1:5273 FAN2:0

UCD-SNMP-MIB::extOutput.3 = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

UCD-SNMP-MIB::extErrFix.1 = INTEGER: noError(0)

UCD-SNMP-MIB::extErrFix.2 = INTEGER: noError(0)

UCD-SNMP-MIB::extErrFix.3 = INTEGER: noError(0)

UCD-SNMP-MIB::extErrFixCmd.1 = STRING:

UCD-SNMP-MIB::extErrFixCmd.2 = STRING:

UCD-SNMP-MIB::extErrFixCmd.3 = STRING:

  
Це дерево OID є репрезентованим виглядом SNMP MIB скомпільованого модуля UCD-SNMP-MIB. Наглядним прикладом структури UCD-SNMP-MIB є [http://www.oidview.com/mibs/2021/UCD-SNMP-MIB.html](http://www.oidview.com/mibs/2021/UCD-SNMP-MIB.html%20)   
  
  
  
Наш запит про температуру є OID = "UCD-SNMP-MIB::extOutput.1"  

%snmpwalk -v 1 -c public 192.168.1.1 .1.3.6.1.4.1.2021.8.1.101.1

UCD-SNMP-MIB::extOutput.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

%snmpwalk -v 1 -c public 192.168.1.1 UCD-SNMP-MIB::extOutput.1

UCD-SNMP-MIB::extOutput.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

  
  
  
Наш запит про оберти є OID = "UCD-SNMP-MIB::extOutput.2"  

%snmpwalk -v 1 -c public 192.168.1.1 UCD-SNMP-MIB::extOutput.2

UCD-SNMP-MIB::extOutput.2 = STRING: FAN0:0 FAN1:5273 FAN2:0

  
  
ДОДАТОК, НЕВИКОРИСТАНИЙ У ДАНІЙ ПУБЛІКАЦІЇ:   
  
    
Також, ці данні  прсутні у OID = 1.3.6.1.4.1.8072.1.3.2 (NET-SNMP-EXTEND-MIB), структуровано він лписаний за цим посиланням: <http://www.oidview.com/mibs/8072/NET-SNMP-EXTEND-MIB.html>.  
  
  

%snmpwalk -v 1 -c public  192.168.1.1  1.3.6.1.4.1.8072.1.3.2

NET-SNMP-EXTEND-MIB::nsExtendNumEntries.0 = INTEGER: 3

NET-SNMP-EXTEND-MIB::nsExtendCommand."fan" = STRING: /bin/sh

NET-SNMP-EXTEND-MIB::nsExtendCommand."temp" = STRING: /bin/sh

NET-SNMP-EXTEND-MIB::nsExtendCommand."voltage" = STRING: /bin/sh

NET-SNMP-EXTEND-MIB::nsExtendArgs."fan" = STRING: /usr/local/etc/snmpd/mbmon.sh fan

NET-SNMP-EXTEND-MIB::nsExtendArgs."temp" = STRING: /usr/local/etc/snmpd/mbmon.sh temp /dev/ada0

NET-SNMP-EXTEND-MIB::nsExtendArgs."voltage" = STRING: /usr/local/etc/snmpd/mbmon.sh v

NET-SNMP-EXTEND-MIB::nsExtendInput."fan" = STRING:

NET-SNMP-EXTEND-MIB::nsExtendInput."temp" = STRING:

NET-SNMP-EXTEND-MIB::nsExtendInput."voltage" = STRING:

NET-SNMP-EXTEND-MIB::nsExtendCacheTime."fan" = INTEGER: 5

NET-SNMP-EXTEND-MIB::nsExtendCacheTime."temp" = INTEGER: 5

NET-SNMP-EXTEND-MIB::nsExtendCacheTime."voltage" = INTEGER: 5

NET-SNMP-EXTEND-MIB::nsExtendExecType."fan" = INTEGER: shell(2)

NET-SNMP-EXTEND-MIB::nsExtendExecType."temp" = INTEGER: shell(2)

NET-SNMP-EXTEND-MIB::nsExtendExecType."voltage" = INTEGER: shell(2)

NET-SNMP-EXTEND-MIB::nsExtendRunType."fan" = INTEGER: run-on-read(1)

NET-SNMP-EXTEND-MIB::nsExtendRunType."temp" = INTEGER: run-on-read(1)

NET-SNMP-EXTEND-MIB::nsExtendRunType."voltage" = INTEGER: run-on-read(1)

NET-SNMP-EXTEND-MIB::nsExtendStorage."fan" = INTEGER: permanent(4)

NET-SNMP-EXTEND-MIB::nsExtendStorage."temp" = INTEGER: permanent(4)

NET-SNMP-EXTEND-MIB::nsExtendStorage."voltage" = INTEGER: permanent(4)

NET-SNMP-EXTEND-MIB::nsExtendStatus."fan" = INTEGER: active(1)

NET-SNMP-EXTEND-MIB::nsExtendStatus."temp" = INTEGER: active(1)

NET-SNMP-EXTEND-MIB::nsExtendStatus."voltage" = INTEGER: active(1)

NET-SNMP-EXTEND-MIB::nsExtendOutput1Line."fan" = STRING: FAN0:0 FAN1:5273 FAN2:0

NET-SNMP-EXTEND-MIB::nsExtendOutput1Line."temp" = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

NET-SNMP-EXTEND-MIB::nsExtendOutput1Line."voltage" = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

NET-SNMP-EXTEND-MIB::nsExtendOutputFull."fan" = STRING: FAN0:0 FAN1:3835 FAN2:0

NET-SNMP-EXTEND-MIB::nsExtendOutputFull."temp" = STRING: TEMP0:208.0 TEMP1:30.5 TEMP2:35.0 TEMPHD1:44

NET-SNMP-EXTEND-MIB::nsExtendOutputFull."voltage" = STRING: VC0:1.63 VC1:1.47 V33:3.28 V50P:4.89 V12P:11.67 V12N:12.04 V50N:3.55

NET-SNMP-EXTEND-MIB::nsExtendOutNumLines."fan" = INTEGER: 1

NET-SNMP-EXTEND-MIB::nsExtendOutNumLines."temp" = INTEGER: 1

NET-SNMP-EXTEND-MIB::nsExtendOutNumLines."voltage" = INTEGER: 1

NET-SNMP-EXTEND-MIB::nsExtendResult."fan" = INTEGER: 0

NET-SNMP-EXTEND-MIB::nsExtendResult."temp" = INTEGER: 0

NET-SNMP-EXTEND-MIB::nsExtendResult."voltage" = INTEGER: 0

NET-SNMP-EXTEND-MIB::nsExtendOutLine."fan".1 = STRING: FAN0:0 FAN1:5273 FAN2:0

NET-SNMP-EXTEND-MIB::nsExtendOutLine."temp".1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

NET-SNMP-EXTEND-MIB::nsExtendOutLine."voltage".1 = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

  
Наші данні про моніторинг можна отримати з OID  1.3.6.1.4.1.8072.1.3.2.4.1.2 (NET-SNMP-EXTEND-MIB::nsExtendOutLine)  
  

%snmpwalk -v 1 -c public 192.168.1.1 1.3.6.1.4.1.8072.1.3.2.4.1.2

NET-SNMP-EXTEND-MIB::nsExtendOutLine."fan".1 = STRING: FAN1:5273 FAN2:0

NET-SNMP-EXTEND-MIB::nsExtendOutLine."temp".1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

NET-SNMP-EXTEND-MIB::nsExtendOutLine."voltage".1 = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

  

%snmpwalk -On -v 1 -c public 192.168.1.1 1.3.6.1.4.1.8072.1.3.2.4.1.2

.1.3.6.1.4.1.8072.1.3.2.4.1.2.3.102.97.110.1 = STRING: FAN1:5273 FAN2:0

.1.3.6.1.4.1.8072.1.3.2.4.1.2.4.116.101.109.112.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

.1.3.6.1.4.1.8072.1.3.2.4.1.2.7.118.111.108.116.97.103.101.1 = STRING: VC0:1.71 VC1:1.55 V33:3.42 V50P:5.16 V12P:12.16 V12N:12.45 V50N:3.60

Конкретно наша температура:  

%snmpwalk -On -v 1 -c public 192.168.1.1  .1.3.6.1.4.1.8072.1.3.2.4.1.2.3.102.97.110.1

.1.3.6.1.4.1.8072.1.3.2.4.1.2.4.116.101.109.112.1 = STRING: TEMP0:208.0 TEMP1:29,5 TEMP2:31.0 TEMPHD1:29

  
  
Використання NET-SNMP-EXTEND-MIB, має можливість працювати з даними, що триманні масивом, індексовані. Так наприклад, можна було створити  інший метод передачі даних про моніторинг. Використовуючи кожен параметр на окремому рядку, і звертаючись до цього параметру за індексом номера рядку NET-SNMP-EXTEND-MIB::nsExtendOutNumLines.  Але при цьому треба виконувати визначення двох масивів, один за назвою параметра, інший зі значенням параметра. Таким чином ми отримаємо у системі моніторингу на графіках автоматичні імена параметрів, за умови правильно сформованого файлу з параметрами XML запиту "Query".
