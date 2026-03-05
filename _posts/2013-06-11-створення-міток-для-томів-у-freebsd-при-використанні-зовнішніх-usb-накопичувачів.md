---
layout: post
title: "Створення міток для томів у FreeBSD при використанні зовнішніх USB накопичувачів"
date: 2013-06-11 18:08:00 +0000
tags: ["backup", "FreeBSD", "label", "mount"]
blogger_orig_link: https://lexxai.blogspot.com/2013/06/freebsd-usb.html
---

![USB Drive](/assets/images/blog/cd57e3aee64980f5-b543c9f10e67bd89.png "USB Drive")

![FreeBSD](/assets/images/blog/3b5d456ae2e09d37-d6724a32ac0eef39.jpg "FreeBSD")

Маю скрипти для резервування на зовнішні USB накопичувачі, але інколи нумерація підключеного пристрою змінюється, і треба змінювати скрипти для резервної копії.  
Тому можна скористуватися [мітками на том](http://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/geom-glabel.html). З версії FreeBSD 7.2 система автоматично створює унікальні мітки ufsid, що зберігаються у /dev/ufsid.  
  
Ознайомитися з переліком поточних міток використати [glabel status](http://www.freebsd.org/cgi/man.cgi?query=glabel&amp;sektion=8).  

```
% glabel status
Name Status Components
ufsid/486b6fc38d330916 N/A ad4s1d
ufsid/486b6fc16926168e N/A ad4s1f
```

Але цей ідентифікатор, унікальний для кожного пристрою, а так як у мене проходить ротація пристроїв, потрібна загальна постійна мітка для пристроїв у ротації. Створити постійну мітку без втрати даних для файлової системи UFS можна командою [tunefs](http://www.freebsd.org/cgi/man.cgi?query=tunefs&amp;sektion=8):
  

```
tunefs -L backup /dev/da5p3
% glabel status
Name Status Components
ufsid/486b6fc38d330916 N/A ad4s1d
ufsid/486b6fc16926168e N/A ad4s1f
            ufs/backup N/A da5p3
```

Для монтування використовуємо :  

```
mount  /dev/ufs/backup /mnt/usbstorage
```
