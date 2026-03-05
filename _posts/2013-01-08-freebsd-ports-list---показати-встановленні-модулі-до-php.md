---
layout: post
title: "freebsd ports list - показати встановленні модулі до php"
date: 2013-01-08 19:14:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/01/freebsd-ports-list-php.html
---

Приклад, показати встановленні модулі до php. Щоб встановити подібні на інші машині.  
  
#php -m  
[PHP Modules]  
  
  
apc  
bcmath  
bz2  
ctype  
date  
ftp  
gd  
iconv  
imap  
json  
ldap  
libxml  
mbstring  
mysql  
pcntl  
pcre  
PDO  
pdo\_mysql  
pdo\_pgsql  
pgsql  
pspell  
Reflection  
session  
shmop  
SimpleXML  
soap  
SPL  
ssh2  
standard  
sysvsem  
sysvshm  
xml  
xmlrpc  
zip  
zlib  
  
  
%pkg\_info -o php\\* | grep -A1 'Origin:' | grep -v 'Origin:' | grep -v "\-\-"  
lang/php52  
math/php52-bcmath  
archivers/php52-bz2  
textproc/php52-ctype  
lang/php52-extensions  
ftp/php52-ftp  
graphics/php52-gd  
converters/php52-iconv  
mail/php52-imap  
devel/php52-json  
net/php52-ldap  
converters/php52-mbstring  
databases/php52-mysql  
devel/php52-pcntl  
devel/php52-pcre  
databases/php52-pdo  
databases/php52-pdo\_mysql  
databases/php52-pdo\_pgsql  
databases/php52-pgsql  
textproc/php52-pspell  
www/php52-session  
devel/php52-shmop  
textproc/php52-simplexml  
net/php52-soap  
devel/php52-spl  
devel/php52-sysvsem  
devel/php52-sysvshm  
textproc/php52-xml  
net/php52-xmlrpc  
archivers/php52-zip  
archivers/php52-zlib  
  
Інші методи:  

* [portmaster --list-origins > ~/installed-port-list](http://forums.freebsd.org/showpost.php?p=171830&postcount=6)
* [pkgng: First look at FreeBSD’s new package manager](https://mebsd.com/make-build-your-freebsd-word/pkgng-first-look-at-freebsds-new-package-manager.html)
* pkg\_create ... pkg\_add ...
