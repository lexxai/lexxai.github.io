---
layout: post
title: "Ремонт аудіоколонок персонального комп'ютера Genius та підключення через Bluetooth"
date: 2020-05-07 18:46:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2020/05/genius-bluetooth.html
---

З появою перших комп'ютерів у нашій сім'ї з'явилися і аудіоколонки, це були досить симпатичні та надійні - Genius (Key Syetems Corp.) SP-G16.  
Так вони пропрацювали десь років з 17.  
  

[![](/assets/images/blog/6b3517b3b20d8132-cdd610d8f91a413a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmGZy_IrkCRipdwmgfYPYhkDyy_VryH9eLSOBZHm-H3sB0KYryUYkw7JFA-YQ73qrhyphenhypheneVdAa9-631WjviU9U2DMy98IMTRk_unirNpzKSbBOcEkhlSwwMuN2mfOLtB84u-gSrfXyM1BNQU/s1600/a8.jpg)  
*Аудіоколонки передня панель SP-G16*

[![](/assets/images/blog/6f1e61d1cc47c531-5a16b3ec0d317607.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQU11y__KpuTVhe5XENWSqC1lWsR5NYiM8A0u3HTkGWTJTXwGYfFcYOSF8wccUzbAlEX5OOEHZLpQNlHwV3cT1qOOB5Ld6MQPkxzNLxX5p2p1xVjy_2P2wVY62UC2Jtw9KDNapLLAmxvZs/s1600/a3.jpg)  
*Аудіоколонки задня панель SP-G16*

Але, якось мене повідомили що вони перестали працювати, і видали на ремонт. Розібравши я був дуже збентежений механічним вигоранням  частини плати де розміщувалися діоди що організовували діодній міст для випрямлення змінного струму у постійний.   
  

[![](/assets/images/blog/762af597ca165023-f32751cc733a81c6.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTEfpkJ2q6DQxQJS6R4NgeXiQhH-qW2R9RDMN9BvkJC35uamYQAZCAclzT_YyTQRcxsrkp6rY-CXXxdODtqOnvQfU6-YNHEUBZcQ38QzMHEfmrkI4vu8tnLB8qCrkzgvN9VsVrBTD3Hjz3/s1600/a6.jpg)  
*Вигорання діодів на платі SP-G16*

Замінивши чотири діоди, навісним монтажем, вирішив перерити плату на замикання і був здивований що замикання все є.   
Вже грішив на мікросхему підсилювання BA5417, але вона була тут не причому. Причиною став КЕРАМІЧНИЙ конденсатор на 0.1 мкф який  вигорів і замикав всю схему.  
Ось тут цікаве і небезпечне, схема постійно ввімкнена у мережу 230В, а вимикач на передній панелі просто вимикає звук, і живлення подається весь час. І це є небезпечно, так як може бути пожежа якщо схема погана.  
У моєму випадку від пожежі зберіг трансформатор в який вбудовано запобіжний засіб термічний запобіжник, що спрацьовує одноразово за перевищенням сили струму або певної температури навколишнього середовища, у випадку трансформатора це обмотки.  
  

[![](/assets/images/blog/45d236a279c5d2b2-a8cbf3166ca8eaf4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigg2SdFMzYmgDszAmPO8or0aVqoAlPvUzIq5gbKk6fvBqnhJlaROnG8z7xcKrLZDw25T4Om7PyvtZ9ZinWQJixTyzV9Kzps8Sfqmbtl_j_-UoZWCY7AFZ8SqG_if1p-Lwby_BcwM8APR9w/s1600/a7.jpg)  
*Трансформатор з запобіжником*

Розібрав трансформатор (за допомогою "кутової шліфувальної машинки" зняв металевий кожух) і  так я дістав запобіжник.  
  

[![](/assets/images/blog/dfca3e0027b43fa1-463eaf38921512c4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXw-i7tuLT8liuMPPdz31fPjFS0scgDRB-aIwEisc1N3kymkiUXm8k9znaG8jDp_fzBXcZMR9N4vdiRCJT7ufxTVqNmI28op13R4n9EZ2VEVvHN1tw9nH3aE00B-YsH02p6EcbmPBKELSF/s1600/a4.jpg)  
*трансформатор розібраний*

Видалив термічний запобіжник M33. Але за умов карантину не маю чим замінити, хоч і коштує він недорого. Тому зібрав трансформатор без нього. Але додав до схеми звичайний запобіжник 0.5А у мережі 230В, та запобіжник 2А у мережі 12В.  

[![](/assets/images/blog/e43b41b62130b274-0a0cebea001ba39e.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4MRG7GWptvntzfevhhvmFoU2pBs9jskNZuK19doMtZ_O-81A0PKZraW7SBiK42tiazfO5rUju9Xd3cHSM5tE8RajQW44w8gFERk9Q_d6u2tM4x8w64j2io6vyJb2g_-y0Uk7yxza2s_c-/s1600/a5.jpg)  
*термічний запобіжник M33*

Для зручності також було додано, кнопку вимикач по лінії 230В.  

[![](/assets/images/blog/8deb300ed7b620d8-beaef854284b75a2.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN70-hLPT98J0zEb2nvYlklmWIvsJrlswDHyOfjWWW8RrN1osc22VNang_Y6hLY1xQxQP00FG0Hu5TXjp9i55wYBXjKq5q24pw-We7PkwiTfRCiuI7WmnSoeR2YnlGjaJKIWbuFmVsai0W/s1600/a2.jpg)  
*Аудіоколонки  SP-G16 з зовнішнім запобіжником та вимикачем 230В.*

  

[![](/assets/images/blog/563d213f3e89d621-f71b89672b539cbb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihyphenhyphenrE6Q1n5Q7Q7DUbLI3UmfS7NMu8ETzXHC_ylh3tinA8U-JdDPTL7uCJ-0zBtC0SeoTuwEC3-74BaambCXDfvyl5TtVFf11Fm5aoPDqkc-XFj8qyS1wHHcM34iDH-Un6Fjb6MKT7gUbN6/s1600/a1.jpg)  
*Відновленні Аудіоколонки  SP-G16, з зовнішнім модулем Bluetooth K330.*

Як подальший етап відновлення був етап додавання можливості програвання через Bluetooth. Так я додав зовнішній модуль [USB Bluetooth K330](https://lexxai.github.io/2020/05/bluetooth-kn330.html). Цей модуль може працювати у трьох режимах. Перемикач RX-TX перемикає режим прийом-передача. Два модулі можуть організувати простий аудіо подовжувач.
