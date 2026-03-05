---
layout: post
title: "HOWTO: PHP IMAP"
date: 2011-07-18 08:59:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2011/07/php-imap.html
---

FreeBSD 8.1  
  
Трапилася проблема компіляції php5-imap з портів FreeBSD  
  
  
  
> checking whether build with IMAP works... no  
> configure: error: build test failed. Please check the config.log for details.  
> ===>  Script "configure" failed unexpectedly.  
> Please report the problem to ale@FreeBSD.org [maintainer] and attach the  
> "/usr/ports/mail/php5-imap/work/php-5.3.6/ext/imap/config.log" including the  
> output of the failure of your make command. Also, it might be a good idea to  
> provide an overview of all packages installed on your system (e.g. an `ls  
> /var/db/pkg`).  
> \*\*\* Error code 1

Проблема була з  версіями  
> /usr/local/lib/ libc-client\*

  
 Виправлення, переставив порт    
> /usr/ports/mail/cclient

cd  /usr/local/lib

ln -s libc-client4.a libc-client.a  
ln -s libc-client4.so libc-client.so  
ln -s libc-client4.so.9 libc-client.so.9  
ls -l libc-client\*  
lrwxr-xr-x  1 root  wheel       14 Jul 18 11:45 libc-client.a -> libc-client4.a  
lrwxr-xr-x  1 root  wheel       15 Jul 18 11:46 libc-client.so -> libc-client4.so  
lrwxr-xr-x  1 root  wheel       17 Jul 18 11:46 libc-client.so.9 -> libc-client4.so.9  
-r--r--r--  1 root  wheel  2245194 Jul 18 11:44 libc-client4.a  
lrwxr-xr-x  1 root  wheel       17 Jul 18 11:44 libc-client4.so -> libc-client4.so.9  
-r--r--r--  1 root  wheel  1851339 Jul 18 11:44 libc-client4.so.9

  
  
І тепер компіляція php5-imap пройшла успішно.
