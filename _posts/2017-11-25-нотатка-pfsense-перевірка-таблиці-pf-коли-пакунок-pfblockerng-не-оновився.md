---
layout: post
title: "Нотатка. pfsense, перевірка таблиці pf коли пакунок pfBlockerNG не оновився"
date: 2017-11-25 21:45:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/11/pfsense-pf-pfblockerng.html
---

[![](/assets/images/blog/2e85b51bb5193863-1e0320b3b90b936a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVrpBGgibZpXBUne-DaS0E9EUXkLtZof5QvFkbP9vMxXJRoDrROMzSxqB1SJPiT-6Nly6_fFpP7Dw28qyPeVolf-GK6YtL0k4_wjTPDWL6du4xEGNF-UO9E7SiRIabQeHYZDk_En_xCZhH/s1600/pfsence-logo.PNG)

Використовуючи pfsense, я налаштував пакунок pfBlockerNG, для обмеження підключень до певних сервісів тільки з відомих країн.  
Одного разу виявив що підключення стали заблоковані. Після аналізу з'ясував, що сервер перезавантажився і доступ не було надано так як таблиці доступу (IP\_COUNTRY\_ALLOWED\_NG) були пусті, а оновлення у мене налаштовано один раз на добу.  

[![](/assets/images/blog/7fe65212f06c926c-3b8802050385824b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcidg7r5zvUD24xeiakySwoc5Y_ALsiVOBg_98GkCtuOjgfcWQfJBXB-7KnhjTRZcuLdTkWjs1fLD2acxZawMdmlG0Kj-qqbdeL8Bc5xY3DKvW3HvMk6S2ZFj2nk8EjfuXD07nwPGe1sEt/s1600/pf-use-ng.PNG)  
*Використання таблиціIP\_COUNTRY\_ALLOWED\_NGу правилах*

  
Тому щоб подібне не повторювалося додав скрипт (check\_ng\_tables.sh) котрий перевіряє кількість записів у таблиці, в  випадку коли їх кількість менше чи критичне значення я форсую оновлення таблиць pfBlockerNG, і сповіщаю про це електронною поштою адміністратора.  
  
/root/scripts/check\_ng\_tables.sh  

```
#!/bin/sh

cntlines=$(/sbin/pfctl -t IP_COUNTRY_ALLOWED_NG -T show | wc -l)
if [ $cntlines -le 100 ]
then
    echo "count of pf table IP_COUNTRY_ALLOWED_NG is ($cntlines) less than 100, try reload pfblockerng.php"|/usr/local/bin/php /usr/local/bin/mail.php -s'[gw] IP_COUNTRY_ALLOWED_NG' postmaster@yourdomain.com
    /usr/local/bin/php /usr/local/www/pfblockerng/pfblockerng.php cron >> /var/log/pfblockerng/pfblockerng.log 2>&1
fi
```

  
Потім додав перевірку за розкладом кожні 5 хвилин.  

[![](/assets/images/blog/c49928e6fe620569-f52f1a324120a58c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiA576nheK3XEGUy7BIj6MPDYpNktfqK0M9UuDyHkvMYhyphenhyphenFZP5DasJgMNy4nNmEbvuZFCi9XgIxHFKWg1ghsfpUs-K0-Ij9lwA0zlaxL-ejnJGVswlzfwI6jhSrfklHJZF8e3aNSlJ-A-Iz/s1600/pf-chech-ng.PNG)  
*Додав перевірку таблиці за розкладом cron*
