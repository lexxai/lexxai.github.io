---
layout: post
title: "Як зробити використовуючи pfSense, FreeRadius, дозвіл на WiFi WPA-Enterprise авторизацію тільки на певній WiFi точці доступу"
date: 2019-01-31 22:58:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/02/pfsense-freeradius-wifi-wpa-enterprise.html
---

Є задача : обмежити авторизацію [WiFi](https://uk.wikipedia.org/wiki/Wi-Fi) користувача котрий використовує [WPA-Enterprise](https://en.wikipedia.org/wiki/Wi-Fi_Protected_Access) з можливістю авторизуватися тільки на певній WiFi точці доступу (Access Point).  
Реалізація за допомогою модуля [FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS) [у pfSense](https://en.wikipedia.org/wiki/PfSense).  
Тестова WiFi точка доступу (MAC:00-10-A4-23-19-C0) підключена до [FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS) у розділі NAS/Clients з назвою "AP1".  
  
Для реалізації обмежень можна використовувати  radius атрибут: [Called-Station-Id.](https://freeradius.org/rfc/rfc2865.html#Called-Station-Id)  
Атрибут "Called-Station-Id" передається до [FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS) від WiFi точки доступу  під час підключення користувача до точки доступу WiFi.  
Якщо запустити [FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS) в консолі в режимі діагностики: freeradius -x, то можна бачити усі використанні атрибути при підключенні клієнта.  
Так для тестової WiFi точки доступу (AP1) атрибут "Called-Station-Id" є "00-10-A4-23-19-C0:AP1".  
Перевірку цього атрибута при підключенні користувача можна додати у розділі  Users, Additional RADIUS Attributes (CHECK-ITEM).  
  

[![](/assets/images/blog/72c7f654ebae2aba-5e0a860d1ccd7276.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh82SvDf_41wGncNkBEpc_XoVGpqNVKtql61QR6_Q0mp9ISjySJX2xBvDdNJPzq8zfAxleBXvfVMYmKRZRL4Ia3cz1yI_YbEY9ANftoLOI9ifr4LKSuq8Z45VVG6BgGtqNay3umOxnc1dPH/s1600/wifrad-01.PNG)

  
  

[![](/assets/images/blog/ac4d209fa5813ea8-82022dc91dab0b0f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkzWgMg_2yDWpmmFeJo7DKv_NPUaBlljbhrdhikgu8H8SpmouYO5uo7wL8nR6cFXgiMKN01moHbLvN9Mk2vw4h4G4CrFsw_1VqGgQbMvVSuK3alLuT7nrQcm-ez0SyL7-ppjwm6UV1xtDn/s1600/wifrad-02.PNG)  
*Users, Additional RADIUS Attributes (CHECK-ITEM).*

  
В результаті як це записано у файлі конфігурації [FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS) можна переглянути у розділі View config, Users.  
    

[![](/assets/images/blog/5cc4861ab941a36b-6bf8743c85134dc5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3jFVzDMO97EidYYVj2-eJWBwoLFs6cn-GjIlLuP2XzDAt-FgB4dmcz6o4gDj1nL1B9-htV1H1K_LtYPaOf0_2vGslTr6iywBeM857ha5WCTV__3gYdxbAFtwpdMZm8axTUKO1Kmihlbyb/s1600/wifrad-03.PNG)

  

[![](/assets/images/blog/6f3ae4d5695ed5c3-58d277a807f4f8c6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4DTF17BMKmkS6E9m7sVNwfYvylrWbtgPAam51JIxcr0DqHFKSl6mVfKPn2me4H206QWNHfhZZzSjvfdpU71WbJDOsBkx7uwPy9RHaLg6Nlgfx07jAwuGkFeRJiom8_uz0N0deFFzTSLlQ/s1600/wifrad-04.PNG)  
*View config, Users*

Але для функціонування перевірки атрибута "Called-Station-Id", після авторизації (post auth), якщо використовується  аутентифікація EAP-PEAP, EAP-TTLS цього не достатньо. Тому що атрибути автоматично не передаються у вкладенні тунелі EAP, про що написано в описі pfSense (Copy Request to Tunnel - The tunneled authentication request does not usually contain useful attributes like 'Calling-Station-Id', etc.
These attributes are outside of the tunnel, and normally unavailable to the tunneled authentication request.
By setting this configuration entry to 'Yes', any attribute which is *not* available in the tunneled authentication request,
but which *is* available outside of the tunnel, is copied to the tunneled request.), та у [How to use Calling-Station-Id on a per user basis in freeRADIUS?](https://serverfault.com/questions/567130/how-to-use-calling-station-id-on-a-per-user-basis-in-freeradius)  
Тому  необхідно ввімкнути "Copy Request to Tunnel" у розділі  EAP.  
  

[![](/assets/images/blog/52f99e51b8024325-9ad2babab2103a75.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2QjdNf96EFuYNwlE1J0O8_RDH0ufP14NbcRmA-iw1O69l0hN4TxiTaTpbPiKro4bOKNBWh7zJlNziTL0qMej_MG5Luq_fOoKWI_xQzrJuYW8rOp8_372cvCvmkp4kaJ5GB203YMAXveVu/s1600/wifrad-05.PNG)

  

[![](/assets/images/blog/6b2b0c9640094ad0-1db6db5e7978e968.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjROT0XK1MRj-6btJJuRSGS9BKDz2kUsLcjexQZH4nBRitYP2_8ca_XNe4tmrnY9NmL0xMz1Re0nI2jGNcZP-01C1vZaRIeqGynVdwvnL5yA1V1JyDuGFS7LqGFb0MbOn1dqsfL7Y0LpfL8/s1600/wifrad-06.PNG)  
*"Copy Request to Tunnel" у розділі  EAP.*

  
Після цього авторизація користувача "wifi\_test\_user" пройде тільки у випадку підключення до визначеної точки доступу "AP1".
