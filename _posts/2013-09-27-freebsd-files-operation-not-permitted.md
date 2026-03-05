---
layout: post
title: "FreeBSD files operation not permitted"
date: 2013-09-27 18:07:00 +0000
tags: ["file", "FreeBSD"]
blogger_orig_link: https://lexxai.blogspot.com/2013/09/freebsd-files-operation-not-permitted.html
---

Іноді не буває можливості робити операції з файлами тому що встановлений "[set the system immutable flag](http://www.freebsd.org/cgi/man.cgi?query=chflags&sektion=1)"  
  
Можемо прибрати цей флаг командою:  

```
chflags -R noschg *
```
