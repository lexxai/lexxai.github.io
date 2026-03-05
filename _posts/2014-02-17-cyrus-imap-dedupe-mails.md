---
layout: post
title: "Cyrus IMAP dedupe mails"
date: 2014-02-17 20:03:00 +0000
tags: ["cyrus", "FreeBSD", "script"]
blogger_orig_link: https://lexxai.blogspot.com/2014/02/cyrus-imap-dedupe-mails.html
---

[![](/assets/images/blog/0a2d1cde3dc21849-3b15b3be24d721f6.png)](http://upload.wikimedia.org/wikipedia/commons/thumb/5/54/Internet-mail.svg/48px-Internet-mail.svg.png)

  
Поштовий сервер Cyrus. OS FreeBSD.  
Задача видалити, дублікати листів у поштовій скрині 'test2' користувача 'some'.  
  
  
З системи портів встановлюю "fdupes"  

```
cd /usr/ports/sysutils/fdupes ; make install clean
```

Створюю скрипт обробки для фіксованої теки котра описанна змінною 'fpath'. Де опрацьовується тека на дублікати, вони видаляються, а поштова тека користувача реконструюється.
  

```
#!/usr/local/bin/bash
# lexxai.pp.ua, 2014
fpath=/var/spool/imap/s/user/some/test2
rpath=`echo $fpath| /usr/bin/cut -d'/' -f6,7`
echo Dedupe this folder: $fpath;
/usr/local/bin/fdupes -d -n -N ${fpath} |grep '[-]'
echo Cyrus reconstruct folders of user:  $rpath;
su cyrus -c '/usr/local/cyrus/bin/reconstruct -r '$rpath
```
