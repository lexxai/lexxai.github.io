---
layout: post
title: "Гостьові Wi-FI мережі у OpenWRT"
date: 2011-10-18 14:12:00 +0000
tags: ["guest", "interfaces", "iptables", "linux", "openwrt", "Triolan Social", "WAN", "Wi-Fi"]
blogger_orig_link: https://lexxai.blogspot.com/2011/10/openwrt.html
---

Є такий соціальний проект як [Triolan Social WiFi](http://translate.google.com.ua/translate?hl=uk&sl=ru&tl=uk&u=http%3A%2F%2Fwifi.triolan.net.ua%2FDefault.aspx).  
Він об'єднує усі доступні клієнтські точки доступу що прийняли учать у цій програмі.  
Таким чином є можливість користуватися мережею WiFi на більшій території.  
Але чи є бажання пускати до себе в локальну домашню мережу усіх хто бажає.  
Тому відокремимо їх окрему мережу Triolan Social та надамо доступ тільки до WAN мережі.  
  

[![](/assets/images/blog/362edcd418d52f00-d468ebe0701da92e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiS2QQ3IEn2lPkp5qlW9vbsxJDSjSWbd93lN2MhdmvhvGigOHtL9G8DrXmKIa27Z0KAahi1zqfeXjywRFH0XlMuYZt8jFhAC8oudyJbYXR7YbiS2_d7983O7D62SgYMRjNO5pOk_8eg-hB0/s1600/guest-01.PNG)  
*Додамо новий інтерфейс*

 Створимо нову під мережу з ізольованою IP адресою 192.168.99.3/24  
  
  
  

[![](/assets/images/blog/565532bc953df88d-169e08296a25ec03.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpMB17DBXUkn32AhQZ-5ut5pb2sf67FH-0t0U8qL6UjNkpqJfEz-agfj-eWGMwyRElsslT77dz8IgNOSpnk592ajbf0HdtrzCVGH5O3doOFEPdvizR-9nJEjrSPa76V_IJ_FH3eaOKvo2A/s1600/guest-02.PNG)  
*Ім'я GUEST, фіксована IP адреса*

  

[![](/assets/images/blog/ca0a30f52513ab1f-0fda43301eb7ed68.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHYSscD4uzXJhSIBhJO1UOe1V_EgFr8KCpcbvBtECj0V9UN9tpZgGNPMlLIrHq1Fg_AnyY4-aemwgXDVIMxegAI0kOgHNksWzosA36OoF0xCtgPUNYBmaqoyRP2B2WSUTqmATsLAaC2cig/s1600/guest-03.PNG)  
*Отримаємо три інтерфейси*

 Створємо додаткову віртуальну WiFi мережу  

[![](/assets/images/blog/a5d83270925bd8bb-b2ec2e8523d689e4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGGtVPa4itWCbvQmDmwd1hiNI-I8iseWu20jl1AtHj0ols6GLF_zMmYjsE3s764xPSGwe3sRAP1_Hah-YzduW020zfgoYxxLi1EAmX1ZfuQtECMFTJDNV4qUV8bZKKp63eLXW8-hCICh8N/s1600/guest-04.PNG)  
*Створимо нову Wi-Fi мережу*

 Налаштовуємо додаючи ім'я   Triolan Social, додавши її до інтерфейсу "guest"   

[![](/assets/images/blog/959ccf3f08341307-47e11ec750ddd587.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhP6fuqh64kkzvfQ6qKksgPk7Mg3vL43j3TySKoX2FcyiPQSmBK9A-wKOKeVFvP0fF_1vlpNcbJFGRGsuYI8JUNGuMNM7l2TqZm2iYAGKaOEbNI6WMuxet4Ka7-7z016Q4ssJ2WJ7qRLgrI/s1600/guest-05.PNG)  
*WiFi "Triolan Social", network "guest"*

 Створюємо правила firewall, створивши нову зону "guest".  

[![](/assets/images/blog/77e525a0d6e910a6-efc58daec1f38cb0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2cX3Sz41yTZe8FxaK971a7KthxC4uKVUr7S7tVGTU5Ev5pIKgI6z6ySlUjH_W24r9coHLknI6cpJFWj_d6vZuoBkniQnIZ1tEtjPusiD_BeWunqk8CuFomIGbtO3kNqtf4FiTRwp5sj7N/s1600/guest-06.PNG)  
*Додамо нову зону firewall*

  
Налаштовуємо дозволи для зони "guest"  

[![](/assets/images/blog/97b2c0c5cd34de36-bc8ca14c0aa61e63.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKk8VrMFyMR8zra1WFv1TN-ZpgjDTi8t3i61H4_P4bopt1K2iCjKYzie70n-MRbW8g2Zp7A_2UY_jjm1klYcK18S-VcnowHguPt_ZpnPo1tXL09y-2OgQgNa3JO3w_oloR8qhEkGBkzKqR/s1600/guest-08.png)  
*Зона firewall GUEST*

[![](/assets/images/blog/b0a412c222a5c786-3b5d691ed1bc7f42.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiR35Gwnv1zGHRKOXk6OPYXPUIfkcF1zbBMSVjUqM62L0Q3ghtPYcdwipYI6RV0KlYREMv7y1bJEez0d_DUUDYh4ZQ-SGyt6jZO7yNItMruS5ykGcZNHA1m_vWUuBQHfTxHYM8JhW-BOKpZ/s1600/guest-07.png)  
*Усі зони firewall*

 Після цього у нас буле створена додаткова Wi-Fi мережа що має ізольований вихід до WAN.  

lexxai
