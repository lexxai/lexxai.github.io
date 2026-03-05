---
layout: post
title: "OpenWRT додати виключення DNS для певних доменів"
date: 2019-03-13 01:20:00 +0000
tags: ["dns", "linux", "openwrt", "router"]
blogger_orig_link: https://lexxai.blogspot.com/2019/03/openwrt-dns.html
---

Для домашнього роутера (OpenWRT) я використовую додатковий захист мережі у вигляді використання DNS сервісу - [opendns.com](http://opendns.com/).  
Але інколи треба швидко додати виключення у DNS запитах і наприклад використовувати open dns google сервери.  
Так, сервіс медіа каталогу [uafilm](https://uafilm.tv/) потребує доступ до доменів, на кшталт: mxase.clmbtech.com, а він у свою чергу блокується сервісом [opendns.com](http://opendns.com/), тому я додав виключення для домену і піддоменів "clmbtech.com" додавши перенаправлення запиту до іншого DNS серверу 8.8.8.8.  
Реалізація додавання параметру DNS forwardings - /clmbtech.com/8.8.8.8  
  

[![](/assets/images/blog/3c55f588aca42e1f-239f40fd81612bbe.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZZIQ9tumhG1k2iBN3n_YjV-ZMZDhYwro5T8uKh5AMhTS23syaC-tUSIvxhiLO-f8CkJWQNpnm7O46Tv9ItptySca8AuKgCGJ8XpVnnkPqaemRXRWGReGVdLJm2YSNtRtVq82n6ucLxp4g/s1600/dnse.PNG)  
*custom DNS forward, OpenWRT 18.06*

Для постійних виключень також можна додати виключення і у налаштуваннях [opendns.com](http://opendns.com/).  

[![](/assets/images/blog/8c0e11d6147b08ca-77ac41ce8a0aace2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4sb2ONn-TiNQUwgcpJHJtMoFUSejebVdtzkN2Cj435wI-gUpWYps6g7rWZq0VtBDijdSrGELUEZTaMs5Fehq3BrnoKaHtwdHccVH0TWUNV3afmSHfO8sPyEI5iFkch951zdVdM-mt2eDR/s1600/dnse1.PNG)  
*dashboard.opendns.com/settings*

Повний список що я додав для програвання у програмі [HD VideoBOX](https://dkc7dev.com/hdvideobox/) ресурсів з uafilm:  
/clmbtech.com/8.8.8.8, /crashlytics.com/8.8.8.8, /apollostream.xyz/8.8.8.8
