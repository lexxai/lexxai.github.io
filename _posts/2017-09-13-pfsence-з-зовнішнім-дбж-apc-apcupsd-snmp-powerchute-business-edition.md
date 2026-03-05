---
layout: post
title: "pfSence з зовнішнім ДБЖ APC, APCUPSD, SNMP, PowerChute Business Edition"
date: 2017-09-13 22:15:00 +0000
tags: ["apc", "Hyper-V", "pfsence", "SNMP", "ups"]
blogger_orig_link: https://lexxai.blogspot.com/2017/09/pfsence-apc-apcupsd-snmp-powerchute.html
---

Використовується спільне [ДБЖ](https://uk.wikipedia.org/wiki/%D0%94%D0%B6%D0%B5%D1%80%D0%B5%D0%BB%D0%BE_%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D1%80%D0%B5%D0%B1%D1%96%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE_%D0%B6%D0%B8%D0%B2%D0%BB%D0%B5%D0%BD%D0%BD%D1%8F) фірми APC by Schneider Electric, модель [Smart-UPS 750](http://www.apc.com/shop/ua/uk/products/-APC-Smart-UPS-750-230-2U/P-SMT750RMI2U?isCurrentSite=true), між двома серверами.  

[![](/assets/images/blog/3f82ab57b1f916a8-41f8b4e3bc529efc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-2Yged1hBXmI5cf4o2FsbMDTcfy8IKU-g0ofE9d2iCgG7cIHMJ-XmibsV40qJYoJJ5dUgMi2s0GEPD_mqBpZb54B8LsWof4kVg4FAvjh1p0447P5bI6iVMo6p2Xh4Cq7iSLIc_k1LLsjg/s1600/pfsence-apcpower-750.PNG)  
*APC Smart-UPS 750VA LCD RM 2U (SMT750RMI2U)*

Головний сервер це гіпервізор [Microsoft  HV-Server 2016](https://www.microsoft.com/ru-ru/evalcenter/evaluate-hyper-v-server-2016), підлеглий це сервер маршрутизатор з системою [pfSence](https://www.pfsense.org/).  
Для керуванням [ДБЖ](https://uk.wikipedia.org/wiki/%D0%94%D0%B6%D0%B5%D1%80%D0%B5%D0%BB%D0%BE_%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D1%80%D0%B5%D0%B1%D1%96%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE_%D0%B6%D0%B8%D0%B2%D0%BB%D0%B5%D0%BD%D0%BD%D1%8F) встановлено програму [PowerChute Business Edition](http://www.apc.com/shop/ua/uk/categories/power/ups/ups-management/powerchute-business-edition/_/N-o29ysx) (9.2.0.604), для керування підлеглим сервером використовується протокол [SNMP](https://uk.wikipedia.org/wiki/SNMP).  
  
Віддалено на сервері  Microsoft  HV-Server 2016 через PowerShell  встановлюються компонент SNMP. Через віддалене керування з іншого комп'ютера налаштовується служба  SNMP Service, властивості, безпека (Security),   додаються Comunity public, private, а також адреса з яких відкривається доступ.  

[![](/assets/images/blog/022f8a0576c5c78f-93c5e7d3c5e9b95f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzfRvIQKv9GveFIaZhq38SklwyZUROgff9BKRyzdpVfYhrHdsYes9OqZzEkPcFmrBborEZgG774QI4FUqoxZDRwkelhfZAa1wfEjfJeb-zbh9Ze-OLnli2BD819KCLXYIgrWuPHCeFdlHS/s1600/pfsence-apcpower-snmp.PNG)  
*Налаштування SNMP Service*

Надалі в налаштуваннях PowerChute Business Edition через веб інтерфейс https://hv.local:6547/snmpsettings, налаштовуємо роботу через SNMP.  

[![](/assets/images/blog/3a0f8ed8a8a2cf0e-dd494f8cb0a81a76.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhg0cXsIMtTHAbUm6KebseTVbpwoVHYC729515kB4PiqMAn3bwYaV8W7h_4eHVJlf4XK-A5ukIw8jQqLAMN0HljZR2-nqLqqX1rbTw_EAF7m1DLY7pluPhAGxviwhdkeNuDuRPU09oikNr_/s1600/pfsence-apcpower-snmp-03.PNG)

[![](/assets/images/blog/fb439eb05e6bb642-cb30dbcf6b3ec079.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhl08rBQ1ErIwSFxnpz8P6lyxZkzWmP1K0yP9UsfXgC5N2LzX2pfZzK19ZFA7G_W1MTRara96WugicOi6CjzbifKRzj1mvpR3aXozllqt4YGEgZIiyQSZiS8Yr9aVrVU1tDppOBbW95DM1e/s1600/pfsence-apcpower-snmp-04.PNG)

Перезапускаємо службу SNMP Service.  
Надалі встановлюємо компонент Apcupsd, до сервера з pfSence, та налаштовуємо.  

[![](/assets/images/blog/8a97d7bb9cc0fb7b-d9dafbe7bb8936da.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixbAAuAQ30tIhXvVDLueMHnJQtr_ZaV5IxSW2Yd9Ca5qmpgvI3iR4D9RHWJYjo2OgIxGgO_xQjLJLxO07Syki0PlD61qDQ_3qDRZgAc9EdZYLrUn8X1URxPwpAcOR4Z-MlbiEskX5e-xzX/s1600/pfsence-apcpower-snmp-01.PNG)  
*pfSence, Apcupsd, General*

  

```
UPS Cable - ether
UPS Type  - snmp
Device    - hv.local:161:APC:public 
NIS IP    - 127.0.0.1
```

  
Зберігаємо, і тепер у статусі бачимо інформацію про ДБЖ.  

[![](/assets/images/blog/7afa2fb1585372e5-9b67e1e93ba8e5df.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXPNnviNR8OZ1bGBlnWB3Z8c1qV_E4ZWhiX55RRH__x902WT5WaC-Zb7DVYNL9t0zH2ROTRS4imObGRa3WrBRAdT3l_Q5XWIqIS9RXprG95bWLcuXQhr9XhV3MqLaqApTw2HsEz9T7rV-2/s1600/pfsence-apcpower-snmp-02.PNG)  
*pfSence, Apcupsd,Status*

  
У додаток, можна налаштувати  SNMP Trap.  

[![](/assets/images/blog/50d95f6e52174247-918b5d2cf32a0af4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilCRuOGRACjg-7hiIM7EdcI3EMooBl_9bCFhol6hOKYASg5gDT1bN-XWa-Bj1954MorpoGjK21ESTdkNTw15GGovD5Wz5-5pHMgk7o7kW7Sg28VHkUvXd6sXJ-D-_pYFHYTZ5fr_8BqiG2/s1600/pfsence-apcpower-snmp-05.PNG)  
*SNMP Trap.*

  
Так як починаючи з apcupsd-3.11.14 введено підтримку [SNMP trap catching](http://www.apcupsd.com/manual/manual.html#snmp-trap-catching). І тепер майстер сервер може першим ініціювати передачу даних SNMP до підлеглого. Ось приклад переданих даних при подіях у ДБЖ:   

```
Enterprise Specific Trap (.8) Uptime: 0:00:20.82, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Communication Established"
Enterprise Specific Trap (.10) Uptime: 0:01:03.88, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Self Test Passed"
Enterprise Specific Trap (.10) Uptime: 0:03:29.20, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Self Test Passed"
Enterprise Specific Trap (.10) Uptime: 0:04:10.79, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Self Test Passed"
Enterprise Specific Trap (.6) Uptime: 2 days, 6:42:24.58, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost Active"
Enterprise Specific Trap (.34) Uptime: 2 days, 6:42:36.76, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost No Longer Active
Enterprise Specific Trap (.5) Uptime: 3 days, 6:42:45.75, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Power Failed"
Enterprise Specific Trap (.9) Uptime: 3 days, 6:42:46.79, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "Power Restored"
Enterprise Specific Trap (.6) Uptime: 3 days, 6:42:50.90, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost Active"
Enterprise Specific Trap (.34) Uptime: 3 days, 6:43:12.20, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost No Longer Active
Enterprise Specific Trap (.6) Uptime: 3 days, 8:04:59.59, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost Active"
Enterprise Specific Trap (.34) Uptime: 3 days, 8:18:08.84, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost No Longer Active
Enterprise Specific Trap (.6) Uptime: 3 days, 8:33:27.19, SNMPv2-SMI::enterprises.318.2.3.3.0 = STRING: "AVR Boost Active"
```

  
До уваги, при цьому pfSence починає прослухувати UDP порт 162 (SNMP trap), на всіх інтерфесах:  

```
netstat -na | grep 162
udp4       0      0 *.162                  *.*
```

```
sockstat | grep apcupsd
root     apcupsd    77968 4  tcp4   127.0.0.1:3551        *:*
root     apcupsd    77968 5  dgram  -> /var/run/logpriv
root     apcupsd    77968 7  udp4   *:8929                *:*
root     apcupsd    77968 8  udp4   *:162                 *:*
 
```

Тому потрібно додати правило що дозволить прийняти UDP порт 162, на Вашому інтерфейсі, наприклад так:  

[![](/assets/images/blog/f7705bbf0501575e-233f652ee8eafa8a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigrlo8TGz5gf7-bLscdRoasBZpr2j6pRDC8SuRgBzQp5ucJ5blvWP6TpYBQRGG0YC8oWMnmdvSeWCcg_0ovUlTx7cvptYd24efByfe-7szMedPNj3EwBDWvZOwBzSK75arWGA56b10zE1b/s1600/pfsence-apcpower-snmp-06.PNG)  
*Додавання правила на дозвіл UDP порт 162.*

Додатково перевірив роботу з програмою  NUT через драйвер ups-snmp:  
  

[![](/assets/images/blog/84d98549760802e7-4cd455c5d34d8e86.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhD_Le-7PaLGEZIoah34kjuv9arVpiVXRRUhSyWCaMrqWfhCYEvmdG4tsoKW697yi6BghvKQMEZBb7qW9SVfuEWizsXUHReOzUkOKIfSd34pDE6lvLpsPinc2np2-4oCXVXCNMiiMwMvsr7/s1600/pfsence-nut-apc-sett.PNG)  
*Налаштування NUT*

[![](/assets/images/blog/8ff0bb8458d182c0-05e4e3e400c759e3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIXSN2egznXJlPe8aXhFISxTURiNcoJRnnb-A4B4ntrzw7qDvI30oFsExCiPPmSuYnIp2ELR6wrZNZrchPqUSQhAYUAvctnGLPORv8GKDgFTxsTBQwnLwOiYLXdr-4xUjid4BHKt25YDlN/s1600/pfsence-nut-apc.PNG)  
*Стан і статус*
