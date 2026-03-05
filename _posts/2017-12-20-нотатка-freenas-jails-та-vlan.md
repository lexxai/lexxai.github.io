---
layout: post
title: "Нотатка. FreeNAS. Jails та VLAN"
date: 2017-12-20 23:25:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/12/freenas-jails-vlan.html
---

Використовуючи у [FreeNAS](http://www.freenas.org/) ізольовані [Jails](https://www.freebsd.org/doc/handbook/jails.html) для ізольованих служб, на-кшталт [ownCloud](https://owncloud.org/), з'ясував що потрібно обов'язково визначати інтерфейс у налаштуванні відповідного jail  якщо мережевий інтерфейс є з [VLAN](https://uk.wikipedia.org/wiki/VLAN) і використовується [VIMAGE](https://wiki.freebsd.org/VIMAGE) .  

[![](/assets/images/blog/509114ee65d61a11-c9df00ae809608c6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSAczlfJTCynaBzcwLRKuHdhLr90EDW3yJjbENu3Bx4kBZFXU49oy8ERqVGFf9cLKAxxLvRix55ojrzIuO_C4HzxhOFQwTSP2XWRJd4DraboRAgDMQZFJcNdc0cm0UF-b_uXeuMF-s9C2s/s1600/nas-jail.PNG)  
*файліiface*

Робити це треба не через графічний інтерфейс, а у файлі конфігурації відповідного jail, у файлі ***iface*** у теці ***/mnt/{ZFSPOOL}/jails/.{NAME\_JAIL}.meta***.  
Після цього в графічному інтерфейсі можна побачити зміну значення NIC.  

[![](/assets/images/blog/8158f8b1e70eac7a-575417c880295aad.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjG9oB99MI7BXWuldlDjJukkW7-vJCxEEEArnp9-u2ePc06qUR9oMEob_cRUhS4Lexj6h5zaOBEzTP0BFddxnEf1Gdg4ZJVjvtxhCSpuH26sz7v6fBeurLf-L3jSiKVnynJdn2yKuMrRTSu/s1600/nas-jail-1.PNG)  
*Advanced GUI jail*

Після перезавантаження у мене почали іти пакети з jail через [VLAN](https://uk.wikipedia.org/wiki/VLAN) інтерфейс.
