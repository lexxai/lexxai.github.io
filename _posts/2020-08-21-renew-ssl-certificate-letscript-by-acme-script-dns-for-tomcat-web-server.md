---
layout: post
title: "renew SSL certificate Let'Script by ACME script (DNS) for Tomcat web server"
date: 2020-08-21 19:38:00 +0000
tags: ["java", "linux", "script", "security", "software", "ubuntu"]
blogger_orig_link: https://lexxai.blogspot.com/2020/08/renew-ssl-certificate-letscript-by-acme.html
---

[![](/assets/images/blog/8b174ec7d4213c3e-568f68e9f7eaa0bb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXSnmaowz3WmmNjLf1SkPlR0DIUXN8Zl1Zp21Hg8RBVDjqic-7I3uJz7i3RQ2T3b-s90daMBIXwLYDLTJJi_lNAuYD9pr-pmO6uJJ06_5Zs8tKC1Lu2kkmyNRBn-fGfwf940cNJIW9k5zB/s400/logo.png)

Я використовую проект [OpenMeeting](https://openmeetings.apache.org) котрий працює на [Tomcat web server](http://tomcat.apache.org/) ([Java](https://www.java.com)).

Стала задача використати [SSL](https://uk.wikipedia.org/wiki/SSL) сертифікати від [Let's Encript](https://letsencrypt.org/) для [OpenMeeting](https://openmeetings.apache.org).

Але безкоштовні
сертифікати мають особливість, короткий термін життя, і їх періодично
потрібно оновлювати. Для звичайних Web серверів, існують скрипти які
автоматично спрощують життя.

Для [OpenMeeting](https://openmeetings.apache.org) і відповідно для Tomcat web server, дещо інше рішення так як сховищами ключів та сертифікатів є [Java KeyStore](https://en.wikipedia.org/wiki/Java_KeyStore).

Для генерації та оновлення сертифікати від [Let's Encript](https://letsencrypt.org/) є скрипт наш кшталт [ACME](https://github.com/acmesh-official/acme.sh). Я використовую цей скрипт під операційною системою [Ubuntu 18.04 LTS](https://ubuntu.com) в режимі [DNS manual mode](https://github.com/acmesh-official/acme.sh/wiki/dns-manual-mode).

Для генерації сертифікатів в  [DNS](https://github.com/acmesh-official/acme.sh/wiki/dns-manual-mode) режимі в сервері DNS для підконтрольного домену додаються специфічні TXT записи \_acme-challenge.

```
acme.sh --issue -d example.com --dns \  
 --yes-I-know-dns-manual-mode-enough-go-ahead-please  


Add the following txt record:  
Domain:_acme-challenge.example.com  
Txt value:9ihDbjYfTExAYeDs4DBUeuTo18KBzwvTEjUnSwd32-c  
  
  
Please add those txt records to the domains. Waiting for the dns to take effect.

  
acme.sh --renew -d example.com \  
 --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

Після успішного оновлення (*acme.sh --renew*), у робочій теці скрипта (*~/.acme.sh*) буде згенерований для кожного домену свій ключ і сертифікат до нього.

Для використання їх з сховищами ключів та сертифікатів в [Java KeyStore](https://en.wikipedia.org/wiki/Java_KeyStore), треба їх [імпортувати](https://tomcat.apache.org/tomcat-9.0-doc/ssl-howto.html) до keystore сховища.

Для автоматизації всього процесу був створений скрипт, котрий запускається за розкладом раз на місяць від імені root, де ім'я звичайного користувача у котрого встановлено скрипти - openmeeting.

```
#!/bin/bash

DOMAIN=example.com
GUSER=openmeeting

HOME=/home/${GUSER}

sudo -u ${GUSER} ${HOME}/.acme.sh/acme.sh --cron --home "${HOME}/.acme.sh"

CMOD=$(stat -c %y ${HOME}/.acme.sh/${DOMAIN}/${DOMAIN}.cer  | cut -d' ' -f1)
CDAT=$(date +"%Y-%m-%d")

echo  "DATE MOD:${CMOD} NOW:${CDAT}"

if [ ! "${CMOD}" == "${CDAT}" ];then
 echo "NOT SAME DATE"
 exit
fi

cd ${HOME}/.acme.sh/${DOMAIN}

KEYSTORE=localhost.jks
STOREPWD=openmeetings
OMSTORE=/opt/openmeeting/conf

rm openmeetings.p12

openssl pkcs12 -export -in ${DOMAIN}.cer -inkey ${DOMAIN}.key -out openmeetings.p12 -name openmeetings -certfile ca.cer
-certfile fullchain.cer -passout pass:${STOREPWD}

rm ${KEYSTORE}

keytool  -importkeystore -srcstorepass openmeetings -srckeystore openmeetings.p12 -srcstoretype PKCS12 -deststorepass ${
STOREPWD} -destkeystore ${KEYSTORE} -alias openmeetings -deststoretype PKCS12
keytool  -import -alias root -keystore ${KEYSTORE} -storepass ${STOREPWD} -trustcacerts -file ca.cer
echo yes | keytool  -import -alias intermed -keystore ${KEYSTORE} -storepass ${STOREPWD} -trustcacerts -file fullchain.c
er

if [ -s "${KEYSTORE}" ];then
 rm  ${OMSTORE}/${KEYSTORE}.bak
 mv  ${OMSTORE}/${KEYSTORE} ${OMSTORE}/${KEYSTORE}.bak
 cp  ${KEYSTORE} ${OMSTORE}/${KEYSTORE}
 ret=$?
 echo "Copy return state: ${ret}"
 if [ "${ret}" -eq 0 ];then
  /etc/init.d/tomcat3 restart
 fi
fi
```

Скрипт запускає оновлення сертифікатів, перевіряє дату сертифікатів, у випадку коли вони нові, запускає процедуру створення сховища keystore з новими сертифікатами.   
У випадку успішного копіювання сховища keystore (localhost.jks) до робочої теки Tomcat сервера, сервер Tomcat презапускається.
