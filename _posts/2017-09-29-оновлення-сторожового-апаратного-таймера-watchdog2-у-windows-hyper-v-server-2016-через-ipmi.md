---
layout: post
title: "Оновлення сторожового апаратного таймера Watchdog2 у Windows Hyper-V Server 2016 через IPMI"
date: 2017-09-29 23:49:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/09/watchdog2-windows-hyper-v-server-2016.html
---

Оновлення сторожового апаратного таймера  Windows Hyper-V Server 2016 не реалізовано базово.  
Тому потрібне додаткове програмне забезпечення на кшталт "Enterprise Managment".  
Після [реалізації під FreeBSD (pfSense)](https://lexxai.blogspot.com/2017/09/watchdog2-pfsense-ipmi.html)  вирішив зробити те саме і у Windows Hyper-V Server 2016.  
Так як сервер не має графічного інтерфейсу, все [робиться віддалено через PowerShell](https://www.howtogeek.com/117192/how-to-run-powershell-commands-on-remote-computers/).  

```
Enter-PSSession -ComputerName COMPUTER 
```

З проекту [http://ipmiutil.sourceforge.net](http://ipmiutil.sourceforge.net/), завантажую [ipmiutil-3.0.7-win64.zip,](http://ipmiutil.sourceforge.net/FILES/ipmiutil-3.0.7-win64.zip)  
розпаковую та переписую до теки що знаходиться на сервері.  
  
Перевіримо статус:  

```
[hv]: PS С:\install\ipmiutil-3.0.7-win64> .\ipmiutil.exe wdt
ipmiutil ver 3.07
iwdt ver 3.07
-- BMC version 2.01, IPMI version 2.0
wdt data: 43 00 00 08 70 17 d7 16
Watchdog timer is started for use with OS Load. Logging
               pretimeout is 0 seconds, pre-action is None
               timeout is 600 seconds, counter is 584 seconds
               action is No action

ipmiutil wdt, completed successfully
```

  
Для скидання таймера  

```
[hv]: PS С:\install\ipmiutil-3.0.7-win64> .\ipmiutil.exe wdt -r
ipmiutil ver 3.07
iwdt ver 3.07
-- BMC version 2.01, IPMI version 2.0
wdt data: 43 00 00 08 70 17 ca 13
Watchdog timer is started for use with OS Load. Logging
               pretimeout is 0 seconds, pre-action is None
               timeout is 600 seconds, counter is 506 seconds
               action is No action
Resetting watchdog timer ...
reset_wdt: ret = 0
wdt data: 43 00 00 08 70 17 70 17
Watchdog timer is started for use with OS Load. Logging
               pretimeout is 0 seconds, pre-action is None
               timeout is 600 seconds, counter is 600 seconds
               action is No action

ipmiutil wdt, completed successfully
```

  
Для періодичного запуску створюю завдання:  

```
SCHTASKS /create /sc MINUTE /MO 1 /TR "С:\install\ipmiutil-3.0.7-win64\ipmiutil.exe wdt -r" /TN "WDT reset"
```
