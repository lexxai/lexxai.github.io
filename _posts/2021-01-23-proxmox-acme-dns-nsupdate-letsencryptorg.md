---
layout: post
title: "Proxmox ACME DNS NSUPDATE letsencrypt.org"
date: 2021-01-23 02:31:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/01/proxmox-acme-dns-nsupdate-letsencryptorg.html
---

Завдання отримання
[letsencrypt.org](http://letsencrypt.org)
сертифікату в Proxmox за перевіркою  динамічного оновлення
[запису в DNS](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge) за допомогою ключа [tsig](https://uk.wikipedia.org/wiki/%D0%86%D0%BD%D1%84%D1%80%D0%B0%D1%81%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0_%D0%B2%D1%96%D0%B4%D0%BA%D1%80%D0%B8%D1%82%D0%B8%D1%85_%D0%BA%D0%BB%D1%8E%D1%87%D1%96%D0%B2) і власно контрольованого [BIND](https://uk.wikipedia.org/wiki/BIND) сервера.
  
Це потрібно в тих ситуаціях коли доступ до web сервера обмежений для зовнішньої перевірки серверами [letsencrypt.org](http://letsencrypt.org) при застосуванні стандартного методу: [http-01 challenge](https://letsencrypt.org/docs/challenge-types/#http-01-challenge).

#### tsig

```
tsig-keygen -a HMAC-SHA512 ns240 > ns240.key
```

ns240.key:

```
key "ns240"   
{ algorithm hmac-sha512;  
  secret "VLv54K+dqSSk9lF75GKREQI8BT0ZYBs9BraQY3D/6f5b1Aw41NN86BwxRNATu7iDoEAiqTCUOk7B6SfxG7Q30A==";  
};
```

#### BIND

named.conf:

```
key "ns240.key" {  
 algorithm hmac-sha512;  
 secret "VLv54K+dqSSk9lF75GKREQI8BT0ZYBs9BraQY3D/6f5b1Aw41NN86BwxRNATu7iDoEAiqTCUOk7B6SfxG7Q30A==";  
};  
          
zone "lexxai.pp.ua"  
{  
  type  master;  
  file  "/usr/local/etc/namedb/master/db.lexxai.pp.ua";  
  allow-query { any; };  
  update-policy {  
   grant ns240.key. name _acme-challenge.ns240.lexxai.pp.ua. TXT;  
   grant ns241.key. name _acme-challenge.ns241.lexxai.pp.ua. TXT;  
  };  
};
```

#### PROXMOX

[![](/assets/images/blog/30c82fa4c9a81ce8-b81144a210204beb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiu-WMR7c-C0GIR2p1JxNFc5Ypb-ppi70xg3M6EmAoVgFgPsCJ-VyZn41VqiNDbWH2NQq_iiYc8ynQTC98MXrAKy4kd8eWXJVZ-hU-EPPw0bYLsvoxh7vVj33xFmE52QqBk0eEv66HMJ1Nr/s1214/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-23+04-08-51.png)  
*Proxmox ACME DNS plugin*

```
NSUPDATE_SERVER=ns1.lexxai.pp.ua  
NSUPDATE_KEY=/home/nskey/ns240.key  
NSUPDATE_ZONE=lexxai.pp.ua
```

```
key path: /home/nskey/ns240.key  
chown nobody:nogroup /home/nskey/ns240.key
```

[![](/assets/images/blog/e0e3e16bebd70df7-558a9f2987662a98.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgoMdKdtrgJhbRTuxRFcIep5bI8glzcunznX99ulGt2T3xZsK0VajAY5AwFgbTSZ7bf3tacYOhUMuNaP3zghdz78Mpt1YacrerSXulOMkyPdISiGNUSKNmw3aW7n6_xqzFSBi5aAm2Nlk7Y/s573/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-23+04-09-51.png)
