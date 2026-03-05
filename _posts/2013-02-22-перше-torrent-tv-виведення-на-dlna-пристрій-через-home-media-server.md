---
layout: post
title: "Перше torrent-tv, виведення на DLNA пристрій через Home Media Server"
date: 2013-02-22 20:57:00 +0000
tags: ["DLNA", "Home Media Server", "multimedia", "online", "Samsung", "torrent", "transcode", "tv", "VLC", "наживо", "телебачення", "трансляція"]
blogger_orig_link: https://lexxai.blogspot.com/2013/02/torrent-tv-dlna-homemediaserver.html
---

У попередній публікації "[ПЕРШЕ ТОРРЕНТ ТЕЛЕБАЧЕННЯ](http://www.lexxai.pp.ua/2013/02/blog-post_18.html)" я розбирався з одним з методів виведення потоку з online tv сервісу [http://torrent-tv.ru](http://torrent-tv.ru/) до телевізора з підтримкою DLNA використовуючи програму роутері на основі OpenWrt - [хupnpd](http://xupnpd.org/).  
Але у приватному запиті у мене запитали як це зробити за допомогою [Home Media Server (HMS)](http://www.homemediaserver.ru/) і маючи вже подібну публікацію "[Дивимося супутникові HD канали через DLNA](http://www.lexxai.pp.ua/2012/04/hd-dnla.html)" створюю більш детальний опис.  
Перший крок це встановити програмне забезпечення online tv сервісу [http://torrent-tv.ru](http://torrent-tv.ru/), надалі треба встановити програму [Home Media Server (HMS)](http://www.homemediaserver.ru/).  
Для того щоб мережевий потік з [TS Playerа](http://torrentstream.org/ru/) потрапляв до [HMS](http://www.homemediaserver.ru/), потрібно [за цим посиланням](http://www.lexxai.pp.ua/2013/02/blog-post_18.html#tune)  налаштувати плеєр.  
Переходимо до налаштування самого [Home Media Server (HMS)](http://www.homemediaserver.ru/).  

[![](/assets/images/blog/2cd8d455730f828c-a143bd64df6ab050.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLFkDOekPSxeMEsNGzMJ0hVPg0jJ7KjBcfuV0xJxH0aJNRVfxomySOsPUXUDdW0x8k4F0gO9_NnWNH6Yav7GGpE2FIc6J36_Eqh39Qjk75BDxrnW96lgsAhNfoNspMqUlmH5ptcfQyQNmz/s1600/hms01.png)  
*Вибираємо розділ "Інтернет телебачення"*

[![](/assets/images/blog/26047229606070fa-6b4c8642f9cdf292.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0BoPnuoo3g2OnO7nU4wtUPP1o3j77lKR4lYmZhoVuBbFGsV75ZijbnzizpW1Zg_ZTHbJ6CRdACBe4w-Hw0wkIiwafh-1wv8V6ZiDwQYbBLWJN4jI2KV8vtJZbmEuQ_c5MvYYeFjZ9jY2Y/s1600/hms02.png)  
*Додаємо посилання на ресурс що транслюється з TS-player*

[![](/assets/images/blog/6fefc7745c371126-37dca16be4a63661.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxgx6CUbHRV3yn5STKMiUJffm6pwAjT4b74UPUZc7JNgKhhd3pPA6cs8XCw2OfCpRDSXtGtx8YXe5tfqGRJF6an5so7YoR5nGCvIrSFpzMsxeXEDZfhniQGK0xXh-tkdrV9mLdBBYfozIS/s1600/hms03.png)  
*Додаємо параметри  ресурсу що транслюється з TS-player*

[![](/assets/images/blog/d70582bae35566c2-1088f0eff1c4436a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCjX7OaTNq6Vd7mCjzaMvwTEJSsz33mi5OmMQnsxj6Y4sDRqSjTZFCfpNVlsHHQkp2yqPH5Oc5ah0TzdN-mtwMqUy2z1KhuC5ibCLdFfduvQ4h5HG4pqE2YuZFAa-9ReW3lUx3aSGXdy_e/s1600/hms04.png)  
*Читаємо властивості потоку що транслюється з TS-player*

[![](/assets/images/blog/51fd740428581ee5-1b28203b8cbd3561.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8AbJIIogpqvfpNGEBHlDwxH1UC0AN6V9UtAtJqDlPM1DNrW1ZH7P3uH0lpzeI7hlrXWXW6i9YTP2Tw8zNWChAbAAFuQKWToIh-QZ3yJXSG2x86k3vKbtvgI4X9tb_O-_G1d5VXgZSqByG/s1600/hms05.png)  
*Перевіряємо властивості потоку що транслюється з TS-player*

[![](/assets/images/blog/50db1fb6b404d648-838c508857367769.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJntoH4fRSuhR7lmKe_0TYorr5FMz-9136HdFr_8dcz5iS1zTZ1rGPdOs0pjU-wpc9uT4k0tHMlde75BOjLjQb_iSu32dAuhxVDQdHRyBVTIm8yuZJl7RWJaDmwUF_7GX6e-lmT1wb4yxG/s1600/hms06.png)  
*В результаті, маємо таке посилання, можемо запускати сервер HMS*

[![](/assets/images/blog/c41a4271a6209cf8-008f8bd749d7720b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdsAQ-zqsEUoOsh6hnZFVfcRfTV5XMyfbYnTRe1VzZc72EmcFp19CX-hcnC3QvmhATLBBsoEkn3bBmD3DUOj9-DKXXIRBaYLO3Xf24047Rz0q7OopNtHDsgfY8jKBs0IuilpHFSV7G9gt2/s1600/hms07.png)  
*Перевіряємо що посилання на потік працює,дійний клік, на посилання*

Після запуску сервера, ресурс може бути доступний для перегляду на DLNA пристрої.  

[![](/assets/images/blog/7263aecf641f6d64-b7c39ecb0a0a6947.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8iOdo1SwZOy3otFh5-Uztk-Hev4ACyc3A68Ka5lUWtRt8RHidv11iMpOGgvdTnzkJB6TjefOy3XU4gfPSYsGY2RarUISpQoWjwthU2z1tBqumRkwhfauBIA8BRF9_ASHKP8CHl16hHLKb/s1600/IMG_8834.JPG)  
*Вибір Torrent-TV у навігації DLNA*

Результат ви можете бачити на основі DLNA пристрою Samsung TV 40B650, і використанням профілю транс кодування "Основний".

[![](/assets/images/blog/157016caedbd2e07-9b9b5ad23906c57b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfceHS6QMTHhgmOJIQ8jjpWHkTODo-6kKVH6cPKp_Ate6s9oppXNUIFD9TSxeo-RudzDHs6F5xBM6dkPnWpMgK1nJytJa94FBLXchJh6-6GDn_cs4m3AqE94ebs-mPJiF8W2S5-_cG81UB/s1600/IMG_8836.JPG)  
*Перекодування зі збереженням пропорціївхідного потоку, за основним профілем*

  
Використання профілю транс кодування "Основний" змушує HMS адаптувати вхідний потік до параметрів кінцевого DLNA пристрою, і тому можемо бачити такі параметри транс кодування.  
З використанням потужностей процесора.  

[![](/assets/images/blog/58876eaf8ba81660-9d320c63911f22f1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBQP8yJsza1BqyZLS6OPhBaKtPV2KOHmRwWTNpQqQdRurY78HgJ9HK6w4sPiSwEs5A10_TWiDylActPwxCaS3X-ZSctqbUNqawjOt95LnxUYV9RM2qMvsl9AQhtM0Y95mzoFtxn9CKLHLt/s1600/hms08.png)  
*Процес транс кодування за основним профілем*

Спробую різні варіанти підготовлених профілів з метою зменшення перетворень вхідного потоку.  

[![](/assets/images/blog/06ec658fcdca9f7d-bee2ae1058fdb6c0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWwbqd6ajKmnXc5LDz4b2kULhyphenhyphen1kWHhnhn7_BfoQhj9NK-PoBIt1D98wu9qEJ9MyP-jsyj0vTbmjJl23Kn1rKhLIr8g17q_DhvLk7gIgmjjTHXzKbiO-CW_gsEP6gWegdx-LALtlTRDhXF/s1600/hms09.png)  
*Змінюємо профіль транс кодування*

[![](/assets/images/blog/349f5a9938ac54ef-d45caa29126b6256.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEieVtrLKCdrYI50QdFnmnNiZKcrvsofWejvnpp0WGG8rTet_RgGRWWieidq9z1NMHLnkqsRJ_hvg7H7K1FbYdkWDBHrqdDo0wh4zUa2IX78GfV5fGy0VvntdyLEmU8zF8pk2Pvchw-UyWbL/s1600/hms10.png)  
*Застосовуємо профіль транс кодування*

[![](/assets/images/blog/66b035132175e13c-9ba340ce5e7c9a8d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqVl-FGY-PV9gh8fH_zO0ScCAQQ1uIfRSSPdCjlRjJvJO5bT1sM80Ygnp1-XJl8csXFEHd5JC7AbsQBxaWczoQ6yB-xp1iN2JQ9FE3jLry0LLDhr97ZdHeoiKac9eECBNIRlA4IZxAOyl4/s1600/hms11.png)  
*Результат профілю транс кодування, відео не перекодується, аудіо так.*

[![](/assets/images/blog/7cb8b1ccfeaa93e0-7aa1de3d2cbabd83.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgT7btfz9wxJXTvcM5sGInc8Cjhwf-9rms3ywa8I7rNsnvWGDK6RfJ2qzIrtvAOachHjBk4lUJj4wJ1N4d47KMk7PyAwCop3-lZRZT5lPhFOkuSNH4XOwWmSNTgpI-tSUvUURRrYETjDlx3/s1600/IMG_8837.JPG)  
*Результат, відео іде з вхідного потоку*

[![](/assets/images/blog/55e8833f21ecd4ac-bab298d1b9fdaaca.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjOP8Xchhya0dHXkAkDUMB2SfJ8x-GG4iWGPd9Jv0loiCmWCmeYlKnOFU4qxjoKhGFJiRUB924kilry6dlT3CSDHO8Vp5YPdwN3aVDnOR9qU8G_IEC0if6pm1PSdrWvb8k-sNu75sKyogJt/s1600/hms12.png)  
*Змінюємо профіль транс кодування*

[![](/assets/images/blog/c880a5d89c492dad-085913da4e153c6c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEip-xN12J9hM8PV87if2Y2Kt-Lr9h-RvW7HbUfbn22wzd2S8lGJHF22GK3trhq3fALUOOX_KW-bWy3frh1SSidbka9ZgCqDpfoLC5_zVb9q-5E84way51slf9LXs3o8nGzzeDEq8D5o8ms3/s1600/hms13.png)  
*Результат профілю транс кодування, нічого не перекодується.*

[![](/assets/images/blog/89806ee6909413f6-17dc1af534301b99.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzv-z6DJs562BdADfl16CSl5A76g6_xc_afDDteWPwk6yldNORXvCc7OtrenzOl7Kq3eG1pe9q6jqu-Ikz6np5DY7tDvq0eHKiGSFxCmwkLjpTkAc26REOWVajZBIaksW9fkj1HWpkJe0N/s1600/IMG_8838.JPG)  
*Кінцевий результат*

Таким чином відео потік інформації що видав Torrent-TV плеєр був переадресований до телевізора з підтримкою DLNA.  
  
P.S. Із практичних впроваджень читачів додам, для телевізора Philips 4307, треба використовувати профіль транс кодування "интернет телевидение".  
  
Ось ще одне рішення для перегляду:  [Перегляд Torrent-tv за допомогою Home Media Server та підкастів](http://www.lexxai.pp.ua/2013/03/torrent-tv-home-media-server.html)
