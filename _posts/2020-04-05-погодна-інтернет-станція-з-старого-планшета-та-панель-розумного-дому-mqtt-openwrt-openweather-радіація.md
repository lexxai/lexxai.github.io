---
layout: post
title: "Погодна інтернет станція з старого планшета та панель розумного дому MQTT + OpenWRT + OpenWeather + Радіація"
date: 2020-04-05 19:38:00 +0000
tags: ["Android", "Mosquitto", "MQTT", "MQTT Dash", "OpenWeather", "openwrt"]
blogger_orig_link: https://lexxai.blogspot.com/2020/04/mqtt-openwrt-openweather.html
---

|  |
| --- |
|  |

  
Ось є час під час домашнього карантину.  
Відновив старий 7" планшет Impression ImPAD 1213 на основі процесора [Allwinner A13](https://en.wikipedia.org/wiki/Allwinner_A1X) з Android 4.0.4, перепрошивши [прошивку від виробника](http://downloads.impression.ua/index.php?route=information/download).  
Але що з ним можна зробити ? Він дуже "тугий", але Google Play Market працює.  
Тому встановив програму [MQTT Dash (IoT, Smart Home)](https://play.google.com/store/apps/details?id=net.routix.mqttdash&hl=uk), і вирішив реалізувати простішу панель для показу поточних даних погоди.  
Хоча простіше можна було б показати готовий віджет від відомих погодних сайтів на головному екрані планшета.  
  

[![](/assets/images/blog/d9c59ebf652783c5-f6012e9208b3f60b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXz39rO-ZgLpQztGocAwiCYI6pWHET2MTMYYMoD5NNu7xi030od2CTxeE82cVxe40kEF6zx9uzGciAZVHSfDBqEdsU_iH1vrj_Xj70rGflqbiMpkjgUn0drrolQgobFCHJBBs94VtfaTnQ/s1600/photo_2020-04-05_20-43-34.jpg)  
*Віджет від відомих погодних сайтів*

Але головна мета розібратися з керування через [MQTT](https://uk.wikipedia.org/wiki/MQTT) пристроями в розумному домі. Тому іду іншим шляхом.

#### OpenWeather

Для отримання даних вирішив використати ресурс [OpenWeather](https://openweathermap.org/). Для використання [API](https://openweathermap.org/api) функціоналу потрібно мати персональний appid, котрий можна отримати після реєстрації користувача. Користувач може вибрати різні [тарифні плани](https://openweathermap.org/price) у тому числі і "Free".  
За своїм тарифним планом "Free", я вибрав данні: [Current weather data](https://openweathermap.org/current) ,   
Приклад запиту за ID мого міста, можна завантажити і знайти свій ID: [city ID](http://bulk.openweathermap.org/sample/city.list.json.gz).  

```
By city ID:
api.openweathermap.org/data/2.5/weather?id={city id}&appid={your api key}
Parameters:
id City ID
Examples of API calls:
api.openweathermap.org/data/2.5/weather?id=706483&appid={your api key}&units=metric&lang=ua
```

Результат є у форматі [JSON](https://uk.wikipedia.org/wiki/JSON):  

```
{
  "coord": {
    "lon": 36.25,
    "lat": 50
  },
  "weather": [
    {
      "id": 800,
      "main": "Clear",
      "description": "чисте небо",
      "icon": "01n"
    }
  ],
  "base": "stations",
  "main": {
    "temp": 5.22,
    "feels_like": -1.2,
    "temp_min": 5.22,
    "temp_max": 5.22,
    "pressure": 1030,
    "humidity": 58,
    "sea_level": 1030,
    "grnd_level": 1011
  },
  "wind": {
    "speed": 5.88,
    "deg": 339
  },
  "clouds": {
    "all": 0
  },
  "dt": 1586109523,
  "sys": {
    "country": "UA",
    "sunrise": 1586055742,
    "sunset": 1586103149
  },
  "timezone": 10800,
  "id": 706483,
  "name": "Kharkiv",
  "cod": 200
}
```

  

[![](/assets/images/blog/c2b75495f24fb1fd-dec42246d62eedbf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjlXB765FdYfDK4w0_EN8lGPvb750RYxmlUKhExuydI8e0lvX1exyupWwYVohi1KJYtoYqCGlQhGr6ENIKCG4_jFOO88DMDd9B-VIkSI0RgJtvhBQ7quzRcKDTx4VoRfc3K3u0mt_NJvIJ/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-04-05+20-59-09.png)  
*Візуальний результат у форматі JSON з OpenWeather*

#### Брокер MQTT - mosquitto.

Пристрої розумного дому обмінються за протоколом [MQTT:](https://uk.wikipedia.org/wiki/MQTT)   
MQTT (англ. Message Queue Telemetry Transport) — спрощений мережевий протокол, що працює на TCP/IP. Використовується для обміну повідомленнями між пристроями за принципом видавець-підписник.  
  
Для використання [MQTT](https://uk.wikipedia.org/wiki/MQTT) необхідно мати прграмму брокер, вона приймає повідомлення від одного пристрою і розсилає це повідомлення всім іншим підписникам брокеру.  
  
Для розміщення програми я вибираю свій домашній роутер, що працює на основі [OpenWrt](https://uk.wikipedia.org/wiki/OpenWrt). Серед готових пакунків для роутера вибираю відому програму [mosquitto](https://mosquitto.org/).  

```
opkg update
opkg list | egrep ^mosquitto-
opkg install mosquitto-nossl
opkg install mosquitto-client-nossl
```

[![](/assets/images/blog/b843e45be79b647e-7133f9a16bf0abf0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiw21Ak1l3AfJ8lnRm20bFN1QR2Xfa7SS51fhl1dGCcpgC2lIPZtjHop0c867tXNBT4EFlHZycpvg6A3YIYO6GmX5SVn2XfnRjXbPetnjvh8A5SbO0EjmQKfkNcJRHfF3T19iONZXjpyTb7/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-04-05+21-14-19.png)  
*OpenWrt - mosquitto*

Налаштування [mosquitto](https://mosquitto.org/) можна знайти в [мережі](https://www.google.com/search?q=mosquitto+openwrt&ie=utf-8&oe=utf-8). У мене зараз мінімальні налаштування сервера без ssl, з використанням авторизаціїї за користувачами.  
Для передачі данних на [mosquitto](https://mosquitto.org/) сервер, використовується [mosquitto\_pub](https://mosquitto.org/man/mosquitto_pub-1.html).  
Для розподілення різних данних один від одного на сервері використовуть [message-topic](http://www.steves-internet-guide.com/understanding-mqtt-topics/).  
Для пердачі даних з погодного серверу OpenWeather я використовую  [OpenWrt](https://uk.wikipedia.org/wiki/OpenWrt), на якому встанвленно і сервер [mosquitto](https://mosquitto.org/) з топіком : wrt/temp-inet.  
Приклад надсилання простого повідомлення з даними вологості:  

```
mosquitto_pub -h 127.0.0.1 -u userwrt -P password81 -t wrt/temp-inet -m '58%'
```

Приклад надсилання повідомлення з зображенням використовуючи файл:
  

```
mosquitto_pub -h 127.0.0.1 -u userwrt -P password81 -t wrt/temp-inet -f 'image.png'
```

Приклад надсилання повідомлення з потоку іншої програми:
  

```
echo test123 | mosquitto_pub -h 127.0.0.1 -u userwrt -P password81 -t wrt/temp-inet -s
```

  
Для автоматизації процесу, результат з OpenWeather отримую програмою [wget](https://uk.wikipedia.org/wiki/Wget) , параметри q -O - дозволять видавати результат до потоку іншої програми.  
Ось скрипт для автоматизації процесу для надсилання кожні 10 хв.  

```
#!/bin/sh
appid=xxxxxxxxxxxxxxxxxxxxxxxxxxxx
cityid=706483
topic=wrt/temp-inet
user=user1
passw=changeme1
wget -q -O - "https://api.openweathermap.org/data/2.5/weather?id=${cityid}&appid=${appid}&units=metric&lang=ua"| \
 mosquitto_pub -h 127.0.0.1 -u ${user} -P ${passw} -t ${topic} -s -q 1 -r
```

  
Таким чином кожні 10 хв, данні передаються до  MQTT брокера в топік wrt/temp-inet.  
Якщо в сервері mosquitto вимкнено опіцію allow\_duplicate\_messages false, то до інших клієнтів котрі підписанні до сервера будуть надсилатися тільки данні у випадку коли вони змінилися.  
  

#### MQTT Dash

Для візуалізації на планшеті встановлено програму кліент : [MQTT Dash](https://play.google.com/store/apps/details?id=net.routix.mqttdash&hl=uk). котра підписується на топік брокера для отримання повідомлень.  
  
В програмі MQTT Dash додаються об'єкти різних типів:  

[![](/assets/images/blog/3eb500ca2541fc19-c99146e6041dc33a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVudw2AViy-knvu1ArDqG7mVGqg3wPyxYHGK_GlZYGNco1tvC3aRK5ON6MP8zVbC8ObWCvlf61Bqjo35D63cor-mHPXNdxfXxc7uSW9U50xFaHnXkUmvZ2tO3jSb2c0Cg0CleBK04ojIyf/s1600/photo_2020-04-05_22-12-10.jpg)  
*Об'єкти MQTT Dash*

Для виділення об'єктів з JSON відповіді, використовується  [JSON Path expressions](http://goessner.net/articles/JsonPath/index.html#e2). Тестувати стовренні запити можна тут: [https://jsonpath.com](https://jsonpath.com/).  

[![](/assets/images/blog/fe15c22067c21d61-b4d80e39605075d6.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9QXKUt7aB-H2W7Lrhd4DKAEIZe_MQ8bBR1y0eneyNl1y-tpC5MDPhn4otlkf3MeZ6KvBK80GlKCEhg-w3PD1PApFGrNLkc0s4ewMKHfaxNdSf3K-8sl3vY3mlBk1NaTuFTeBn_O7l6NMy/s1600/photo_2020-04-05_22-16-56.jpg)  
*Вологість (main.humidity)*

[![](/assets/images/blog/705aad6f9067da84-722ce059928a0ca4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBgu7ZBUr7o02kCalvMfcnHym8tjXV3pMmKWQZprH46oYeodjsPIHvL2Cs5HsZsH1CeXF2IipRB4T5W-u4MdXytc81QQhy_epDz3nAzmB8UvYRvNIHaPezk6I4jErk027xg9OPWyv6D2MH/s1600/photo_2020-04-05_22-16-57.jpg)  
*Температура (main.temp)*

[![](/assets/images/blog/0eeaa617e4220867-e1c9ac4411753dc5.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4W3RK8S7nFrrYGqharg_BYzBceO7PjF39NF5vc4hlhSNFHWjbRoyfuKCBx-G7jmtZAvKzWTlGWWFMl7ivcsQIFqmp7CRZz6DITGtPYm_2GKgw80OUB6rQHwDyjrTKJ1N1FyX07JHsPHP6/s1600/photo_2020-04-05_22-16-57+%25282%2529.jpg)  
*Атмосферний тиск (main.pressure)*

[![](/assets/images/blog/ae7b0a778deae9c5-8a2d2e12731d8f25.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikqXZLVRVs-r_vx3n5ifGr1Y1EPEkf1dn9Ch0-ozuVac0-lFZOy_-eXCoUVrSY0huayDAaONQT0nihLAGttmnR-VqJciO2_-wBp0FbcF5QLmFEX2Kc95pEnrqERCAaH12XS2sdqroRoAK-/s1600/photo_2020-04-05_22-16-57+%25283%2529.jpg)  
*Опис (weather[0].description)*

[![](/assets/images/blog/c471faa5cac5aa8b-8f80ae3456ab8cb6.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjREDb_7yEigseuBSeeRS86fk-GwHYtJh-NH_H6Uv6n1iCKfNtjWNPnsSiWXAItPYa46_rn3KXXvdoiWjwt9gRc5abO9VXiUiidK7vA86WkNpgq-j-hkdbA9vBfBEe_sadYK-ZLdj8TyNnc/s1600/photo_2020-04-05_22-16-57+%25284%2529.jpg)  
*Вітер (wind.speed)*

Попередження про великий вітер у вигляді іконки котра блима можна задати так:  

[![](/assets/images/blog/e23056ff624d7750-387d8bc566d774b0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8pF-eC25a9hiIi0HkFcF4nwCOZMFoHWMcuQnx5-KleqHMq_KeIQ2iW0_TL5DUSKGAv26wuKUfrMDamT3xHtjzm2YrnPxpB2mjkkAcVZ-7tq_Gfj60rpYbB2H6fo2jW0iEErPFn4sti3rO/s1600/photo_2020-04-05_22-16-57+%25285%2529.jpg)  
*Блимання при значенні вітру більше 10 м/с*

[![](/assets/images/blog/6805c4146fbe70ec-67d4a1383b016c64.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh59ipeT4Ywo3vqR6g1Yg4tp26zXS3Zd7Px-IGqM6bR4quidg0zMUT5Jbk25WmbyNUFmqFitwixIB_K70yZ5094UJnP3oVeRhljotX8ZlmR5OkNbA_L7a99rCBehhkXZYc7ROmmfv685WzJ/s1600/photo_2020-04-05_22-16-58.jpg)  
*Зображення іконки з погодою (weather[0].icon)*

Але в даних не передається посилання на саму іконку, а тільки її код, тому потрібно сформувати код завантаження іконки з сайту в форматі: http://openweathermap.org/img/wn/{icon}@2x.png  
  

[![](/assets/images/blog/f3e8556dcadb5675-b5cee2ad18df7775.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjL6GrHKG-M44g3_cM9aAqi6jwy-7yMN6J1fQT3xVjhEBcN3lH2QmUWHKjJzlxvt4hQ9vG8l1fP8ZVSGdpOIjIuVN52CCi2tjvfPJ3gVmjdFlfsL2_yEyf2uy8Hv0B6OLVDnfpYI_t5EvOr/s1600/photo_2020-04-05_22-16-58+%25282%2529.jpg)  
*Обробка даних при показі даних за допомогою JavaSctipt*

[![](/assets/images/blog/40b3f7d7a57a31ec-1156cb673b3c80c4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhd5SNnYrH3uKlJfIOTL2wZBMgkvL4si9Fd8m77UUzqJM6LrPXt1b9owe9cd96zi4TCo_PUNuODm3FCpOqnhLxb4vN4H54DcIiOyNUrzKaBvBZkVr1ujC_7HEVi2VuCL8Qpb9w-vkysU-4q/s1600/photo_2020-04-05_22-16-58+%25283%2529.jpg)  
*Обробка коду у JavaSctipt onDisplay event*

  
Для простоти налаштування я робив налаштування в своєму мобільному телефоні, а потім налаштування публікував у топік metrics/exchange засобами програми.  
  

[![](/assets/images/blog/30142a1ea2074dfa-42a4f73f6383063e.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhlcPdADzgOHDLlqQGYUnLtmt9eS1J9bCK9_PVILRTqCWUWVZK3V34MtyMHpkouxo2Ta0Z3LO5GaVbAPFX9rTxfIk8rQXllVzmtofP2PxWJ4pG7yDE4luPum1JJtdFC7f4qa3Er6hSGtTD7/s1600/photo_2020-04-05_22-12-05.jpg)  
*Export / Import налаштувань через metrics/exchange*

А на планшеті прийняв налаштування.  
  
В результаті отримав налаштований планшет.   

[![](/assets/images/blog/596822626c9cc01d-115c5a490679a0c0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7QQrw4fYATkEjo3boTJmsNvUwevajiV0MZvgY3EZd8aGidsiom_2Z_ax8BGOceMPSxccVowqN79Y98tedDm2TG_FBvXg4gBH7eXptbCmwBSyRTh7jPgwk_ie1m0NBE-p7DT1pT-trVQmj/s1600/photo_2020-04-05_19-44-24.jpg)  
*Планшет з погодними даними*

#### Споживання енергії

[![](/assets/images/blog/fd689fe6abcb55a3-a7a84494daa12bf6.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhq2OUCSeruccSAErDxaj2tzSSQ_VLjoYyxulJQXgmizaXelRKHRfwGoBeQE-nNKowuGYAuSbd6_U-JVohi65uHpRanubJUqOvBFigmUTfJ5uwkn2zhBa_i2CgCz6O5me5ykI1mYjftSuBv/s1600/photo_2020-04-06_17-13-16.jpg)  
*Вимкнений екран, 1W*

[![](/assets/images/blog/13f56d9a768ef79b-04715460fb49bf0a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9B7InPvZx16lHn3NlzeZfWak9F-njlhKDMOk9CeO9V2LTe0l0Dt0OTBxNOc9bzmh6ek1bpHEO5gYKTaLnlRkFoqRZWryUxMe3nOroDhF5mBuGWt8IM_wrmAnab3zlChU9q79NyVuwTsLw/s1600/photo_2020-04-06_17-13-30.jpg)  
*Ввімкнений екран, 2W*

#### Данні радіоактивного забруднення

Якщо проаналізувати сайт "[Український гідрометеорологічний центр](https://meteo.gov.ua/)" то можна знайти інформацію у [JSON форматі](https://meteo.gov.ua/radiations/dataSourceFiles/googleMapDataSource.json) котра передається до карт Google у масиві.  

[![](/assets/images/blog/c8daad23d62462f4-215e3cb6d4e0f9dc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0T3MPz9qC5GJ4NrPYbG3dJ1dEmZeccMgjaZ9gFcR7VqjyZH6gg7b2fjyVFaTftNuO-LsQz_NSC2_oiJ3D7N0WyvBAf48g8Ds_ixmQsyZmvbo7fQVQSr5UkLAVqew8dOxZlanc8Nx-eMSK/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-04-10+01-56-20.png)  
*Український гідрометеорологічний центр. Рівень радіоактивного забруднення*

І якщо зробити вибірку з масиву за номером станції stationCode = 34300 різними засобами, то можна побачити поточні данні радіоактивного забруднення в місті Харків:

```
{
  "id": "89",
  "stationCode": "34300",
  "stationName": "Харків",
  "latitude": "49.927978",
  "longitude": "36.282428",
  "height": "156",
  "zievert": "61.11",
  "roentgen": "7",
  "measurementDate": "09.4.18",
  "measurementTime": "9",
  "iconURL": "/radiations/edose_pic/50_100.png"
}
```

А зображення іконки загального стану за адресою "iconURL" з префіксом: https://meteo.gov.ua,  тобто: https://meteo.gov.ua/radiations/edose\_pic/50\_100.png  
Для пошуку даних з масиву в OpenWRT можна використати пакунок jq.  

```
opkg update
opkg install jq
```

  

```
jq  ".[] | select(.stationCode == \"${cityid}\") "
```

Скрипт для передачі вибраних даних до брокера:
  

```
#!/bin/sh
url=https://meteo.gov.ua/radiations/dataSourceFiles/googleMapDataSource.json
cityid=34300
topic=wrt/rad-inet
user=userwrt
passw=password81
wget -q --no-check-certificate -O - "${url}" | \
 jq  ".[] | select(.stationCode == \"${cityid}\") " | \
 mosquitto_pub -h 127.0.0.1 -u ${user} -P ${passw} -t ${topic} -s -q 1 -r
```

В результаті тестування іконка загального стану не звантажувалася до програми, тому я замінив її різним кольором тексту в залежності від рівня зараження.

[![](/assets/images/blog/d4f2845c420df80c-d8b2ed6fa34f8230.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-73DleDuM33IE52YYYMk96YvVeALPP5dgks2TaKsFSti36lUbq3B4pRmCr3RF01FOK7khxWPFOdPqzr9qLSWjxVLoGazrii6UKArBEd0N0WJeFQ7moWJB8fxLjwHXb3H8OTOYQ5YUTsjt/s1600/photo_2020-04-11_15-04-40+%25283%2529.jpg)  
*on Display для виділення кольором рівня радіації*

#### Деякі візуальні доопрацювання

Використовуючи можливості обробки у JavaScript, використовуючи JSON parser цієї мови, я об'єднав параметри для більш ефективного відображення.   
Так опис погоди об'єднав з іконкою погоди замінивши заголовок при обробці скриптами on Receive та on Display:

[![](/assets/images/blog/8839ce76300fc027-98594875b8c902f2.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2ovl4og2bdr_yzRJXtxJmA7rKKRBt9-Ftn1LoHtBg0U1O5aPOr5X2P5pokbSgvd5iYmHX-1sCnwPyMFHvsoJQ2OIFWYJGe7al5r2LUl5LcO3ynyzFSNnM19b453NdzxOzErSBtv0l2eJi/s1600/photo_2020-04-11_15-04-40+%25286%2529.jpg)  
*Обробка даних про іконку погоди та опису*

Для температури об'єднав декілька параметрів:

[![](/assets/images/blog/73b782f649935ec5-4a7c2482ec026c32.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoThG30EUW2pYPJtMQlEFTo9B5bY32Hd_Ns356fHVHCBsHYLj5_hVXQNzmhVeFMWGLylPimozXH7kUxecY4DsSGkU5PjDEYT1bqmRcs7HaHshGmwz2aO3dq2KIPUMu32w_kY0fyqZVmgyN/s1600/photo_2020-04-11_15-04-40+%25282%2529.jpg)  
*Отримання та обробка даних температури*

[![](/assets/images/blog/ca0be3ec0c581433-0d3146b86c0cb5d7.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFqdmgiM3uIHdiJdws8x-_rWi0pO8gOudzLqK-5YY1orNU_KZI0q_x1mOSgDC31tW3XVnItX-_eBE8aKzeEfxnw2T1fyTPsTCcUaiFbEHkL6ENuHmH_GgAZGvVVPmMfqcV4l6e8ZoT7T8X/s1600/photo_2020-04-11_15-04-40.jpg)  
*Показ даних температури*

Додав статичне зображення мапи з урахування даних хмарності, завантажуючи її за адресою: https://meteo.ua/var/zip/Sputn-24.jpg, оновлюючи її кожні 3600 секунд.

[![](/assets/images/blog/1adb68058ebfb8d8-c71e81dcf6785203.jpg)](https://meteo.ua/var/zip/Sputn-24.jpg)  
*Інфрачервоний канал - Хмарність*

В результаті маю такий вигляд додатку:

[![](/assets/images/blog/03d37f93a6e8f349-678491c57a43ad59.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhF_gOMnbxw9kL2dBUBS8iZPh5dd3OnRzwZr121Pkn3PgsGDKKhsLVY3TkKIWhhwwa0oBD7dHya3EoW85H1mXvNzlwqff7AxeK1oZkkV3dLvvMbYql17Zyhpcj_HWUqXv_N4JT4KWyt3Tih/s1600/photo_2020-04-11_15-04-40+%25284%2529.jpg)  
*MQTT Dash та погода.*

#### Максимум / Мінімум

Якщо треба відслідковувати максимальні мінімальні значення, наприклад температури, то зберігаємо їх данні в об'єктах скрипт обробки. О 4:10 щодня, макс. мін. значення фіксуюються поточними значеннями.

on receive:
  

```
if (!event.data) { event.data = {}; } 
obj=JSON.parse(event.payload).main;
event.data['fl'] = obj.feels_like;
event.data['temp'] =obj.temp;
d=new Date();
m=d.getMinutes();
h=d.getHours();
r=(h==4 && m==10);
if (r || !event.data['tmax']) 
 event.data['tmax'] = obj.temp;
if (r || !event.data['tmin']) 
 event.data['tmin'] = obj.temp;
if (event.data['temp']>event.data['tmax'] )
  event.data['tmax']=event.data['temp'];
if (event.data['temp']<event.data['tmin'] )
  event.data['tmin']=event.data['temp'];
```

on display:
  

```
if (event.data) {
t=event.data['tmax']
+"\n[ "+event.data['temp']
+" ]\n"+event.data['tmin'];
event.text=t;
}
```

Для скидання значень "max/min" до поточних.
  
on tap:
  

```
if (!event.data) { event.data = {}; } 
if (event.data['temp']){
event.data['tmax'] = event.data['temp'];
event.data['tmin'] = event.data['temp'];
}
```

  

#### Дата / час

Для показу поточного часу можна використати [JavaSctipt](https://uk.wikipedia.org/wiki/JavaScript) клас [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date).

on display:
  

```
d=new Date();
event.text=d.toTimeString().split(' ')[0];
```

[![](/assets/images/blog/76ec2fdeb62fa4d5-93fe88bf9c03170a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSt-_M6T8xffNQtBTaI-fi1TSqtxJyam48rorfYebOj30CDpngmPlMsDJm7BWagXJGCjtEem4mHaoI7M979Utml2UPfBDRl_PdbxF05NxeN76vLaMppo3dV8hdrNqMFG_QXIliI12MH2x_/s1600/mq11.jpg)  
*Температура зі значеннями Максим./[ Поточ. ]/Мінім. , поточний час*

#### Усі налаштування

Для використання Export / Import налаштувань через топік: *metrics/exchange* можна використати мої усі налаштування з очищеними значеннями для *lastPayload* за допомогою редактору [jsoneditoronline.org](https://jsoneditoronline.org/):

```
[
  {
    "decimalPrecision": 0,
    "displayPayloadValue": true,
    "maxValue": 100,
    "minValue": 0,
    "postfix": "%",
    "prefix": "",
    "progressColor": -64,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "",
    "jsonPath": "main.humidity",
    "lastJsonPathValue": "57",
    "lastPayload": "",
    "qos": 1,
    "retained": false,
    "topic": "wrt/temp-inet",
    "topicPub": "",
    "updateLastPayloadOnPub": true,
    "id": "c28934a3-37bc-4e2f-9565-7ad1d70d0b04",
    "jsBlinkExpression": "",
    "jsOnDisplay": "",
    "jsOnTap": "",
    "lastActivity": 1587754200,
    "longId": 4,
    "name": " Вологість",
    "type": 3
  },
  {
    "mainTextSize": "SMALL",
    "postfix": "",
    "prefix": "",
    "textColor": -49088,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "if (!event.data) { event.data = {}; } \n \nobj=JSON.parse(event.payload).main;\nevent.data['fl'] = obj.feels_like;\nevent.data['temp'] =obj.temp;\nd=new Date();\nm=d.getMinutes();\nh=d.getHours();\nr=(h==4 && m==10);\n\nif (r || !event.data['tmax']) \n event.data['tmax'] = obj.temp;\nif (r || !event.data['tmin']) \n event.data['tmin'] = obj.temp;\nif (event.data['temp']>event.data['tmax'] )\n  event.data['tmax']=event.data['temp'];\nif (event.data['temp']= 11",
    "jsOnDisplay": "val=99",
    "jsOnTap": "",
    "lastActivity": 1587754200,
    "longId": 7,
    "name": "Вітер, м/с",
    "type": 1
  },
  {
    "imageUrl": "http://openweathermap.org/img/wn/04n@2x.png",
    "kind": 2,
    "openUrl": "",
    "reloadInterval": 300,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "if (!event.data) { event.data = {}; } \nobj=JSON.parse(event.payload).weather[0];\nif (!obj.icon.startsWith('http')){\n event.data['ico'] = 'http://openweathermap.org/img/wn/'+obj.icon+'@2x.png';\n}\nevent.data['tit'] = obj.description;",
    "jsonPath": "weather[0].icon",
    "lastJsonPathValue": "04n",
    "lastPayload": "",
    "qos": 0,
    "retained": false,
    "topic": "wrt/temp-inet",
    "topicPub": "",
    "updateLastPayloadOnPub": true,
    "id": "66f4abfc-3c59-4932-be4c-22f7a421516f",
    "jsBlinkExpression": "",
    "jsOnDisplay": "if (event.data['ico']!==event.url){\n event.url=event.data['ico'];\n}\nevent.name=event.data['tit'];",
    "jsOnTap": "",
    "lastActivity": 1587754200,
    "longId": 3,
    "name": "Зобр. погоди",
    "type": 5
  },
  {
    "imageUrl": "https://meteo.ua/var/zip/Sputn-24.jpg?u=62501",
    "kind": 1,
    "openUrl": "",
    "reloadInterval": 3600,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "",
    "jsonPath": "weather[0].icon",
    "qos": 0,
    "retained": false,
    "topic": "wrt/temp-inet",
    "topicPub": "",
    "updateLastPayloadOnPub": true,
    "id": "120b696a-04f5-45a3-82f1-a87991cfefef",
    "jsBlinkExpression": "",
    "jsOnDisplay": "",
    "jsOnTap": "",
    "lastActivity": 1587754193,
    "longId": 12,
    "name": "",
    "type": 5
  },
  {
    "mainTextSize": "MEDIUM",
    "postfix": "",
    "prefix": "",
    "textColor": -4144960,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "if (!event.data) { event.data = {}; } \n \nobj=JSON.parse(event.payload);\nevent.data['r'] = obj.roentgen;\nevent.data['z'] =obj.zievert;",
    "jsonPath": "roentgen",
    "lastJsonPathValue": "10",
    "lastPayload": "",
    "qos": 1,
    "retained": false,
    "topic": "wrt/rad-inet",
    "topicPub": "",
    "updateLastPayloadOnPub": true,
    "id": "e512c0d1-0cf2-47d2-be05-18d835091824",
    "jsBlinkExpression": "val>11",
    "jsOnDisplay": "event.text=event.data['z']+\"\\n\"\n+event.data['r'];\n\nc='#ffffff';\nv=event.data['z'];\nif (v > 0 && v < 50 ){\n c='#00D0FC';\n} else if (v >= 50 && v < 100){\n c='#B4FF33';\n} else if (v >= 100 && v < 150){\n c='#BFFF33';\n} else if (v >= 150 && v < 200){\n c='#FF6000';\n} else if (v >= 200 && v < 250){\n c='#FF5E33';\n} else if (v >= 250){\n c='#FF0000';\n}\n\n\n\nevent.textColor = c;",
    "jsOnTap": "",
    "lastActivity": 1587754193,
    "longId": 16,
    "name": "Радіація,                 нЗв/год, мРн/год",
    "type": 1
  },
  {
    "mainTextSize": "MEDIUM",
    "postfix": "",
    "prefix": "",
    "textColor": -1,
    "enableIntermediateState": true,
    "enablePub": false,
    "enteredIntermediateStateAt": 0,
    "intermediateStateTimeout": 0,
    "jsOnReceive": "",
    "jsonPath": "",
    "qos": 0,
    "retained": false,
    "topic": "",
    "topicPub": "",
    "updateLastPayloadOnPub": true,
    "id": "81bffc3d-ab44-4772-ae8a-ee99279ec439",
    "jsBlinkExpression": "",
    "jsOnDisplay": "d=new Date();\nevent.text=d.toTimeString().split(' ')[0];",
    "jsOnTap": "",
    "lastActivity": 0,
    "longId": 15,
    "name": "Поточний час",
    "type": 1
  }
]
```

Використати можна як зберігти ці налаштуваня до файлу, напиклад export.json, і опублікувати до топіку : metrics/exchange.  

```
mosquitto_pub -h <host>  -u <user>  -P <password>  -t  metrics/exchange -r  -f ./export.json
```

#### Наступні плани

Додати за датчиком руху, повідомлення то брокера, коли є рух у приміщенні і за цим повідомленням вмикати екран планшету на певний час.
