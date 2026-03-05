---
layout: post
title: "mail server Cyrus - imap index"
date: 2013-02-13 22:52:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/02/mail-server-cyrus-imap-index.html
---

Для прискорення пошуку у IMAP теці, треба зробити індексацію поштової скриньки користувача  

```
/usr/local/cyrus/bin/squatter -v -s -r user
```

Для періодичного індексування о 2-гій годині
до cyrus.conf додамо у блок
  

```
EVENTS { 
...
squatter cmd="squatter -s -r user" at=0200
...
}
```

Буває так що індексація припиняється бо сталася помилка

То можна спробувати відновити за допомогую команди  

```
/usr/local/cyrus/bin/reconstruct
```

```
/usr/local/cyrus/bin/reconstruct -r user/someone
```

Пошук користувачів:
  

```
find "/var/spool/imap" -type d -regex '.*/user/.*' -maxdepth 3 | cut -d'/' -f6,7
```

Цей скрипт знаходить користувачів і по кожному з них робить індексацію, при цьому якщо буде помилка у одному з користувачів то обробка інших не припиниться.  

```
#!/bin/sh
find "/var/spool/imap" -type d -regex '.*/user/.*' -maxdepth 3|cut -d'/' -f6,7|while read str;do \
/usr/local/cyrus/bin/squatter -s -r $str;
done
```
