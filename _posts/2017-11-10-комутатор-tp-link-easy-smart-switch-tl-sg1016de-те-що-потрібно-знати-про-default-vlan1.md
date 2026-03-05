---
layout: post
title: "Комутатор TP-Link Easy Smart Switch TL-SG1016DE те що потрібно знати про (default) VLAN1"
date: 2017-11-10 19:52:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/11/tl-sg108e-default-vlan1.html
---

[![](/assets/images/blog/b6531053fa15536d-ad1c24fc42fd4554.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipJieb61qf5qXIYSYGZOUXwOAo9-USKiOCPzPtWaEqJWHsmCFOXQjytapR1nn0ICp6jm88uUPo2CusXc6hoV8B8bpZ-1kfeBi3xqCZyhEat89NepuBvotfirF243baIAo5U20exMKCNNyc/s1600/logotplink.png)

Є така лінійка комутаторів від компанії [TP-Link](http://www.tp-link.ua/uk) як [Easy Smart Switch](http://www.tp-link.ua/uk/products/biz-list-41.html).

Все б не погано якби продавець попереджував про обмеження "початкової" лінійки, Easy Smart Switch, і про те що таких обмежень не має у лінійках для корпоративного рішення.

Почалося з того що було придбано 16-портовий гігабітний комутатор серії Easy Smart [TL-SG1016DE.](http://www.tp-link.ua/uk/products/details/cat-41_TL-SG1016DE.html)

Вивчаючи можливості застосування в своїй корпоративній мережі, з'ясувалося що якщо увімкнуто режим 802.1Q VLAN Configuration, то кожному з портів є можливість назначити VLAN ID тільки  у проміжку від 2 до 4092. А де №1 ?

  

### Знайдено

Ось і секрет цієї моделі у тому що ***VLAN ID=1*** - фіксований, і до нього належать усі порти автоматично і це ніяк не вимикається.

### *Не можливо видалити, або змінити VLAN ID 1*

### Що це дає ?

Це дає те що з кожного з портів комутатора доступний web інтерфейс адміністрування комутатора, наприклад за адресою http://192.168.0.1. Це напевно зроблено щоб домашній користувач не зміг заблокувати сам себе.

Але якщо, наприклад, є порт WAN  і він використовується як вхідний у цей комутатор, то у випадку коли мережевому адаптеру що підключений  до  WAN прописати IP адресу з однієї мережі що і комутатора наприклад 192.168.0.200, то за адресою http://192.168.0.1 буде отримано web контроль над комутатором з завідома не дружньої мережі WAN.

### 

### Ну і що, є пароль адміністрування - комутатор захищено

Так, нехай пароль буде складний, але ж його можна потихеньку підібрати.

Ну це ще так сяк, але ніхто не знає те що для налаштування VLAN портів комутатора не потрібен пароль зовсім. У підтвердження цього ось мій пост на форумі виробника:  [TL-SG1016DE security of changes value without any authentication.](http://forum.tp-link.com/showthread.php?91420-TL-SG1016DE-security-of-changes-value-without-any-authentication.&p=189032#post189032)

Приклад, формується запит: [http://192.168.0.1/vlan\_8021q\_based\_set.cgi?qvlanid=314&qvlanname=Ate
st314&tag\_1=1&tag\_2=2&tag\_3=2&tag\_4=2&tag\_5=2&tag\_
6=2&tag\_7=2&tag\_8=2&tag\_9=2&tag\_10=2&tag\_11=2&tag\_
12=2&tag\_13=2&tag\_14=2&tag\_15=2&tag\_16=0&addModify
=+Add%2FModify+](http://192.168.0.1/vlan_8021q_based_set.cgi?qvlanid=314&qvlanname=Atest314&tag_1=2&tag_2=2&tag_3=2&tag_4=2&tag_5=2&tag_6=2&tag_7=2&tag_8=2&tag_9=2&tag_10=2&tag_11=2&tag_12=2&tag_13=2&tag_14=2&tag_15=2&tag_16=0&addModify=+Add%2FModify+)

І зміни застосовуються без  запиту авторизації.

### 

### Які рішення?

#### PVID

Як одне з простих рішень це прописати  [PVID на інше значення ніж 1 на потрібному порту, наприклад де WAN, це вирішить питання з UNTAGED пакетами і відправить відповідь до іншого VLAN](javascript:void(0)).  Але це рішення для UNTAGED підключень, а якщо форсувати  на мережевій карті потрібний VLAN ID=1, то це рішення буде не ефективне.  

[![](/assets/images/blog/7c1f637b06951c54-4ec04c00d86ffc42.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwzPynZYj-cCKXEFeNPxIf-hDqnQ5OH0bb3IluYwCoXVNYFGfNGBdEXOU-X4Dads1iRgQw5ogcPM7cDogf-w0ehIPNxyCISrCnvc1y5n7wGXTfM8OpS10cFfmw5biE3WpD2tdoj7mtMx8c/s1600/tp_vlan1_pvid.png)  
*PVID початкові значення*

#### Config HACK

А ось сьогодні отримав рішення від користувача ***tpham3783***: [Here is how you can hack ( un-member ports on vlan1).](https://forum.pfsense.org/index.php?topic=123324.msg763557#msg763557)

Це дозволяє змінити фіксування VLAN ID=1, для всіх портів.  

[![](/assets/images/blog/69e31add8f60097c-fd27dc412e78276d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgkwpansfr7-BRmcDlZ9ZBNSYg1tA9Jf1zK_5ule8UK-JnoOXcs1WtjSbH7yg9OuJSNPqFYpnz3wJUkXGvSHUyeGa20B_sy8yLr1xxJZS-hDeDIJusCGmK9BL6j2qH468xFN_c5EF8J_x1l/s1600/tp_vlan1_back.png)  
*Config Backup*

*Для версії SG108PE (hw version 3):*  
  
Для цього зберігається файл конфігурації комутатора для версії SG108PE (hw version 3), у бінарному редакторі правиться значення після тексту "Default\_VLAN", там повинно бути прописано FF. Якщо змінити значення FF на 00, то це вимкне Default\_VLAN ID=1 на всіх портах. А якщо прописати 0х80 (1000 0000) то дозволить Default\_VLAN ID=1 на порту №8.

[![](/assets/images/blog/a80602d0f1fd3407-be5615f0a36283d1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNagY6GL1myloUq8NUf80n7zeW4jMeVB2hxor78gbBvWoPbdR89B-713vtWoMSRVdvctcI0VJMCXi-N7Ve4fEOmeHf_onWJdVGCyAsBAlxqTybAgexrSpiPz0K0CZqVCPRYA1JlF1wNDsc/s1600/tp_link_unmember_vlan1_hack.png)  
*Редагування файлу конфігурації SG108PE (hw version 3)*

Якщо після редагування повернути файл до комутатора, то отримаємо це:

[![](/assets/images/blog/a4631497cccc7a5c-2fbb98157ce260e5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgRId9lYlyAzCRYaNSbbFGcD3dgR_5KSV38dBnzBwjv0_CxQBeGlDs0ACp1pwpgqpjGfizCQX3wYwird_eEbwRRTveCIMAQwNDoHtFa7_dsHBRsEMLVXEkAoinennyVC0g_JYiEDu1EcH1P/s1600/tp_vlan1_disable_all_ports.png)  
*Після редагування файлу конфігурації. SG108PE (hw version 3)*

Можу поки що проінформувати що рішення не перевірено мною персонально, але файне рішення.  
**Аналізую...**   
*Для версії SG1016DE (hw version 2):*  
vlan:777,port: 5tag, name: TESTVVV  
777 = 0x0309 (0x09 0x03)  
5 =  0x10  (0001 0000) 5 bit.  
  

[![](/assets/images/blog/da659c16f0ac2866-49a06ca5be033e2c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhR50CMNXxonLUtEOX3eefWWkDG_F_-aaFLA5HOTPqSzCSYGiK7bxeiNpF0NN1AHiQrEWjKl1Edp7J09AFPvbRO7mbwExdQETECZbR-puwE8acXowpbe9JcdfMGFyC51yL6TodG3o5yZUom/s1600/tp_1016de-v2-vlan1_vlav777%25285tag%2529.png)  
*TP\_1016DE (v2) vlan:1, name: Default. vlan:777,port: 5tag, name: TESTVVV*

  
vlan:777,port: 5untag, name: TESTVVV  
777 = 0x0309 (0x09 0x03)  
5 =  0x10  (0001 0000) 5 bit.  
  

[![](/assets/images/blog/23f577a5d3ea5eb8-0384fc7a4cdc305d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmFqoMhANG-DjzGXD2LSHpcX5PwhLVou8OByf4o4MrnLEZBZw_PTgDXPVk1BbOvEMN2kxDfdz0q_td0UWmGq5bbfQhfeRnJLPZYRVLZtRd27bJ6yCMQkE7WPrE62nQUhYf6iiCPHLojSmA/s1600/tp_1016de-v2-vlan1_vlav777%25285untag%2529.png)  
*TP\_1016DE (v2) vlan:1, name: Default. vlan:777,port: 5untag, name: TESTVVV*

[![](/assets/images/blog/887ff570fe9d901f-d2b60d51fdf4b37c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyUt0oZXX70PT47zkOhFCzEMmUwzw_gc5nHdnLfpJ4CndV7e87MRQ3tKPdKRUbMJ-vITW7XYSpbKkx-cCqyudU_3OPYJetZltMV1rYfIqumghJm4FZ9sMH-XpojA1ICsQ1dAcuk7LG2yt8/s1600/tp_1016de-v2-vlan1_vlav777%25285untag%2529-conf.png)  
*TP\_1016DE (v2) vlan:1, name: Default. vlan:777,port: 5untag, name: TESTVVV*

  
  
vlan:777,port: 3tag, name: TESTVVV  
777 = 0x0309 (0x09 0x03)  
3 =  0x40  (0000 0100) 3 bit.  

[![](/assets/images/blog/5fe75443f5f0141d-c741097174303a94.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiiniaPdZqBQT2jwRpg6dwVhTpong6xegCVDm4imD-g6iaCNakvEZtiXUeL-z5BReQ8iYrRGH9CrD2OUYb1J3_cv-7rfBjYH1gQxqAdj7Ugq-h2pPzhK1J-dLcqR3VuTC3T1YpdaW2IRE-d/s1600/tp_1016de-v2-vlan1_vlav777%25283tag%2529.png)  
*TP\_1016DE (v2) vlan:1, name: Default. vlan:777,port: 3tag, name: TESTVVV*

За попереднім аналізом за адресою 0x15C-0x15D є два байти 0xFF, котрі ймовірно і відповідають за налаштування портів-членів VLAN1. Наступні два байти 0x00, говорять що це UNTAG порти.  
  
Але можу додати, що просто змінити значення у HEX редакторі не достатньо, так як змінений таким чином файл, комутатор не приймає. Скоріше за всього контрольну суму потрібно корегувати. Можливо це два байта з адреси 00000006,. А можливо послідовність з адреси 00000E0E.  

```
fc /b Config777-3tag-vname.cfg Config777-3untag-vname.cfg
Comparing files Config777-3tag-vname.cfg and CONFIG777-3UNTAG-VNAME.CFG
00000006: BD 94
00000007: 4B 28
00000177: 04 00
00000E0E: F3 74
00000E0F: B9 78
00000E10: 66 9F
00000E11: 85 73
00000E12: 9E 8E
00000E13: CB 8C
00000E14: 44 17
00000E15: 01 06
00000E16: 7C 29
00000E17: A1 04
00000E18: 07 1E
00000E19: 6B AB
00000E1A: 30 98
00000E1B: B5 C5
00000E1C: BA 9A
00000E1D: B8 BF
00000E1E: 7A 13
00000E3C: 03 05
00000E76: F3 74
00000E77: B9 78
00000E78: 66 9F
00000E79: 85 73
00000E89: 03 05
```
