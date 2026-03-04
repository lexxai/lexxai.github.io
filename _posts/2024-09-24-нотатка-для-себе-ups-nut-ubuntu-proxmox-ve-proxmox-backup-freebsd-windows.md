---
layout: post
title: "Нотатка для себе. UPS, NUT. Ubuntu (Proxmox VE, Proxmox Backup), FreeBSD, Windows"
date: 2024-09-24 23:34:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2024/09/ups-nut-ubuntu-proxmox-ve-proxmox.html
---

UPS CyberPower CP 1500C - USB connection

В материнці підключати до портів USB що не є версією 3.0 і вище.

[![](/assets/images/blog/3f9f78c628f209b6-66cbdb1d4f8a68aa.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEicH3f0XvbfOilrO54HDEnaa7UZN6PcJlWJ3SY2llJTTaYWcYs3HMTQvZlWBpA2PznFo1zEK9-sKOAtu0BmJXg2YM-7ufE_LabbCgy5F77z2F0p24CC2l-5_xLtFsEH-Vl01r4Ju07OKUhq_zv9wt5bJZJGr4thfUDhrJEf-sb3E4hNVxHau-Bbt9FpX50x/s976/Screenshot%202024-09-25%20002700.png)  
*USB 3.0 - Error*

[![](/assets/images/blog/a5cea4b2006887d7-934ced96575277b6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3eCSrM8W12-E6WEXzpmzW8I89mbj5MBS-QPHHDOX1fKS_CCcQZgD29Elth1U-neI2P-lUkJTt0n-bOCD46veHoJbknzojvI-F8flWBPUqingnbhEy4WHoe-M8bre5EtOplGkOKMXB4_0dtQnRT1pQM3XogpVwHhkiNz2305aKBGlJx2hh-w8smroKX9Gy/s1328/Screenshot%202024-09-25%20002749.png)  
*USB 2.0 - OK*

#### Ubuntu Primary

```
# apt update
# apt install nut nut-server nut-client
```

```
# nut-scanner
Scanning USB bus.
No start IP, skipping NUT bus (old connect method)
Scanning NUT bus (avahi method).
Failed to create client: Daemon not running
[nutdev1]
        driver = "usbhid-ups"
        port = "auto"
        vendorid = "0764"
        productid = "0501"
        product = " CP 1500C"
        vendor = "CPS"
        bus = "004"
```

Права доступу до USB:

/etc/udev/rules.d/90-nut-cp.rules:

```
SUBSYSTEM=="usb", ATTR{idVendor}=="0764", ATTR{idProduct}=="0501", MODE="0666", GROUP="nut"
```

Застосувати зміни

```
# udevadm control --reload-rules
```

/etc/nut/nut.conf

```
MODE=netserver
```

* Вимикаю біп
* LOW Battery від UPS ігноруємо так якзмінити вбудованну змінну 10% не можна
  через upsrw, використувую власне первизанчеення знаяення battery.charge.low
  = 50.
* ups.delay.shutdown = 420 вимушено віикористовую так як не змінити вбудованну
  змінну ups.timer.shutdown: -60

/etc/nut/ups.conf:

```
[C1500]
  driver = usbhid-ups
  port = auto
  vendorid = "0764"
  productid = "0501"
  ignorelb
  override.ups.beeper.status = false
  override.battery.charge.warning = 75
  override.battery.charge.low = 50
  override.ups.delay.shutdown = 420
```

/etc/nut/upsd.conf:

```
LISTEN 0.0.0.0 3493
```

/etc/nut/upsd.users:

```
[admin]
 password = adminpassword
 actions = SET
 instcmds = ALL

[monuser]
 password = clientpassword
 upsmon primary
```

/etc/nut/upsmon.conf:

```
MONITOR c1500@localhost 1 monuser clientpassword primary
FINALDELAY 300
SHUTDOWNCMD "/sbin/shutdown -p now"
```

```
# systemctl enable nut-server
# systemctl start nut-server
# systemctl enable nut-monitor
# systemctl start nut-monitor
```

```
# upsc c1500@localhost
Init SSL without certificate database
battery.charge: 100
battery.charge.low: 50
battery.charge.warning: 75
battery.mfr.date: CPS
battery.runtime: 978
battery.runtime.low: 300
battery.type: PbAcid
battery.voltage: 13.8
battery.voltage.nominal: 12
device.mfr: CPS
device.model:  CP 1500C
device.type: ups
driver.flag.ignorelb: enabled
driver.name: usbhid-ups
driver.parameter.pollfreq: 30
driver.parameter.pollinterval: 2
driver.parameter.port: auto
driver.parameter.productid: 0501
driver.parameter.synchronous: auto
driver.parameter.vendorid: 0764
driver.version: 2.8.0
driver.version.data: CyberPower HID 0.6
driver.version.internal: 0.47
driver.version.usb: libusb-1.0.26 (API: 0x1000109)
input.transfer.high: 140
input.transfer.low: 90
input.voltage: 118.0
input.voltage.nominal: 120
output.voltage: 118.0
ups.beeper.status: false
ups.delay.shutdown: 420
ups.delay.start: 30
ups.load: 31
ups.mfr: CPS
ups.model:  CP 1500C
ups.productid: 0501
ups.realpower.nominal: 388
ups.status: OL
ups.test.result: Done and warning
ups.timer.shutdown: -60
ups.timer.start: 0
ups.vendorid: 0764
```

#### Ubuntu Secondary

```
# apt update
# apt install nut nut-client
```

/etc/nut/upsmon.conf:

```
MONITOR c1500@192.168.0.100 1 monuser clientpassword secondary
SHUTDOWNCMD "/sbin/shutdown -p now"
```

```
# upsc c1500@192.168.0.100
Init SSL without certificate database
battery.charge: 100
battery.charge.low: 50
battery.charge.warning: 75
battery.mfr.date: CPS
battery.runtime: 978
battery.runtime.low: 300
battery.type: PbAcid
battery.voltage: 13.8
battery.voltage.nominal: 12
device.mfr: CPS
device.model:  CP 1500C
device.type: ups
driver.flag.ignorelb: enabled
driver.name: usbhid-ups
driver.parameter.pollfreq: 30
driver.parameter.pollinterval: 2
driver.parameter.port: auto
driver.parameter.productid: 0501
driver.parameter.synchronous: auto
driver.parameter.vendorid: 0764
driver.version: 2.8.0
driver.version.data: CyberPower HID 0.6
driver.version.internal: 0.47
driver.version.usb: libusb-1.0.26 (API: 0x1000109)
input.transfer.high: 140
input.transfer.low: 90
input.voltage: 118.0
input.voltage.nominal: 120
output.voltage: 118.0
ups.beeper.status: false
ups.delay.shutdown: 420
ups.delay.start: 30
ups.load: 31
ups.mfr: CPS
ups.model:  CP 1500C
ups.productid: 0501
ups.realpower.nominal: 388
ups.status: OL
ups.test.result: Done and warning
ups.timer.shutdown: -60
ups.timer.start: 0
ups.vendorid: 0764
```

```
# systemctl enable nut-monitor
# systemctl start nut-monitor
```

#### FreeBSD

```
# pkg install nut
```

Перевіряємо чи є у /usr/local/etc/devd/nut-usb.conf:

```
#  Dynex DX-800U?, CP1200AVR/BC1200D, CP825AVR-G, CP1000AVRLCD, CP1000PFCLCD, CP1500C, CP550HG, etc.  - usbhid-ups
notify 100 {
        match "system"          "USB";
        match "subsystem"       "DEVICE";
        match "type"            "ATTACH";
        match "vendor"          "0x0764";
        match "product"         "0x0501";
        action "chgrp nut /dev/$cdev; chmod g+rw /dev/$cdev";
};
```

Перезапуск служби devd

```
service devd restart
```

Мають бути зміненні права доступу до USB

```
# dmesg | grep 1500C
ugen1.2: <CPS CP 1500C> at usbus1 
```

```
# ls -l /dev/usb
total 0
crw-------  1 root  operator  0x30 Sep 24 06:25 0.1.0
crw-------  1 root  operator  0x69 Sep 24 06:25 0.1.1
crw-------  1 root  operator  0x32 Sep 24 06:25 1.1.0
crw-------  1 root  operator  0x6b Sep 24 06:25 1.1.1
crw-rw----  1 root  nut       0x75 Sep 24 06:25 1.2.0
crw-------  1 root  operator  0x7c Sep 24 06:25 1.2.1
crw-------  1 root  operator  0x34 Sep 24 06:25 2.1.0
crw-------  1 root  operator  0x68 Sep 24 06:25 2.1.1
```

Далі все як зазавичай,  конфігурація у теці /usr/local/etc/nut.

rc.conf:

```
nut_enable="YES"
nut_upsmon_enable="YES"
nut_upsshut="YES"
```

окрім того що у файлі /usr/local/etc/nut/upsmon.conf  замість
*POWERDOWNFLAG /etc/killpower* використав
*POWERDOWNFLAG /var/db/nut/killpower* щоб були права стовення файлу у
корисувача nut.

```
# service nut start
# service nut_upsmon start
```

```
# upsc c1500@localhost
battery.charge: 100
battery.charge.low: 50
battery.charge.warning: 75
battery.mfr.date: CPS
battery.runtime: 2902
battery.runtime.low: 300
battery.type: PbAcid
battery.voltage: 13.4
battery.voltage.nominal: 12
device.mfr: CPS
device.model:  CP 1500C
device.type: ups
driver.debug: 0
driver.flag.allow_killpower: 0
driver.flag.ignorelb: enabled
driver.name: usbhid-ups
driver.parameter.override.battery.charge.low: 50
driver.parameter.override.battery.charge.warning: 75
driver.parameter.override.ups.beeper.status: false
driver.parameter.override.ups.delay.shutdown: 420
driver.parameter.pollfreq: 30
driver.parameter.pollinterval: 2
driver.parameter.port: auto
driver.parameter.productid: 0501
driver.parameter.synchronous: auto
driver.parameter.vendorid: 0764
driver.state: quiet
driver.version: 2.8.2
driver.version.data: CyberPower HID 0.80
driver.version.internal: 0.53
driver.version.usb: libusb-1.0.0 (API: 0x1000102)
input.transfer.high: 140
input.transfer.low: 90
input.voltage: 117.0
input.voltage.nominal: 120
output.voltage: 118.0
ups.beeper.status: false
ups.delay.shutdown: 420
ups.delay.start: 30
ups.load: 8
ups.mfr: CPS
ups.model:  CP 1500C
ups.productid: 0501
ups.realpower.nominal: 388
ups.status: OL 
ups.test.result: Done and passed
ups.timer.shutdown: -60
ups.timer.start: 0
ups.vendorid: 0764
```

#### Windows NUT Client

[![](/assets/images/blog/8fadf0eedc9c1c5f-24bb7074305aa372.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6Uc5-2k9pRNNvnr8OGLDH2k2haj0MxY4L15ZCW_K6dBeWEXD1sXLjFTAA4_AchM18G_d8eOUsuTtsgN09AJD_mx-iYZyIy0H6XRbOYNkW0r3S98S5OjHoh_5H0O45qwf3uQT93eE8G-ByL8Vp4Z04fzImOpHxQclVlXIRUmloPyfPXQ-BvalJFeHkFQjE/s705/ScreenHunter%20338.jpg)  
*Windows NUT Client*
