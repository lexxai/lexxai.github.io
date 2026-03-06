---
layout: post
title: "pfSence, Squid proxy, deny acces to DMZ networks"
date: 2017-09-13 20:47:00 +0000
tags: ["pfsence", "security", "squid"]
blogger_orig_link: https://lexxai.blogspot.com/2017/09/pfsence-squid-proxy-deny-acces-to-dmz.html
---

[![](/assets/images/blog/cdd1478625241c89-1e0320b3b90b936a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxQsRUbZHW_69ocsPymmX4ktev4YbzZucWBPleqZPYoWGbs7rS4NI4ft6S0IBpFY1Grp4hZAvj3ZH31bJvD7aZzymJHzpytWlZiQAcf9MZZ07aDrQqCkTPIs1kSFyqh0UycGZD93Bt7YE9/s1600/pfsence-logo.PNG)

Якщо використовувати разом з [pfSence](https://www.pfsense.org/) модуль [Squid proxy server](https://wiki.squid-cache.org/) то існує можливість потрапляти користувачам до деяких [DMZ](https://lexxai.blogspot.com/void(0)) локальних мереж.  
Використовуючи локальні адреси роутера але через процес squid.  
Як вихід додав до дозвіл до [DMZ](https://lexxai.blogspot.com/void(0)) мережі тільки спеціальному користувачу  який пройде автентифікацію на у проксі сервері, якщо така (proxy\_auth) використовується.  
Для цього у Package/Proxy Server: General Settings/General , натиснути "Show Advanced Features" і у розділі  
Custom Options (After Auth) додаю:  

```
acl BlockedHost dst 10.0.0.0/8
acl SysopUser proxy_auth sysopuser
http_access allow BlockedHost SysopUser
http_access deny BlockedHost 
```

  
Де "10.0.0.0/8" - локальна мережа з DMZ, а "sysopuser" ім'я користувача котрому дозволено доступ після автентифікації до цієїї мережі.  
  

[![](/assets/images/blog/fa4c416f11df07a9-1e27b9be7e2542da.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizVhomRYgecBc1ptFtT8LXEjX8tPFZlHR3X_Q-eXTfMIcqtgoonfl2c5mYlB0ssT38a-XWNO4GpKuNmhzBJqbqZu-jag9eLUexLByk1p0etqx3XG5XtbBCPrulPbGCWs0qIQ2g97qAM7O_/s1600/pfsence-squid-acl-denylocal.PNG)  
*Proxy Server, Advanced Features*
