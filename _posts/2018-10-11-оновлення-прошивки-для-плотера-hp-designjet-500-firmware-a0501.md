---
layout: post
title: "Оновлення прошивки для плотера HP DesignJet 500 firmware A.05.01"
date: 2018-10-11 21:59:00 +0000
tags: ["firmware", "HPDesignJet500", "printhead"]
blogger_orig_link: https://lexxai.blogspot.com/2018/10/hp-designjet-500-firmware-a0501.html
---

Закупили нові головки для плотера HP DesignJet 500, на одній з них був червоний стікер  котрий попереджував про необхідність  прошивки  (firmware) до  A.05.01.  
Спробували проігнорувати це попередження як завжди :) Але головку таки плотер не прийняв.  
  
На сайті HP є стаття про причини і необхідність оновлення -  [HP DesignJet 500, 510 and 800 Printers - HP 11 Printhead Errors.](https://support.hp.com/us-en/product/hp-designjet-500-printer-series/25301/document/c06098737/)  
  
Тому вирішили оновити, завантажили програму для оновлення прошивки на сайті hp: [HP DesignJet 500/800 Firmware Upgrade Tool A.05.01](https://ftp.hp.com/pub/softlib/software13/printers/HP_DesignJet_500_800_FU_3.8.zip).  
  
Але...  
  
Спробували оновити прошивку на Windows 7 x64,  Windows 10 x64 результат той самий - пише що все гаразд, дивіться на дисплей пристою на подальші інструкції.  
  

[![](/assets/images/blog/10173a14a21e3e18-a796eae4b547e7b4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMnyJWJagkuqUw3z_26ExH9jo_YjP1WX2EJSq3rbxNgesSf6_hpxBHTAOsb3WDI0owkyyJVCZ3oOnLVWVzXR5jhmfQF7DzVdqpC6oq3gkoRj5X6IaavegFk3yqKu-QaFVZnwH7RN9Wto0w/s1600/scr1.png)  
*Підготовка до завантаження прошивки до пристрою*

[![](/assets/images/blog/0eca8707d57a7e64-c98f93ff85a10672.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTnHos4mw3bcOO2z6YhojqeEJFsQKEFjePM4GqbRkA9n_5SLjZS_GBG6jLc5_0v-cdbl2or5dOvkUAnrFywbY9TboJOsEwYUCyd4wpGer9FWZG9nSE6DRXXSTHnx77wm9nAIxIeJ_j6-nS/s1600/scr2.png)  
*Звіт про те що все гаразд*

Після звіту що все гаразд, чекали і чекали - нічого. Спробували інший кабель USB, інший "чистий" комп'ютер, нічого. Інтернет пошук знаходить тільки запити від користувачів.  
  
Остання ідея була спробувати старий комп'ютер з Windows XP, встановили драйвер [х32 8.10](https://ftp.hp.com/pub/softlib/software13/printers/COL25754/hpdj510wumglen.exe), (може треба кому:  [x64](https://ftp.hp.com/pub/softlib/software12/COL48412/pl-117454-1/hpdj510wx64glen.exe)), [сховище драйверів HP](https://support.hp.com/us-en/drivers/selfservice/closure/hp-designjet-510-printer-series/3737526?ssfFlag=true&sku=).   
І почали оновлення, і о чудо, у нас запитала програма, що додатково потрібно дати мережевий доступ до принтера, надали.  
Зачекали закінчення роботи програми  [HP DesignJet 500/800 Firmware Upgrade Tool A.05.01](https://ftp.hp.com/pub/softlib/software13/printers/HP_DesignJet_500_800_FU_3.8.zip) і побачили таки не дисплеї плотера процес оновлення і дочекалися зображення що показує на виключення пристрою з електричної мережі, вимкнули / ввімкнули і отримали версію A.05.01.  
  
Велика дяка  Windows XP!
