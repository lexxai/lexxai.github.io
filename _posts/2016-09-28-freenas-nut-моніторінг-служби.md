---
layout: post
title: "FreeNAS NUT моніторінг служби"
date: 2016-09-28 18:50:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/09/freenas-nut.html
---

[![](/assets/images/blog/a9af5e5067ec5cdc-5fdd8e5bde117f28.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhc7WS6uQVQxMskjUAfPdLu8-HLNtiSRxJ-k63VwgK88IQj8Pj5z31EhepDrP-Fu0K3R3xim-tDFSGhDO-IMsRl9lJgZJoQ0wKUwKfwrZQTd7cg438d91nGkbZKQzEYnc02ZoJ8cDaL93z2/s1600/freenas-logo.png)

Щось за деяких умов, у моєму сервері, перестає працювати зв'язок між службою NUT, і UPS.  
У логах є:   

```
COMMBAD - ups
```

Перезапуск служби відновлює зв'язок інколи:  

```
$ service nut restart
```

Тому задача перепускати службу автоматично.  
Але цього не достатньо для мого USB з'єднання, тому я вимикаю живлення на USB порту де під'єднаний ДБЖ і вмикаю знову:
  

```
$ usbconfig -d ugen0.2 power_off
$ usbconfig -d ugen0.2 power_on
```

Для визначення назви де USB порт з моїм ДБЖ:  

[![](/assets/images/blog/869bcf4432a53e6d-955a64d4dedd3585.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPeCVZ1nXg4LWaXJht1-s4Ps8O-eV4720H9Jvyl65LpE1rWXCtdtK2vBg3Qa91g3cus_d4IcnKmEB-8nhiZUbziVV3Bhtgh1PynMBLip8jcUZ1tStZ8mKNGtcum4T68OSOpYHqlDX8Ntwr/s1600/usbconfig.png)  
*usbconfig*

 Спроба. Редагую файл:     
/usr/local/bin/custom-upssched-cmd:  

```
  "EMAIL"|"COMMBAD"|"COMMOK")
       if [ "${ups_emailnotify}" -eq 1 ]; then
            echo "$NOTIFYTYPE - $UPSNAME" | mail -s "$(echo "${ups_subject}"|sed "s/ ....
            if [ "${NOTIFYTYPE}" = "COMMBAD" -o  "${NOTIFYTYPE}" = "NOCOMM" ]; then
                 logger -t upssched-cmd "commbad try service nut restart"
                 usbconfig -d ugen0.2 power_off
                 usbconfig -d ugen0.2 power_on
                 service nut restart
            fi
       fi
       ;;
```

  
Перевіряю:  

```
$ service nut stop
```

```
$ tail -f /var/log/messages
```

  

```
Sep 28 21:41:52 freenas upsmon[75199]: UPS [ups]: connect failed: Connection failure: Connection refused
Sep 28 21:41:53 freenas upssched-cmd: try service nut restart
Sep 28 21:41:57 freenas upsmon[75199]: Communications with UPS ups established
```

Таким чином служба перезавантажилася успішно. Перевірено на FreeNAS 9.3.
