---
layout: post
title: "Postfix SASL cyrus"
date: 2012-10-01 19:27:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/10/postfix-sasl-cyrus.html
---

## Postfix SASL cyrus

Була необхідність налаштовувати авторизацію, ось коментарі щоб не забути :)   

*/usr/local/lib/sasl2/smtpd.conf:*  
pwcheck\_method: saslauthd  
mech\_list: plain login  
  
*Postfix main.cf:*  
smtpd\_sasl\_auth\_enable = yes  
smtpd\_sasl\_authenticated\_header = yes  
smtpd\_sender\_login\_maps = pcre:/var/postfix/smtpd\_sender\_login\_maps.pcre  
  
*/var/postfix/smtpd\_sender\_login\_maps.pcre:*  
/^(.\*)@.\*$/   ${1}
