---
layout: post
title: "Нотатка: IPMI Watchdog timer, скидання та встановлення через ipmitool"
date: 2023-04-04 20:05:00 +0000
tags: ["bmc", "hp", "HP ProLiant", "iLO 4", "IPMI", "ipmitool", "linux", "server", "ubuntu", "watchdog"]
blogger_orig_link: https://lexxai.blogspot.com/2023/04/ipmi-watchdog-timer-ipmitool.html
---

Сучасні серверні материнські плати мають можливість контролювати свій "живий" стан через спеціальний сторожовий таймер (Watchdog timer).

[![](/assets/images/blog/abfc40412cec31bd-b9db6cfb564b64fd.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjE6-xDKavoQUWE7Y3M7AFKgfVs2ya4nIwUDHFUgWFmJ3jyOEzceUjd1uvap-6c7kgI6E_oJUlSkxsCNuHCRuXO0INkFJdCKMRcuoKYc35pHAEXgKByjqHRToVOx8O4FVsR-Rvm72uQU8VCy2grpnqqc-RsTM-tsQP7okSi4jp7OINphyfLq0KfhMPtJw/s557/watchdog.jpg)  
*Налаштування Watchdog Timer у BIOS.*

[![](/assets/images/blog/8a8b68a19cc3c693-a33e299f9b068c4e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoos8dvafa-bf_kqbz_qyASc6PGxszqRCMg3KbrSDvxYOSHhggKloqWjxF4zVxJiWiA9ztBTVAd8MwjI63AslQiqwfrf6eZFLnOMOxor8VSJnQUNSi3BuqjKnvZdBeh-4NdlOF9s3KDgJ-jqkkRUA7p93GKvNKa8BUTVMGgRKdRuZzzXLCXlj4YbuIAw/s905/Screenshot%202023-04-04%20180444.png)  
*WatchDog Timer HP server*

Принцип його простий - запускається таймер з певним початковим значенням, на рівні BIOS спеціальної материнської плати (BMC), і  якщо значення таймеру досягне значення 0 сек., тоді виконається певна дія.   
Ці  дії можуть бути: перезавантажити материнську плату - reset, вимкнути живлення - power off,  або вимкнути та ввімкнути живлення послідовно - power cycle.

#### Налаштування

Є старенький сервер HP ProLiant DL380e Gen8 з iLO 4 (HPE Integrated Lights-Out 4).

[![](/assets/images/blog/872d721f2b37a046-1f848fe3a9df6cfd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiS6zXD9RbRdABmYGEftqB5za1B4qqZBl0n5P0OwPE7i4gRobn2_qYCDVP7eZ_MZj5Oq8KYsn2Rg4TjnLbwC5rwNwVsf7INv3No02lyRnsDDzTcQCzETd1kqwuOGTJSntFygKkNlAU9e2Ckuy1xQmSRw_Jv5Hn_IBGtx8IBO04GZEOaNBHLqTE5pbFIsg/s1483/Screenshot%202023-04-04%20155501.png)  
*iLO 4 ProLiant HP server*

На сервері встановлено систему 'Proxmox'. Тому я можу виконувати в консолі unix команди, та встановлювати пакунки.

[Встановлюю: ipmitool за допомогою apt](https://advantech-ncg.zendesk.com/hc/en-us/articles/360033313052-How-to-Install-IPMItool).

Тепер можу прочитати поточні значення сторожового таймера:

```
# ipmitool mc watchdog get
Watchdog Timer Use:     SMS/OS (0x04)
Watchdog Timer Is:      Stopped
Watchdog Timer Actions: No action (0x00)
Pre-timeout interval:   0 seconds
Timer Expiration Flags: 0x00
Initial Countdown:      300 sec
Present Countdown:      0 sec
```

Щоб встановити дію та нові значення для таймера з консолі, можна скористатися публікацією: [How to use ipmitool command to set BMC watchdog timer ?](https://advantech-ncg.zendesk.com/hc/en-us/articles/360028285872-How-to-use-ipmitool-command-to-set-BMC-watchdog-timer-)

```
# ipmitool raw 0x06 0x24 0x04 0x01 0x00 0x00 0x70 0x17
Watchdog Timer Use:     SMS/OS (0x04)
Watchdog Timer Is:      Stopped
Watchdog Timer Actions: Hard Reset (0x01)
Pre-timeout interval:   0 seconds
Timer Expiration Flags: 0x00
Initial Countdown:      600 sec
Present Countdown:      0 sec
```

Де # ipmitool raw 0x06 0x24 0xWW 0xXX 0x00 0x00 0xYY 0xZZ  
0xXX: WDT action mode

[![](/assets/images/blog/3e2b17ed1dda993b-0062520e07a025c9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjv_xgjgfvOlOnyGBCFsRKuWrT4G_2egYb2Zz1OyTmA6d9kbnVNsm45oHbHeyZotTxkA_bxOj1uozyB-5iHD8_DEzsQi71acc9lf6qoB5QjMId3z2XsGV7K3c0uEeoWTaR5jcZcAVWtsKllO9k5dagTRIdzSZxHKlqYOxlHNtSPE81Ib_VJQYv3_CgTtA/s622/mceclip1.png)  
*WDT action*

0xYY 0xZZ: Встановити початкові значення для сторожового таймера BMC

[![](/assets/images/blog/2bb0b71f4c8c81e6-1dd8a6e0428ea7f8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwls5XYvXF70VR5EHBPCvnEqoURjcPeopOZL5vWUUPo3cfMa_35MBel-TJWt5PvZvUTaMzlzKg0fTAMxBsJdTkkHYJzCG8gOoN_bYQo9-jEkg4sLSBzc3fQCRMROB_Dx3Tm7cM06DdoFtR88t4ATrpwsPkyBaL-6lTBDqHLiT19QcRB26l8WhMYjkblQ/s617/mceclip2.png)  
*WDT Timer*

Timer проміжок від 0.1(0X0001) до 6553.5(0XFFFF) секунд, де одиниця виміру - 0.1 секунди.   
600 sec: 6000 DEC (0.1 sec) = 1770 HEX = 0x70 0x17

#### Приклад роботи

Таким чином команда, налаштує таймер на 10 хвилин, і з дією перезапустити сервер.

```
ipmitool raw 0x06 0x24 0x04 0x01 0x00 0x00 0x70 0x17
```

Для того щоб показати що сервер працює за допомогою cron ми раз на хвилину запускаємо команду по скиданню таймера до початкових значень та перезапуску.

```
# ipmitool mc watchdog reset  
# ipmitool mc watchdog get  
Watchdog Timer Use:     SMS/OS (0x44)  
Watchdog Timer Is:      Started/Running  
Watchdog Timer Actions: Hard Reset (0x01)  
Pre-timeout interval:   0 seconds  
Timer Expiration Flags: 0x00  
Initial Countdown:      600 sec  
Present Countdown:      592 sec
```

Таким чином бачимо що таймер запущений і залишилось 592 секунди до перезавантаження сервера, якщо таймер не скинути.
