---
layout: post
title: "Нотатка. pfsense. DNS views для різних локальних мереж"
date: 2017-11-21 23:31:00 +0000
tags: ["dns", "network", "pfsence", "security", "view"]
blogger_orig_link: https://lexxai.blogspot.com/2017/11/pfsense-dns-views.html
---

[![](/assets/images/blog/2e85b51bb5193863-1e0320b3b90b936a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVrpBGgibZpXBUne-DaS0E9EUXkLtZof5QvFkbP9vMxXJRoDrROMzSxqB1SJPiT-6Nly6_fFpP7Dw28qyPeVolf-GK6YtL0k4_wjTPDWL6du4xEGNF-UO9E7SiRIabQeHYZDk_En_xCZhH/s1600/pfsence-logo.PNG)

Так сталося що потрібно було для різних мереж видавати різну адресу для одного і того запису у DNS сервері.  
Знаючи що у BIND це можливо робити за допомогою видів, почав шукати таку можливість у pfsense.  
pfsense використовує один з варіантів як DNS resolver що є програмою [unbound](https://www.unbound.net/). Читаючи [документацію](https://www.unbound.net/documentation/unbound.conf.html) до [unbound](https://www.unbound.net/), знайшов розділ  View Options.  
В інтерфейсі GUI pfsense DNS resolver, такої функції не має, але є можливість додати свої значення до unbound.conf (Show Custom Options).  
Так додавши такі налаштування:   

```
server:
access-control-view: 192.168.9.100/32 testview
view:
name: "testview"
local-zone: "local.lan" static
local-data: "test.local.lan. 90 IN A 10.10.10.10"
local-zone: "100.9.168.192.in-addr.arpa" typetransparent
```

Ми для клієнта локальної мережі 192.168.9.100 створили testview, і значенню імені "test.local.lan" перевизначили адресу на 10.10.10.10.  

[![](/assets/images/blog/c0ac60577e0a1fa4-dccb746bf88e1e3c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8XC90TycPgqoaHtehQX_olHi16NaFYeUacfR-jWvxqXFsHHG5-H7EaEDzV_ak2Ovxe33alFGPTQ8BS5PTC3x3S2j0fLNHNYTKyzvDp0nLgd_c_jpXUi19zKq-KvnJ8DemWkw05uSeWbqx/s1600/pf-dns-view.PNG)  
*додавання View Options у DNS resolver pfsense*

 Подяка за [форуму](https://forum.pfsense.org/index.php?topic=126740.msg699877#msg699877) за те що вказали необхідність додати рядок "server:" на початку, без нього видавало помилку конфігураційного файлу.
