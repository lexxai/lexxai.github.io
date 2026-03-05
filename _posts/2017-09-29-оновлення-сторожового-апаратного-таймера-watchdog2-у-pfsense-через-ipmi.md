---
layout: post
title: "Оновлення сторожового апаратного таймера Watchdog2 у pfSense через IPMI"
date: 2017-09-29 21:54:00 +0000
tags: ["IPMI", "ipmitool", "pfSense", "watchdog", "Watchdog2", "watchdogd"]
blogger_orig_link: https://lexxai.blogspot.com/2017/09/watchdog2-pfsense-ipmi.html
---

З'ясував що версії [pfSense](http://pfsense.org/) версій 2.3.4, 2.4.0, не обробляє оновлення апаратного сторожового таймера материнської плати з Watchdog2.  
Виправляв ось так:  
Додати підтримку IPMI у ядро:  
Додавання рядка ipmi\_load="YES" до файлу /boot/loader.conf.local   

[![](/assets/images/blog/b8b870207e1e72d0-3cfec9400b3fe742.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0N8nBAGhAMWwVKfmu6CLbJV8anJK9iqxBf7wmbHoHmOeKxnJLeSLwxM3_tqnpVB-r-I1lat25nZjpDC8i8HlruCAEeVbS_5MWzv3fpi9KtQXVCUr-x4tFrjnU1cEmvyVrGfSqbUjyRdLC/s1600/pfsence-kload-ipmi.PNG)  
*/boot/loader.conf.local - ipmi\_load="YES"*

  
Потрібно перезавантажити систему і перевірити чи є підтримка IPMI з терміналу:  

```
cat /var/log/dmesg.boot | grep ipmi
```

  

[![](/assets/images/blog/5735eef708b4a617-f0f4c013db1593d7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgQuEbaORxW2C5XhYhkERnmpbgS7a6nwpF0FKzM-TDIEZumhUBBpWfLBi3vBzuKhGrf7zK-nfnvTeuM1lhMQlWZ9MJn2iTfFlGXAJrskHJWM5UDOuXuYrhkiikGidGWkNV7zaqQ5mbsN_6/s1600/pfsence-dmesg.PNG)  
*/var/log/dmesg.boot*

  
З терміналу читаємо значення сенсорів:  

```
ipmitool sensor
```

  

[![](/assets/images/blog/8a4a06aabc2cab7b-06b96f2a98615111.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEim_b0xsNclj6tg1aMWIH-V1zgx508DToJlonbWqH2PTJQxdHlCSvtgf-0RglOPOGsr5evnXv8w4n7-DKX8-JvjNNb6qyxC_sAo9MoWL_0FjPkeaV9jjy20NOaE4ENJNCxVXeA17zQNJ1K8/s1600/pfsence-ipmitool.PNG)  
*ipmitool sensor, перевірка наявності Watchdog2*

  
Надалі необхідно оновити значення сенсора, це можна зробити за допомоги команди watchdog  

```
$watchdog -d -t 300
shifted 549755813888
seconds_to_pow2ns: seconds: 300, ns 300000000000, power 39
Timeout for -t is 2^39 nanoseconds (in: 300 sec -> out: 549 sec 755813888 ns -> 549757 ticks)
Timeout is 2^39 nanoseconds
```

  
І повторювати її періодично, наприклад правилами планувальника cron:  

[![](/assets/images/blog/a480eec4c6fcc998-ed7669f2807cc816.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0qIB_h8GlUmQBKBy2_nJAkBBgSbcCiqG30xN-5Ssc25T_B4BrV7xaidho6gCYfynrjQkpg05Us_hkzea6GF_4Ii1EGir7Xjd-a6RgAm8Gtjw34H63mNxQLyNxxFW05IMte8c_Qm9zucNm/s1600/pfsence-cron-watchdog.PNG)  
*Періодичне оновлення значення сенсора Watchdog2*

Або можна запустити у фоновому режимі програму watchdogd.  

```
watchdogd  -s 60 -t 300
```

  
Також можна скинути значення таймера виконавши  команду ipmitool:  

```
$ipmitool mc watchdog reset
IPMI Watchdog Timer Reset -  countdown restarted!
```

  
Або визначити поточне значення таймера:  

```
$ipmitool mc watchdog get
Watchdog Timer Use:     SMS/OS (0x44)
Watchdog Timer Is:      Started/Running
Watchdog Timer Actions: Hard Reset (0x01)
Pre-timeout interval:   0 seconds
Timer Expiration Flags: 0x08
Initial Countdown:      549 sec
Present Countdown:      545 sec
```

  
Або вимкнути таймер повністю:  

```
$ipmitool mc watchdog off
Watchdog Timer Shutoff successful -- timer stopped
```

  
Тоді можна змінити cron на ipmitool mc watchdog reset:  

[![](/assets/images/blog/cd319c67d5a47346-29f43fa92a05fc19.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjI9ZsnuxqoMaPJl15dkZSp9FNBRpKNwI815LqVGIsdkv4CNlmGk99hK8k34ZB1g8E6BNNr-f5wz-dWvVl7xEbl6DoOQJ5JrBAL3WAvbmbCO7sMfRRCbCVECsr-C7L4gLp2d9ToIRdlzia0/s1600/pfsence-cron-ipmitool.PNG)  
*ipmitool mc watchdog reset*

  
  
Використано материнську плату Asus P10S-C/4L з модулем ASMB8-iKVM.  

[![](/assets/images/blog/d4faba689b593a9e-343771aa1d118da3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQEErEXuJyyj6Xn4at_C6aR0uzYX9efjT__OrV7OE1DrHA8eIVq3Hy2tA7127Hw4X4cAmGs9r8GpF1gU3TbAlWDzvImLTaNmO2Hn0x1kYw-AXIf6h2aF6Z5aRPFqRTi-Z7lxKsk18Ke0Yk/s1600/watchdog-bmc.PNG)  
*Налаштування Watchdog timer в BIOS через віддалене підключення iKVM.*
