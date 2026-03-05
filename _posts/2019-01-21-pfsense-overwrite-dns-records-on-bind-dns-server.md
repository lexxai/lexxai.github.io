---
layout: post
title: "pfSense overwrite DNS records on BIND DNS server."
date: 2019-01-21 20:15:00 +0000
tags: ["BIND", "dns", "FreeBSD", "pfSense"]
blogger_orig_link: https://lexxai.blogspot.com/2019/01/pfsense-owerwrite-dns-records-on-bind.html
---

[![](/assets/images/blog/4a06743947a0a90f-28c3030eb22c3da6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDNRpFXrSIQuOTAaADO1qile0kruSTweD6TT1-SV5fohJO_qwzuGvPYYiI-i03OcgSgXoNzvCtq7ybXjdjgqzf-5Z5DcExyFK9Zag28-AkHzCshmk1EuwkebYoybZq5qbP-bG3D93VhStJ/s1600/pf-dns_bind-190x190.png)
Стала необхідність створити резервні - Secondary [DNS zones](https://en.wikipedia.org/wiki/DNS_zone) інших [DNS](https://uk.wikipedia.org/wiki/%D0%94%D0%BE%D0%BC%D0%B5%D0%BD%D0%BD%D0%B0_%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0_%D1%96%D0%BC%D0%B5%D0%BD) серверів котрі працюють у віртуальному середовищі. У випадку коли проводяться операції з віртуальним середовищами до [DNS сервери](https://uk.wikipedia.org/wiki/DNS-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80) не відповідають. Реалізував secondary DNS zones за допомогою [BIND DNS](https://uk.wikipedia.org/wiki/BIND) server у [pfSense](https://www.pfsense.org/).  
Але для ефектного використання мережевих сервісів котрі працюють як на зовнішньому інтерфейсі так і у внутрішній мережі є необхідність перевизначати значення  DNS записів для різних локальних підмереж.  
Так наприклад запис era.lexxai.pp.ua має IP адресу xxx.231.86.xxx, з глобальних DNS серверів, а потрібно щоб в локальній мережі для клієнтів підмережі 172.16.0.0/24, DNS запис era.lexxai.pp.ua мав IP адресу 10.250.10.15.  
Згідно з [публікацією: Overriding DNS for fun and profit.](https://www.redpill-linpro.com/sysadvent/2015/12/08/dns-rpz.html)  
Додаємо зону "rpz" де додаємо всі DNS записи які треба перевизначити.  

[![](/assets/images/blog/9212ac860c494047-053811694820f248.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcHlUshZ2iUE-b1c_jOaV6zRMHEvxav-voCw467ogl3wD57Zwip4wprz7IoZAtilFqkhiR_ie9A2Yz8YcqKLwrYa0vwXtlBGx-awmEx8nq_sFx73Dla-e1dVILc7OKcu2AVZhJhmxUVGG1/s1600/dns-owerwrite-02.png)  
*Зона DNS "rpz" визначення зони "net172"*

[![](/assets/images/blog/7868d1e78205732e-d66269cba8bf16a5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjngp8DBE1A-JG_sy3n4XhkYioqrft-VVUs2i-81Qgu_6RZOk9x7ADtX85bY_M3TzG44jcR_P63JdoCEMPgCkiB_rfLPqLOwk9L_ReYxkxa5_W4Lz2wTencuJJsJ0k_vixiBDvk88MOWCNS/s1600/dns-owerwrite-03.png)  
*Зона DNS "rpz"додавання записів*

[![](/assets/images/blog/d85ea35eb861616a-1933fc6d1eaea39c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipKNhh17Wr2hDVcMvazkLomlNbXNbHRoeIeqtB8Er0YUSTceaahKbKWSqnGAoBdXILOYDfeX52zZVjZaQWyuArmL6hAW118eXOeHm2HsM3sOHLiORrCiiDb1cMJUf2NBZvpmtz4HMzJlg3/s1600/dns-owerwrite-01e.png)  
*Зона DNS "rpz"*

[![](/assets/images/blog/27f098a0ffea2e25-c5c44a3b41dd5847.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg669I8RFjV8Fg3FoLygwCOCZBe_OnNb-rUzQvYbcpNigXi563a1OEKBgjujOX0urhC3s_U0GbLWp9tDjPYARzeLUCebfcVUOxXmD2jpafHJgnTx1tteEcGdmR7hWmmSarqtkllQ6fWClDW/s1600/dns-owerwrite-06.png)  
*BIND DNS ACLs "net172" , 172.16.1.0/24*

[![](/assets/images/blog/c7a135e14fe48307-4a2ca986155a2ddb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7oblSMkBLJnC9RA4Ebec3SjAsxw1HQVfn8AndpKH_qHpuIQi-4M3QLqCEB9YPz0hQo-s6qX8-W115EPRnsTsrOYuwqNbHiOfDCbTnh33SxAejVeqIO-3mYkltcoJRi7H2eT3jUv5gR-x4/s1600/dns-owerwrite-07.png)  
*BIND DNS View "net172"*

  

[![](/assets/images/blog/c4fc8694aef72d2a-68d4929aea6e0bb1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzbiVjomCGW5th5gDX5Yre1KP1TNa7u4rFZ1WeW_cOmaco0TNm4I9mAg8_pgShOzIF4iLUz4RKIexnKl2xg8WgwnxtDdBkmByp2rTAO6X7ZTnU4Vdy9gpJ6YqNN0Xb5SzObA6Q9jnSwtRM/s1600/dns-owerwrite-04.png)  
*Загальні налаштування BIND DNS сервера*

[![](/assets/images/blog/8119b00a779c1d38-a9313b9bf79bc413.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhhhJtbnrjbI-La2UwKvXtkAAvTnXRmWN6Jc2oC1GOY62wzVvWgGfJTlTkVTibHcs7iG7gAo60REf70zSzv9P-XAJANp2BN2ztk5FyR-Lc9l3hi-mOQ02vnJi-xgbynBfDBn7xW1W3HRtRy/s1600/dns-owerwrite-05.png)  
*Розширенні налаштування BIND DNS сервера, в custom options визначаємо response-policy {zone "rpz";};.*

[![](/assets/images/blog/f37d1e0e9c18ac5d-35e5f645062957d1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1om1vZLAI0id0RCXYs_3R88TL_vfEB3QM56yWlnoJCS8M9Sv46UWH7gyp1PsPgRReQgPHeefmvJlSLgD8pU_wNIOYjs53BwQAyMjd6EegU9Qxk-lmb0zLz7T4kaL-lwg_-2uCQshpRf74/s1600/dns-owerwrite-08.png)  
*Або можемо не робити запис в розширених налаштування BIND DNS, а відмітити безпосередньо опцію "Response Policy Zone"в зоні "rpz"*

  
Тестуємо з використанням локального DNS сервера в мережі - pfSense.  
  

```
$ host -a era.lexxai.pp.ua
Trying "era.lexxai.pp.ua"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18328
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 0

;; QUESTION SECTION:
;era.lexxai.pp.ua.              IN      ANY

;; ANSWER SECTION:
era.lexxai.pp.ua.       5       IN      A       10.250.10.15

;; AUTHORITY SECTION:
rpz.                    43200   IN      NS      localhost.
```

Тестуємо з використанням віддаленого DNS сервера
  

```
$host  era.lexxai.pp.ua 1.1.1.1
Using domain server:
Name: 1.1.1.1
Address: 1.1.1.1#53
Aliases:

era.lexxai.pp.ua has address xxx.231.86.xxx
```
