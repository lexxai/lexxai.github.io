---
layout: post
title: "Результат встановлення модуля USB Bluetooth KN330 в музичний центр  Aiwa NSX-SZ50 та ремонту регулятору гучності"
date: 2020-05-29 18:04:00 +0000
tags: ["AIWA", "Bluetooth", "KN330", "ремонт"]
blogger_orig_link: https://lexxai.blogspot.com/2020/05/usb-bluetooth-kn330-aiwa-nsx-sz50.html
---

[![](/assets/images/blog/8f9c26ae138a1f89-7eb31da1eda45c23.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_dW6PLAVV-nk2BgQfIQ0Flxeo6aKoVwGB_8p231rEs79zOpYDhy35Tb3KUGaOoVLhxZsI2twTXERAmR-dOljsqwuV2TJXVYsqYojpI2J9u328330BM1tCpyQbt5rSoF5-frvdbvyuR3Nb/)  
*музичний центр Aiwa NSX-SZ50*

З давніх часі маю музичний центр "Aiwa NSX-SZ50", все гаразд, якісне звучання.   
Але час плине, касетна дека не працює та і слухати не має що...  CD головки не читають диски, регулятор гучності потребує ремонту...

Залишився лише підсилювач для аналогового телевізора на кухні (Line) та FM радіо.

Так як я застосував [Bluetooth модуль KN330](/2020-05-06-розбирання-bluetooth-модуля-kn330.md) у [інших аудіоколонках](/2020-05-07-ремонт-аудіоколонок-персонального-компютера-genius-та-підключення-через-bluetooth.md), тому вирішив інтегрувати модуль KN330 до музичного центру Aiwa NSX-SZ50.  

[![](/assets/images/blog/a89d0f076566cd48-211bc8f1f5dc984a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrQtzHoKFQu4fVDxrdGDTY6d-zNPrsI6g3upcSpGVH4snOKxsXrxuKl-IuAgpKl08Mq-4McIpCUowoUksDXzKRs-gyXXQUr7IKd0cfldN_S5by8p7UEHeN7ciUrTwDsw2GRU33XLxn6ivi/s1600/bt2.jpg)  
*модуль KN330*

Ідея підключити Bluetooth модуль, була давно. Спробував підключити і модуль і телевізор до одного входу "Line", але таке рішення не сподобалася, та і блок живлення треба було окремий мати постійно ввімкнутий, або вмикати окремо.  
Тому наступний крок, проаналізувати конструкцію музичного центру "Aiwa NSX-SZ50" на можливість використати блок живлення музичного центру для живлення Bluetooth модуля, і можливість підключитися до входу замість CD.  

#### Схема музичного центру "Aiwa NSX-SZ50

Схему я знайшов за допомогою сайту [www.Manualslib.com manuals search engine](http://www.manualslib.com/).  
Першим ділом знайшов модуль - мікшер або перемикач вхідних сигналів, це чіп M61503FP.  

[![](/assets/images/blog/f885b5ff00d86679-17db35271197437e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiemmdYbhf6MdIe80nWAXEkWjW6NUhUu1qSgBkmL1tCI6zcNR6hEntvncG6A7OKiy2mfRJIOpsx8ArFLycHZ5GidccKcX-pMB3-ymN8olDHXZYFEznJY6UXTUxw02ns8MlQhvPkHhl93qYT/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-05-29+19-15-19.png)  
*M61503FP*

За його входами видно, що все керується в M61503FP через цифрові команди, що йдуть від головного процесора.  
Надалі на схемі знайшов роз'єм де під'єднується до плати модуль CD програвача - CN602.   

[![](/assets/images/blog/930868a155e9b759-b63f7d2fc91e0b37.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEglhEIOSXyw2uihO_SVHWwfUukK7cESWJL6es0QJrFY5bhtD-JVeG94OJThz4iI9VbK088DXI9XT8hIlzZpYcK740Uf4kt_Cv08hU-lBDlnrqJIIN7GGA3K-fNYYNudegXg1h3GgyiswZWZ/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-05-29+19-20-08.png)  
*Роз'єм підключення CD програвача CN602.*

На цьому роз'ємі є 11.2В живлення (1-2), сигнал готовності CD програвача - CD-ON (3), стерео аудіо входи від CD програвача(4,5,6).  

#### Етап реалізації підключення

Так як Bluetooth модуль живиться напругою 5В (через USB роз'єм) потрібен модуль пониження напруги з 11.2В до 5В.   
  

[![](/assets/images/blog/41fa9c1618b06cc0-226b33f39e855a9d.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRMhOIRwZL9pB6BEdBMDDtyIROECoph91pQEo-ezzuhXOt9BeGnLiS2M3yIwKxIswdQ6DM1INGwHPbaAVr3Qnr-lJKv5O3WwAUGgg34hX2Jdt835NNP_FPbQF9-WaoUcHvG8Ld-YTkCa8k/s1600/bt5.jpg)  
*сила струму споживання модуля Bluetooth, до 40мА.*

Тому вирішено застосувати 78L05, за типовою схемою, з С1=1000мкф, С2=0.33 мкф, С3=0.1 мкф, і паралельно С3 додатково 100мкф,   

[![](/assets/images/blog/89701ade397c1109-bbb1d2ca20096eb0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1TrgcDcpjQ8VovGj3a-MrhYio9IfE8MVr4AbocoTL4MFyKn5Vl1xiHH9QA3Y8enNmtMY60c73SEYONx7hzVnMJT993SutlbviQrOVXzX2Di9OBPTyzG-YSIZsFBU0DPZLu15qYpszb9K1/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-05-29+19-50-05.png)  
*схема підключення 7805*

[![](/assets/images/blog/dbd0c5a02b797cb5-048d5d8b70b329bb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQKPI8rJgFfGzNxEWv8SRkBFCiEtFjDyVWZG_dhG2SZ9bm9HbMNmS_y3cZQqrDyqBREa_eP1yOZGAk2bvBz1Mh65BzB5OpqOEu8WONfoKKZC8Z6zxqT-h569BqB55aMdsJDPSZiJQ_jbSn/s1600/P00508-185652%25281%2529.jpg)  
*плата з 78L05*

  
Надалі було зібрано плату для підключення аудіо сигналу з Bluetooth модуля до схеми музичного центру за аналогом підключення Line/AUX.  
  

[![](/assets/images/blog/4aca8cba26f675c9-0a43e6fd5a5b1b46.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3PwmNMNLoSO4j0953C3BgzP3saOj5AWhV7FetqQQ7-2_1V2M_ZbX74x9aRTCzUsuG1Xu4lg0LMQK1OGmYng0srEqyiN8cNVDtoR6daD6Hxm2wxbFpFYpHYkyna5YUFv5r79vNZejg-xBd/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-05-29+20-13-23.png)  
*підключення аудіо сигналу*

[![](/assets/images/blog/6f13055fb73552cf-04eadcd142849d42.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhnwPsZdch7SSaEdMlpU2BrPfNJDTC7SySBFvdgKVWRLD5RDhfA-qnJFwfzSET1LUfnwzislXeNrOPwhlADoqHDgXC0nD3Q7b5AyrYTtbG8T-mpSfxeSRM3m5Dv-7YlwWM1aY9KXzdIoXTx/s1600/P00509-210907%25281%2529.jpg)  
*плата підключення аудіо сигналу*

Для підключення до музичного центру використовується роз'єм CN602, але попередньо треба від'єднати від плати шлейф який з'єднує з плату і CD модуль.  
  

[![](/assets/images/blog/f3099acc0f42f8be-e81f80e399968c88.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjY4Z7inFakPYG0ExE5Wx1Pdu9NCtKmgIrE0ftNm_imiqScaIGXgelZ1PX_1ydo1L-pqINvkLBpnDEhxayNqZpVg5zvv2Q9NvtTJFcdSjoPweGm7DAmEmhtv-7ULohdVNlfL7OzSzt99tPX/s1600/P00508-195256.jpg)  
*шлейф який з'єднує з плату і CD модуль*

Надалі пайкою під'єднуємо екрановані кабелі що з'єднують плати.  

[![](/assets/images/blog/52c967398fe72cda-0defd8c48445dc14.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiK3rm_If0C4pqgn4V0H73gCdhdxaal1XiLxK2gGUuK2gIRblVPpNG4lbuSftVv8LpiADhrjBaGbLkttq2eltUOMFHwN0zhInbgFg_DjL0hUJDuVu9EpiCTD45fhYw-IsYr6hLlyQdgVAFj/s1600/P00511-174004.jpg)  
*підключення до музичного центру роз'єм CN602.*

#### Ремонт гучності

Надалі за відео матеріалами інших користувачів я розібрав повністю плати щоб добратися до регулятора гучності і почистити енкодер.  
  
  
  

[![](/assets/images/blog/269364d2e4e3c616-998c387126ddcc78.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKeWFD61ZnTU0wfuN0PszIWZeXww6gaQ3-xhBqMUFWzUz96GKN7U3Gi6uPgEQ1DQEQyjDPJXR6v8_aXsoSkHpTEVqEuIj-jmOzzDC2a_AXzDR_0tZZZtU5X4i2QMaZgdd0WYQkSgMfutkv/s1600/P00509-214712.jpg)

  

[![](/assets/images/blog/2e42b86d4c21eb28-442eec343785fc44.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhL6nsFvxG8WprFS0Yjcnk7ilfKi9O2TzRTH2il5YROyQgcHJDxPJhjXjrFNtXdD2vo6kyLXcCiH8QBZHXsBa-PRqK3fx58tThMjxmUzuIeWf0DYxbQLQX_zv_yvhzElGplIICU3c7LX-BB/s1600/P00509-215051.jpg)

  

[![](/assets/images/blog/83b6c3dc428466e8-f9b3d3475f87e938.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOlSW695m2d2OYaglLJJZO646NOZ_Rz4TZ8fAjm3cpjBqq_cw-cPU6ePIA2fMW4iu6JGxsISU22v3QZjU4IASkvkAuiP2llRE5g-ns4R9-mqh9ILBpdTVLZwz3i44V-qkt8eyLQfop9EF2/s1600/P00509-215428.jpg)

  

[![](/assets/images/blog/36b1d40d60fb3c69-165a13e09fd3aaa7.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6Myju_g-9prlMfHYUe154lDQYv270jYSdo65xkKJfRG_QEHYvyAm6pkCWk6a-5tCD3qAw64U97Aqz0YvxpWYPA0Zwa6ksdMjaYrVGcR47VnHBuBD9u-aOq4v9dqRo-YEC_bAs7ueJ7LOk/s1600/P00509-221149.jpg)

  

[![](/assets/images/blog/e6941e1ddfe44122-3ac0580947bd60d7.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRGziro-8HzVnSxxnkCPX-uCYLSZ1wceNqJSAOpR3O_JELsF0DwUP-z4z5hV6Lg645dmTaGIVKanuV3jpS9lPCbhy4dYWltRMfcB8ciztZzmJl44ivccQF0K8KPwkjYbflhtagt2VBp4X1/s1600/P00509-221654%25281%2529.jpg)  
*Енкодер керування гучності*

#### Збирання конструкції

Надалі у зворотньому порядку все було зібрано і змонтовано.   

[![](/assets/images/blog/33be33b4362a7ff0-7c51ebac443b86ae.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-BYUERLzprvzIJ83E88mRXPXZAx51BknyIbuKyG1IH_OwoTd7uB5tklgjLLPo7UX4Ujt0AuDgXiaBXxuvraw-WjAbu4OU-gMbewvfqrg5-3G44O93HrToqTr3Fck7PZd_WkM7WJslg7YZ/s1600/P00511-183039.jpg)  
*зібрана плата музичного центру*

Bluetooth модуль був закріплений за конструкцією касетної деки.  

[![](/assets/images/blog/87427ad072e538b9-33dde389258e86a1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5oSJhkJtJ-yDx5bI-iv6qNFK9-TLyLltvqu72bJdFZyP4c8DFGYkn_2GPy4UAbWM_LjdAWQUK4W17yo5KO55OxRysjxBfh7Bm6iDeVFH8XkjmqwET22w7IJ3Vu-zbhIEFjf9czJol-QR_/s1600/P00511-182937.jpg)  
*Встановлений Bluetooth модуль*

#### Надалі проведено тестування.

Модуль Bluetooth вмикається коли вмикається дисплей музичного центру.  
  
  
Хочу зауважити для того щоб не вимикався музичний центр через неактивність CD приводу, потрібно натиснути клавіші зміни треку, щоб завжди блимала кнопка з зеленим трикутником.
