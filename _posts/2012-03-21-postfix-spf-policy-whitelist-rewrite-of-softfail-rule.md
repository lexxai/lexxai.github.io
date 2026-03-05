---
layout: post
title: "Postfix SPF policy, whitelist, rewrite of softfail rule"
date: 2012-03-21 10:02:00 +0000
tags: ["perl", "posfix", "python", "spam", "spf", "whitelist", "відсилання пошти"]
blogger_orig_link: https://lexxai.blogspot.com/2012/03/postfix-whitelist-spf-policy.html
---

Sender Policy Framework  
Введення перевірки [SPF](http://en.wikipedia.org/wiki/Sender_Policy_Framework) ([Sender Policy Framework](http://en.wikipedia.org/wiki/Sender_Policy_Framework)) до налаштувань поштового сервера, надає можливість достатньо ефективно боротися зі спамерами котрі відсилають пошту з недозволенних IP адрес для конкретного домену.  Так на сьогодні вже багато поштових доменів ввели запис типу TXT до [DNS](http://uk.wikipedia.org/wiki/%D0%94%D0%BE%D0%BC%D0%B5%D0%BD%D0%BD%D0%B0_%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0_%D1%96%D0%BC%D0%B5%D0%BD) з описом дозволених адрес і дією щодо дозволу використання цих адрес. Наприклад:   
*example.com. IN TXT "v=spf1 a mx -all"*  
Для свого поштового сервера [Postfix](http://www.postfix.org/) я використовував Python реалізацію "[SPF Policy Server for Postfix (Python implementation)](https://launchpad.net/pypolicyd-spf/)" на базі портації для FreeBSD "postfix-policyd-spf-python" тут є розширені можливості, чим наприклад у реалізації "postfix-policyd-spf-perl".  
  
  
  
Але за певних умов у мене перестала працювати реалізація "postfix-policyd-spf-python", прийшлось тимчасово перемикатися на "postfix-policyd-spf-perl", і надалі будуть описані деякі реалізації до дають можливість додати мінімально необхідного функціоналу.  
  
Оновлення: виявлено проблему не функціювання "*postfix-policyd-spf-python*" - з оновленою версією компонента py27-dns-2.3.6. Виявленні проблеми з запитом DNS : DNS time out". Для діагностики треба запустити *python spf.py "209.85.214.49" "test@mail-**bk0-f49.**google.**com" "mail-bk0-
f49.google.com"* результат повинен бути *('none', 250, '')*. Для виправлення - встановити попередню версію [py27-dns-2.3.5](https://sourceforge.net/projects/pydns/files/pydns/pydns-2.3.5/).  
  
Встановлення postfix-policyd-spf-python  
Варіанти   

* з портів FreeBSD, *ports\postfix-policyd-spf-python, make install clean*
* з пакунків FreeBSD, *pkg\_add -r "py27-postfix-policyd-spf-python-\*"*
* з дистрибутиву [pypolicyd-spf](https://launchpad.net/pypolicyd-spf/+download%20%20) , *python setup.py build, python setup.py install*

перевірити чи встановленні залежні модулі: python27, py27-spf, py27-dns  
Знаходимо файл policyd-spf.conf, у мене це  /usr/local/etc/postfix-policyd-spf-python. Налаштовуємо згідно з описом  що знаходиться у policyd-spf.conf.commented.  
Зробити зміни у Postfix, master.cf додавши:  
policyd-spf  unix  -       n       n       -       0       spawn  
    user=nobody argv=/usr/local/bin/python2.7 /usr/local/bin/policyd-spf /usr/local/etc/postfix-policyd-spf-python/policyd-spf.conf  
Як і як мінімум зробити зміни у Postfix,main.cf:   

smtpd\_recipient\_restrictions = ...., check\_policy\_service unix:private/policyd-spf, ....

policyd-spf\_time\_limit = 3600  
  
Встановлення postfix-policyd-spf-perl   
Ви можете використовувати або python або perl версію.  
Можете встановити з портів FreeBSD, ports\postfix-policyd-spf-perl, make install clean. Або іншим способом.   
Зробити зміни у Postfix, master.cf додавши:  
 spf-policy unix -       n       n       -       0       spawn  
          user=nobody argv=/usr/local/sbin/postfix-policyd-spf-perl  
Зробити зміни у Postfix,main.cf:   
smtpd\_recipient\_restrictions = ...., check\_policy\_service unix:private/spf-policy, ....  
spf-policy\_time\_limit  = 3600  
  
  
  
Як працює SPF.  
У прикладі 1 ми бачимо авторизовано прийом листа з ip адреси за правилами що прописані у DNS TXT SPF:  
*host -t txt meta.ua  
meta.ua descriptive text "v=spf1 +ip4:194.0.131.0/24 ~all"*  

Приклад 1.

*Mar 21 11:34:42 postfix/policy-spf[19769]: : SPF pass (Mechanism 'ip4:194.0.131.0/24' matched): Envelope-from: nullsender@**meta.ua* *Mar 21 11:34:42 postfix/policy-spf[19769]: : Policy action=PREPEND Received-SPF: pass (meta.ua: 194.0.131.41 is authorized to use 'nullsender@meta.ua' in 'mfrom' identity (mechanism 'ip4:194.0.131.0/24' matched)) receiver=**mail.example.com.ua**; identity=mailfrom; envelope-from="nullsender@meta.ua"; helo=smtp.meta.ua; client-ip=194.0.131.41*  
  
Приклад 2, демонструє як спрацьовує SPF реалізація на спробу прислати листа за IP адреси що не прописана як дозволена у spf правилах для домену *meta.ua.*  
  

Приклад 2.

*Mar 20 03:02:46 postfix/smtpd[53863]: connect from lovikupon.net.ru[95.167.71.117]  
Mar 20 03:02:49 postfix/policy-spf[53868]: : SPF softfail (Mechanism '~all' matched): Envelope-from: redkit@meta.ua  
Mar 20 03:02:49 postfix/policy-spf[53868]: handler sender\_policy\_framework: is decisive.  
Mar 20 03:02:49 postfix/policy-spf[53868]: : Policy action=PREPEND Received-SPF: softfail (meta.ua: Sender is not authorized by default to  
use 'redkit@meta.ua' in 'mfrom' identity, however domain is not currently prepared for false failures (mechanism '~all' matched)) receiver=mail.example.com.ua;  
 identity=mailfrom; envelope-from="redkit@meta.ua"; helo=mail6.vish.ru; client-ip=95.167.71.117*

  
Перепризначення дії правил spf.  
Але є велике запитання що робити з такими листами?  
За spf правилом що надає домен meta.ua "*v=spf1 +ip4:194.0.131.0/24 ~all*"  
прописана дія ~all ([SOFTFAIL](http://en.wikipedia.org/wiki/Sender_Policy_Framework)), що означає примати листи але помічати як неблагонадійні.  
Але для свого сервера я бажаю вжити більш жорсткі правила, і тому листи з SOFTFAIL повинні бути відкинуті на етапі перевірки заголовку листа.  
Тому зробимо перевірку заголовка "Received-SPF" що є результатом роботи *policy-spf.*  
Створюємо запис у файі *header\_checks.pcre*  
*/^Received-SPF: softfail/i      REJECT SPF: softfail*  
У  файлі конфігурації postfix *main.cf* прописуємо:  
*header\_checks = pcre:header\_checks.pcre*  
  
Тепер ми бачимо у прикладі 3, як спрацьовує перепризначення дії правил: reject 5.7.1  
За необхідністю таким чином можна додати перепризначення і на дію SPF neutral.  
  
Приклад 3.  
*Mar 20 03:02:50 postfix/cleanup[53879]: E297139DF7: reject: header Received-SPF: softfail (meta.ua: Sender is not authorized by default to  
use 'redkit@meta.ua' in 'mfrom' identity, however domain is not currently prepared for false failures (mechanism '~all' matched) from lovikupon.n  
et.ru[95.167.71.117]; from=<redkit@meta.ua> to=<user@example.com.ua> proto=ESMTP helo=<mail6.vish.ru>: 5.7.1 SPF: softfail*  

Примітка: Таке перепризначення дії правил можна не робити якщо використовувати реалізацію "*postfix-policyd-spf-python*". Достатньо описати це у файлі конфігурації *postfix-policyd-spf-python/policyd-spf.conf,* дивіться повний опис у файлі *policyd-spf.conf.commented. Наприклад, HELO\_reject = SPF\_Not\_Pass, Mail\_From\_reject =  SPF\_Not\_Pass.*

  
У прикладі 4, Name сервер домену example.com взагалі видає помилку
на запит DNS TXT. Тому додамо виключення для домену example.com.  
  

Приклад 4.

*Policy action=DEFER\_IF\_PERMIT SPF-Result=example.com: 'SERVFAIL' error on DNS 'SPF' lookup of 'example.com'*

*reject: RCPT from mail.example.com: 450 4.7.1 <user@example.com>: Sender address rejected: SPF-Result=example.com: 'SERVFAIL' error on DNS 'SPF' lookup of 'example.com'; from=<user@example.com> to=<some@example.com.ua> proto=ESMTP helo=<example.com>*

  
Білий список  
У файлі конфігурації postfix *main.cf* прописуємо:  

*smtpd\_restriction\_classes = spf-sender, spf-policy*

*spf-sender = check\_sender\_access pcre:access\_spf\_sender.pcre*

*spf-policy = check\_policy\_service unix:private/spf-policy*

  
Створюємо запис у файі *access\_spf\_sender.pcre*:  

*#CLASS that Allow checking FROM and apply whitelist for spf policy checking*

*#/whitelist\.domain$/ DUNNO*  
*/example\.com$/* *DUNNO*

*/.\*/ spf-policy*

  
Таким чином створені два класи *spf-sender* та *spf-policy*.  
*spf-sender* перевіряє доменне ім'я адресата, якщо у ньому є ім'я на виключення, то спрацьовує правило [DUNNO](http://www.postfix.org/access.5.html) котре перериває перевірку поточних правил класу, і не виконує перевірку на SPF зовсім,  якщо немає такого то спрацьовує правило для усіх доменів з виконанням класу *spf-policy*.  
Клас *spf-policy* описує дію для реалізації spf у postfix, він може відрізнятися в залежності від реалізації виконаної на базі python чи perl.  
  
Якщо використовується  "postfix-policyd-spf-python" то білий список можемо налаштовувати у самому конфігураційному файлі *postfix-policyd-spf-python/policyd-spf.conf.*  
Whitelist, Domain\_Whitelist, Domain\_Whitelist\_PTR
