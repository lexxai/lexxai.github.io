---
layout: post
title: "Нотатка для себе. CARP monitoring script pfsense."
date: 2021-05-24 12:34:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/05/carp-monitoring-script-pfsense.html
---

Маю два сервери pfsense (2.5.1-RELEASE ) у різних віртуальних машинах з налаштованими Proxy сервер - Squid, та Virtual IP type CARP.

[![](/assets/images/blog/b3059a7241780ad7-21dee6f3926c3194.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiP7bOwHBr9fPdopbh5q_g41uTuvu1H4EWQa1XB78HQOKtqBlojh-dvaMCW_9ogB8pJ_xCy0V7gG36e6-xKts3jN5KXQfva6ge5Pqav5nloTkb6W9_crt9HsqLo14S6TjFgCoK9xkkTCdbt/s1180/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-05-24+15-18-14.png)  
*Virtual IP type CARP.*

Задача мати на напоготові резервний Proxy сервер у випадку коли ведуться роботи на одному з гіпервізорів.

Для цього існує пункт налаштування "Squid General Settings/CARP Status VIP", і теоретично тут достатньо вибрати у списку необхідний спільний Virtual IP. 

Але виявленні були проблеми з тим що сервіс автоматично не змінює свій стан синхронно зі зміною CARP (Master/Backup). При завантаженні завжди вмикається сервіс squid ігноруючи  /rc.start\_packages: [squid] Do not start service... (CARP BACKUP/INIT).

[![](/assets/images/blog/f64607381e67c72f-d2fc5b3089d3d2d3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKKSuKNowuvksjcTX7t_Bdt7DCMZa-Div_8FVKfuYuXFvgtdquljMOFXXjq6Yn7H7eZb1IUQcydxAQT3KrQu9bbBEZ8J6y_EW3wqDLU7tmlJFyD14mcdGzjp2KfuOW3LMcY_ldzn7I6D6M/s1187/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-05-24+15-15-26.png)  
*pfSense Proxy Server - CARP Status VIP*

Тому для швидкого вирішення проблеми було застосований скрипт на базі [автора "maxxoverclocker"](https://www.reddit.com/r/PFSENSE/comments/f2xhdi/carp_status_change_doesnt_seem_to_run_custom/).    

```
#!/bin/csh
set path = ($path /bin /sbin /usr/bin /usr/local/bin)
set carp_check_interface='hn1'
set startminute=`date +"%M"`

@ bootseconds=`date +%s` - `sysctl kern.boottime | awk '{print $5}' | sed 's/,//g'`

#echo $bootseconds

if ( $bootseconds >= 60 ) then
    set carp_status=`/sbin/ifconfig $carp_check_interface | grep 'carp:' | awk '{print $2}'`
      if ( "$carp_status" =~ 'MASTER' ) then
        echo "CARP status is 'MASTER'"
        if ( ! -e "/var/run/squid/squid.pid" ) then
          echo "SQUID start"
           /usr/sbin/service squid.sh restart
        endif
      else if ( "$carp_status" =~ 'BACKUP' ) then
        echo "CARP status is 'BACKUP'"
        if ( -e /var/run/squid/squid.pid ) then
           echo "SQUID shutdown"
           /usr/local/sbin/squid -k shutdown
        endif
      endif
    endif
endif
```

Змінна carp\_check\_interface має назву інтерфейсу, потрібно змінити на реальний.

Скрипт збережено до файлу /root/carp-scan.sh.

І він запускається кожної хвилини через додаток cron.

[![](/assets/images/blog/c01784dd5fe267a2-4ecb1c5e57be3026.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPTdFRoBpzKpGMVU5eAXd4e8gSbYGj-6OZahscnPeDU-YpxQKgMS7XoSiVnzo6NX_gzIoP8lo2D2NyDtPdxT5GzC4a9AitMakFY1Jp1wXS12JBd-j7Q5qAXsSX6pJuM1ffk8AFkGMlCQuH/s1012/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-05-24+15-29-36.png)  
*Cron task*
