---
layout: post
title: "Amavis та SpamAssassin і помилки при використанні"
date: 2012-07-11 19:37:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/07/amavis-spamassassin.html
---

<http://skeletor.org.ua/?p=288>  
  
Ось деякі ціні рішення з описаних проблем автором [skeletor](http://skeletor.org.ua/?author=1 "Посмотреть все записи автора skeletor").   
  
*Рішення:*   
#perl -MCPAN -e shell  
>install Bundle::CPAN  
>reload cpan  
>exit  
  
*Рішення:*  
#sa-compile  
#sa-update -D  
  
*Рішення:*   
Добавити користувача **vscan** до групи **clamav** та користувача **clamav** до групи **vscan﻿**   
  
  
*Рішення**:*  
Запускати **spamassasin** від користувача **spamd:spamd** та встановити права рекурсивно на теку **/var/spool/mqueue/.spamassassin/** **spamd:spamd**, **0770**
