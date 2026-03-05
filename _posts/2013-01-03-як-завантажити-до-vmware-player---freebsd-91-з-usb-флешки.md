---
layout: post
title: "Як завантажити до VMware player - FreeBSD 9.1 з USB флешки."
date: 2013-01-03 23:00:00 +0000
tags: ["boot", "FreeBSD", "usb storage", "VMware player"]
blogger_orig_link: https://lexxai.blogspot.com/2013/01/vmware-player-freebsd-91-usb.html
---

Для перевірки чи працює завантаження з флешки, хотів перевірити її у віртуальній машині, але з'ясувалося що VMware player, не має можливості завантажувати операційну систему з Flash disk.  
Тому і знайшов [просиляння](http://theholyjava.wordpress.com/2010/06/10/booting-from-a-usb-stick-in-vmware-player/) на  [Plop Boot Manager](http://www.plop.at/en/bootmanagers.html), і використав його.  
  
1. Завантажую образ FreeBSD 9.1 для флешки (FreeBSD-XX-RELEASE-i386-memstick.img), <http://www.freebsd.org/where.html>  
2. Записую образ на флешку, за допомогою [Image Writer for Windows.](https://launchpad.net/win32-image-writer/)  

[![](/assets/images/blog/5bbb62b5245e07a0-0a205d3fcdc01dc2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTpME3sRSitQO_HV0FUsaivtu_gq7Po8qnkWGMILp9fxqeiJymGgONprMhC1Qcdp_LuSPM3jXF41r0g3-atdLW2_kAkuQcjYBzAC-5P9UHkuaf_fbv7IuYMS8nsf4D68ODBpPyrKpEitew/s1600/vm-usb-09.png)  
*Запису образу на флешку*

3. Завантажую [Plop Boot Manager](http://www.plop.at/en/bootmanagers.html), розпаковую. І мені потрібен файл "plpbt.iso".  
4. Налаштовую VMware player, віртуальний CD/DVD з iso файлу: "plpbt.iso".  

[![](/assets/images/blog/717a1de06f3c2877-710f00159bf514d0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiY5g-8rovJ4TKQoiHZw79fP-GtWhuNee0kx-BOZXH8AaAC4Zz9kUjaJt7YJ0zr4B1I1u0OUz0rYcBmSrrpIzIXlF1Nev_ByqUwH_Y8KT78XnkyzFnT3Nqb2TEY7O64k8RNHhcTgmlqhjVd/s1600/vm-usb-01.png)  
*Налаштування віртуальної машини "freebsd usb boot"*

[![](/assets/images/blog/b4e9911cce6f5412-8f339152c8101c87.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi54mn2qrdToXir4eeUA7HOUAIfI2CAHyjALW9JMhY9HkssBAVOnGd6_Ls0vZZTFWP0gmRcfai9IDfettqgGYy4jV5Pt3FQ1ADh_2iFn1BUjvN1cgN8P_kvqPDgudGgEMG24wUIw-McMoUi/s1600/vm-usb-02.png)  
*Запускаю вибрану віртуальну машину "freebsd usb boot"*

[![](/assets/images/blog/81fef62d920c29ee-160519a6c077101f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-oqaqYv468Znhq2llojyRrO9jjToHDgQk_dsPeMNU4MAeVbJEi6-nGfZNnN_PC_2cSmcgHzGDlaCO0a0Ot3GQDgrkI2rqEcGqPw8iRvPplqTGZSheLy8TnMgfu7bhPA-EldGHly-qzc9M/s1600/vm-usb-03.png)  
*Під'єднання USB пристрою*

[![](/assets/images/blog/4b25c630a1f1f917-fda2e73794417420.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhul-7ujQljnaS2Dd3AEYQzgdWe_BxwYVwGTL_JvMOabwZhKxyzCNG2GyZoIk8htkOeUbdkguR-f93JG7QrAwKMxPn-2tEdvrWv5VP7Nry_BAnpDAUEGEqxx6XFLgllDOYab9uLm5iOaCpm/s1600/vm-usb-04.png)  
*Під'єднано USB drive*

[![](/assets/images/blog/29257a856497b2a7-1e3137fed85f3306.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgkOaPs8-GXdcDJt7smrsL4hjOkdINYgs59gNeU-IOlz8crSxDiECSwhLzfROSi1uJV3Z-qHrbI0BvG7eGRpif68H9RQE8wUQC2-4ng63O0LF-7E1_EZwdEOW77JFQvSTtaqidl53IrYy_-/s1600/vm-usb-05.png)  
*Вибираємо звідки завантажуватися*

[![](/assets/images/blog/101d098c742e61c2-9d1b332a7b8523b3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiM3AvjjDEEj_ulVthscJBtAVBxZyXn4Svxc1M2BzsZtBIjVpxiUXFyaGGIlxo6cUvw18S20tCVNeq7J9bcoq2aYgxeKTE6AwV4JutVBll4fpBhWQbLQ85Zm1YONq5TmZ3AGSoaCZMrahUG/s1600/vm-usb-07.png)  
*FreeBSD завантажується...*

[![](/assets/images/blog/4f12d5c8ec3224ca-4efcbe029daf50ec.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8axOvJJCAYEoj9cM0Bpjh51hMg3P1G8kWbl3fYR5v-LLeIUBiMpJqPP0dIhSxCy0UTh-rxZ2D5c6QcU9s2Ql767ABGWqVMnbxzXr3jDPTOsgsp0i94q7re9YJArpnYKofUafF9h6LSutC/s1600/vm-usb-08.png)  
*FreeBSD завантажено*
