---
layout: post
title: "Завантажжeння через USB для OPNsense проблема GPT"
date: 2018-09-05 10:07:00 +0000
tags: ["boot", "FreeBSD", "gpart", "gpt", "opnsense", "usb", "VMware player"]
blogger_orig_link: https://lexxai.blogspot.com/2018/09/e-usb-opnsense-gpt.html
---

[![](/assets/images/blog/39974404977fffe6-ec10c9360dd95ec3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtYFcERZvixIy7fbTFh_rFQ4e8vO0VwNSkLGSn_uz675rWjeuaI_SHI27uo4SNUY4rQSlFV03GCtFGb4f0SJhF0aPaeaC3t9WCHqRbORtlzmhpjQZk-oL0665bWQRIcbiT_wqzbt0nfbha/s1600/boot1.PNG)

  
Працював давно вже  OPNsense на старенькій машині i386 де IDE диск 8Гб.  
Почалася почалась проблема з диском, вирішив перевстановити, на новий такий же старий диск :)  
Але наткнувся на проблему створити завантажувальний USB диск з дистрибутиву https://opnsense.org/download.  
Беру образ IMG: OPNsense-18.7-OpenSSL-vga-i386.img.bz2 записую через rufus 3.1, все проходить гаразд.  
Завантажуюсь - і зразу  проблема з GPT, і перезавантаження.  
Гаразд, спробую образ для DVD  OPNsense-18.7-OpenSSL-dvd-i386.iso.bz2, rufus каже підтримується цей формат.  
Спроба згідно документації інший метод:  

```
physdiskwrite -u OPNsense-18.7-OpenSSL-vga-i386.img
```

Результат той самий - проблема GPT.  
Один з топіків фoруму [opnsense.org](https://forum.opnsense.org/index.php?topic=4011.msg14437#msg14437) спробував з робити диск з самої oopnsense або freebsd чи linux за допомогою  dd.  
Гаразд, піднімаю VMware Player, підключаю пристрій USB контролер і потім підмонтовую USB диск, завантажуюсь з нього через ISO, встановлюю на віртуальний диск, дозволяю SSH, і через WinSCP записую образ OPNsense-18.7-OpenSSL-vga-i386.img у теку  користувача /root, далі через PUTTY підключаюсь до консолі SSH, через dmsg  визначаю пристрій для USB у мене це /dev/da0. І записую образ на USB disk.  

```
dd if=OPNsense-18.7-OpenSSL-vga-i386.img of=/dev/da0 bs=16k
```

По закінченню бачу помилку:   

```
GEOM: da0: the secondary GPT header is not in the last LBA.
```

Вирішую глянути на таблицю GPT.  

```
gpart list da0
```

Показує що є проблема. Спробую відновити:  

```
gpart recover da0
gpart list da0
```

Все гаразд тепер після відновлення.  
Перевіряю завантаженням з USB диска у VMWare player. Так як це не можливо засобами player, використовую iso що дозволяє завантажуватися з USB всередині віртуальної машини [Plop Boot Manager](https://www.plop.at/en/bootmanager).  
Система завантажилася...
