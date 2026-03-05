---
layout: post
title: "Мережева ізоляція jails через vnet та private bridge members"
date: 2018-09-11 23:54:00 +0000
tags: ["bridge", "FreeBSD", "FreeNAS", "iocage", "isolation", "jail", "security"]
blogger_orig_link: https://lexxai.blogspot.com/2018/09/jails-vnet-private-bridge-members.html
---

За допомогою jails є можливість ізолювати процеси у окремому середовищі.  
Але якщо jails сконфігуровані з vnet=on і  jails використовують  той самий інтерфейс то  усі jails підключаються до мережі через створення epairs і додавання їх до спільного bridge.  
Це дозволяє  спілкуватися jails через мережу, але якщо це не потрібно ?  
То можна налаштувати через системні налаштування можливість застосувати правила firewall до bridge або bridge members, це по замовчування вимкнено.  
Є завдання не дозволити мережеве спілкування окремим jails за допомогою функціоналу [PRIVATE members of brigde](https://www.freebsd.org/doc/handbook/network-bridging.html).  
*A private interface does not forward any traffic to any other port that is also designated as a private interface. The traffic is blocked unconditionally so no Ethernet frames will be forwarded, including ARP packets.*  

[![](/assets/images/blog/fbdf0fc242ee4d0c-725b5b7e92b0e792.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiR8_DrSzWsQALqOil_KF6vv2SIoCHZ7sXF-WRbsAcgH1R_Ddi0HHjQM1cMi-kSOP8CglzIqFskzb9zh4q1wm5MfJdkgC1KtuEUBa_gHOja5tGvsHz6VGTvElNTsBF0-ieyxR8Q779EQGJ9/s1600/iocage01.PNG)  
*private bridge member*

  
Для додавання значення private до vnet members of bridge було створено скрипт (/root/test/pstart.sh).  

```
#!/bin/sh
maxloop=20
BRIDGE="bridge0"
while [ "${maxloop}" -ne "0" ]
do
  if [ ! -z "`/sbin/ifconfig ${BRIDGE} | /usr/bin/grep 'member: vnet' | /usr/bin/grep -v PRIVATE`" ]; then
    /sbin/ifconfig ${BRIDGE} | /usr/bin/grep member: |/usr/bin/grep -v PRIVATE | awk '{ print $2 }' | /usr/bin/grep 'vnet' | xargs -I % ifconfig ${BRIDGE} private %
    break
  else
    sleep 1
    maxloop=`expr $maxloop - 1` 
   fi
done
```

Його потрібно запускати перед запуском або після запуску jail у випадку використання iocage.  

```
./pstart.sh&
iocage start foo
```

Таким чином скрипт запускається у фоні і чекає створення інтерфейсу vnet до 20 сек, як тільки інтерфейс буде створений  скрипт додає відповідне значення private.  
  
Додатково, є метод використовувати автоматичний запуск налаштувань з використання параметра [exec\_poststart jail](https://www.freebsd.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports).  
  
Але є особливість  використання exec\_poststart - те що він повинен закінчити роботу з кодом 0, а також jail чекає закінчення роботи усіх підлеглих процесів.  
Тому я застосував не гарний але робочий трюк - використати системний планувальник at котрий запускається через cron за замовчуванням кожні 5 хвилин.  

```
iocage set exec_poststart='/usr/bin/at -f /root/test/pstart.sh now' foo
```

```
iocage start foo
* Starting foo
  + Started OK
  + Configuring VNET OK
  + Starting services OK
```

Результати роботи налаштувань:  

[![](/assets/images/blog/591dafeab82d77d9-108d6bdc2317632d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0vMMnmOikSkTEvz0JKpmtRFRwDRCoJJwffbI0-1jr_pieRu5Mq9ivv7ChWP2b0-_7ThkpDt4Utv0u-TUNkX4apl00Nmuo8yvxS_dAzGnJgMYrRoSvnq9AM_wVba2t7n32k7_OZlGDgJql/s1600/iocage02.PNG)  
*example of result 1*

[![](/assets/images/blog/89aa23e2ca5960a9-30dc8d3e47be7cf1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhA0AmYQ3muo_igRXZwnRQ8wbUnbx7xnRxZshKw5-r-fpumZL-vG9bG7GbY5QOrbn5IsD7XSGFBwGi-a3tmBF8g4QcwmoiJEIwd1TVIFyvnA01djXBczHC6AVj1aPhGXRT_KB2YdRPREWGs/s1600/iocage03.PNG)  
*example of result 2, added Private*

[![](/assets/images/blog/c343eb45b0ce6a8a-3ee920d5e8ba5e1f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKDWKmEH6UedaJKzAmTwdXjjenTO8zJWEidaZ9UIaTlAqxMTwBWWg1cyaOfLXOrXOPPs3BiHgmYItv1mo2OZ_R9v0kN1xske-88uvx52m0tUQB3ZbMijhDgbovOINxJCGPcYZ7J_kcoOSk/s1600/iocage04.PNG)  
*negative test ping jail foo to jail faa*

[![](/assets/images/blog/c67733104219ec37-0c431ae208b87d0d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8WQ5-ADmnDB2Im1JXSd5qAgrJESZLNi3Mm2SGf14fkdld_-vffW9TImDITiqO0VuutGCpd6Y2_63KclgGjWFUTpYb0VWWo4h78HEIvVLyHhc8nPNejLpgnGWGgNLjHoJyysDXDtRainF7/s1600/iocage05.PNG)  
*negative test ping jail faa to jail foo*

[![](/assets/images/blog/e51b7cf35fbe9db7-a7bb2bcec6abf670.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjf3v3ICnp6QvRZHh4dvguDWZLkLfrWgJYerW4MFaw0LgwPM8IqyGdmTaGOltBjyqE2ATSvGmYrAf1iGKmaTBb_K8IWS8dgk27CYZQ98Pzhx6sjRHC3PfyuWW6ziLLa72s3N-H8fNAJ0jG-/s1600/iocage06.PNG)  
*positive test ping from jail faa to other non private jail*

  
  
Буду радий на кращі пропозиції ...
