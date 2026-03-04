---
layout: post
title: "Нотатка:  Динамічний таймер на базі \"розумної\" розетки SONOFF S26 з Tasmota та MQTT"
date: 2022-01-15 02:20:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2022/01/sonoff-s26-tasmota-mqtt.html
---

Є завдання, вмикати освітлення у приміщенні тамбура перед квартирою, за певними персоналізованими умовами.

[![](/assets/images/blog/8321bd847cb4b411-c9c534e61d83ca6c.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEhJDt_-svAwrrXC3O2lyMkS1gpAE5OO6E8UZH7WUE6sXWN6lbEraMPNZiZfz9DjBaAd_YzEIampMLXL3V26Qo1VXUf9eukvearCxALMJ3fGzqBO3kGlD3uwnASxGypT1AR2PGCE_QvO_j8g53xoI9UGrRFLQFeOuYdveECkTK86IIpt2IIDQbFZBlgqLg=s2605)  
*SONOFF S26 - Tasmota*

#### Умови

* ручне керування: вимикання, вмикання на короткий час, на довгий час
* віддалене керування через MQTT брокер, вимикання, вмикання на короткий час, на середній час,на довгий час.
* вимикання завжди за аварійним або контрольованим таймером

#### Реалізація

Модифікуємо прошивку пристрою на альтернативну - [Tasmota](https://tasmota.github.io/docs/devices/Sonoff-S26-Smart-Socket/)

[![](/assets/images/blog/8321bd847cb4b411-fe0e661db091c9f2.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEj6r6YO5s-uY8iKUQUMhQlnH16KCQcYvFHzd4QtmfJc0-m00NGtv7sM-PnNVtKL85LPgih7s6spPAImo6rAdCh_TURYz5PEhoMfRjg7cGxGspcdXSsivd6Rut7H_5Lwh680dJWhZG68myxJ6Nh-vtlwNvPNBxBakTexz_3SjBEhLr_ToizyQ0PzjrQNHQ=s1038)

  

[![](/assets/images/blog/8321bd847cb4b411-89a4cd02d73aec0c.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEjEhLVaBspArrXG5UJtthQc_odvmJ-Jr_-PZTBqaEpHHsVLNEva3QwGJ4IULBsHlrP5T1Jv7FMZt1ClhIIZjQRRqIXCV4cdp0WB4S5plVraXzOZnYbqU5f_v4B-997agxPrDVQhq5jhRICyYUGSW_525OkqVL7nYvj_xFChwt0w6dwifpaiE53XFW7YZg=s811)

Після цього етап первинних налаштувань [Tasmota](https://tasmota.github.io/docs/), створення шаблону, додавання правил автоматизації.

[![](/assets/images/blog/8321bd847cb4b411-c8e13dbb6684754c.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEjIUDLPvB2Ige4ofKSBNWT5Zb36ZuLHREf9chO_aW3deobrdS2I5Iw28DSoocpkuKBk5r49rSFwRruwPD7TNCt8WWf8sasT7mFoGrvnNSlOAWDLQ-yJykAvMHDrXimmLQfYVx3bvXA7U2mEVyUKuAOpXr_sUFVmpzRM7a3-cQY0y-Zktxo3FFC-xN9Fgw=s858)  
*Шаблон з модифікацію для кнопки*

  

[![](/assets/images/blog/8321bd847cb4b411-03e2af651fb32a86.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEgdiAh86amrQyVpvY3jCeC4oAL4m6hjmXsVxRdoUxEsSUW_8Ss8iKJ0hp1eJC0lrWaekidouZFRupleAVGEjnqU1PZFVq5Gnw-P5IB2tMugYBGt6qL7CKMYDus4h7gCBO8jQe1iarHxnQ1v5mrSaEqxDTQO7p6_GqgVx5vXpX45C6gcmfUP4RrbQ2JD2w=s563)  
*Основне меню*

Надалі в меню Console виконую команди налаштувань і додавання правил.

#### Rules

```
rule1 
 ON power1#state=0 DO PulseTime1 444 ENDON
 ON event#short DO backlog0 PulseTime1 280;power 1 BREAK
 ON event#mid DO backlog0 PulseTime1 400;power 1 BREAK
 ON event#long DO backlog0 PulseTime1 1900;power 1 BREAK
 ON button2#state=11 DO event mid=1 BREAK
 ON button2#state=2 DO power 0 BREAK
 ON button2#state=3 DO event long=1 BREAK
rule1 1
backlog PulseTime1 444;PowerOnState 0;SetOption0 0;LedState 0;sleep 75;savedata 0;
restart 1
```

#### Опис правил

За зміною шаблону для Button - 1 на 2, я відокремлюю багатофункціональну обробку дій на натискання клавіші пристою.

При поодинокому натисканні клавіші - вимикання, два рази - вмикання на термін 300 секунд, довготривале натискання (4 сек) - вмикання на довгий термін 1800 секунд.

Аварійний термін ввімкненого стану є 344 сек.  Він описаний в системі при старті як PulseTime1 444, а також значення оновлюється через правила при кожному вимкненні реле пристою.

```
ON power1#state=0 DO PulseTime1 444 ENDON
```

Натискання на клавішу пристрою обробляється за ***button2#state***, де state: 2 - просте поодиноке коротке натискання, 11 - подвійне натискання, 3 - довго тривале (HOLD) натискання.

Для логіки роботи подвійного натискання, генерується нова подія - mid, для довгого натискання - long.

```
ON button2#state=11 DO event mid=1  BREAK
ON button2#state=3  DO event long=1 BREAK
```

А просте натискання кнопки - вимикає реле

```
ON button2#state=2 DO power 0 BREAK 
```

Події обробляються в rule1 так:

```
ON event#short DO backlog0 PulseTime1 280; power 1 BREAK
ON event#mid   DO backlog0 PulseTime1 400; power 1 BREAK
ON event#long  DO backlog0 PulseTime1 1900;power 1 BREAK
```

Де є послідовність команд без затримки для корегування таймеру PulseTime1, і ввімкнення реле - backlog0 : PulseTime1 N; power 1. Де: N - різна травились таймеру ввімкненого стану (час в секундах + 100)

Також встановленні наступні параметри через команди в консолі:

* rule1 1 - ввімкнути обробку правила 1
* PowerOnState 0 - при ввімкнені пристрою завжди реле вимкнено.
* SetOption0 0 - не зберігати зміни налаштувань автоматично
* LedState 0 - режим синього світлодіоду - максимально вимкнено
* sleep 75 - зменшення швидкості роботи контролера у сторону меншого споживання енергії
* savedata 0 - не зберігати стан стану реле.
* restart 1 - перезавантажити пристрій зі збереженням налаштувань

#### Віддалене керування через MQTT

За попередніми публікаціями ([Перший код в LUA. Скрипт для роутера з OpenWRT. MQTT та Telegram статус підключених WiFi clients](/2021/12/lua-openwrt-mqtt-telegram-wifi-clients.html), [Нотатки : Простий скрипт для MQTT в OpenWRT : відсилання Telegram повідомлень з Emoji. Tasmota.](/2021/01/mqtt-openwrt-telegram-emoji.html)  ) я описував як створив дуже простий контролер для обробки невеликої кількості команд MQTT, на базі скрипту у домашньому роутері на базі OpenWRT.

Таким чином я відсилаю команди брокеру MQTT для пристрою S26-01, для різних станів:

```
mosquitto_pub -t "cmnd/S26-01/event" -m "long=1"  
mosquitto_pub -t "cmnd/S26-01/power" -m "0"
```

І тут я показую частину скрипту з роутера котрий аналізує стан дверей (door/closed, door/locked), і стан присутності людей в зоні дії WiFi (tele/wifi\_users/LWT) через топіки брокера.  

```
iDoor=$'\xF0\x9F\x9A\xAA'
iLockON=$'\xf0\x9f\x94\x90'
iLockOFF=$'\xF0\x9F\x94\x93'
iON=$'\xf0\x9f\x9f\xa9'
iOFF=$'\xF0\x9F\x94\xB4'
iCLED=$'\xF0\x9F\x94\xA6'
IFS=" "

while true; do
 mosquitto_sub -v -t 'door/#' -t 'stat/S26-01/POWER' -t 'tele/+/SENSOR' -t '+/+/LWT'  -q 0  | \
  while read t v; do
    #echo "readed topic:$t, message:$v"
    case "$t" in
     "door/closed")
      a=$(eval "echo \${i${v}}")
      notify "${iDoor}${a}" 0
      if [ "$v" == "OFF" ];then
        publish "cmnd/S26-01/event" "long=1" "-q 1"
      fi
     ;;
     "door/locked")
      a=$(eval "echo \${i${v}}")
      b=$(eval "echo \${iLock${v}}")
      notify "${b}${a}" 0
      stateLocked=$v
      if [ "$stateLocked" == "ON" ];then
       publish "cmnd/S26-01/event" "short=1" "-q 1"
      fi
     ;;
     "tele/wifi_users/LWT")
         if [ "$v" -gt 0 ]; then
          if [ "${stateDual2P1}" == "OFF" ]; then
            stateDual2P1=ON
            publish "cmnd/S26-01/event" "long=1" "-q 1"
          fi
         else
          stateDual2P1=OFF
          publish "cmnd/S26-01/power" "0" "-q 1"
         fi
     ;;
     "stat/S26-01/POWER")
       a=$(eval "echo \${i${v}}")
       notify "${iCLED}${a}" 0
     ;;
       done
  sleep 5
  echo "restat loop"
done 
```

#### Сповіщення в Telegram

Так виглядає реальне сповіщення про стан дверей, замку дверей, та освітлення приміщення за дверми квартири (S26-01).

[![](/assets/images/blog/8321bd847cb4b411-bcb9bdcba723b50b.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEgJHGA1mkcxi4JxPEGcP7mULKh1cMnRhT48zg6cZ_ezkaK5MGkIC9zjmaczxYurqFqZdwa0BeaIRB0AHO_csVyw6kNdDL38f8sVeedlbxaOZIwZC8TUpr6SUMNMKCeT8XHue1uko-xYWCD4mGV_MbTWBVPu0ZPPlShfEsjQi3JEAcFmv24FIIKqCcyPQA=s591)  
*Статуси роботи в телеграм*

  
P.S. Так як тамбур спільний то не було використано простий датчик руху, а застосовувано більш персональне рішення ввімкнення/вимкнення. Надалі буде додано пошук пристою за Bluetooth (телефон, розумні годинки, фітнес трекери, [iBeaсon](https://en.wikipedia.org/wiki/Bluetooth_low_energy_beacon)) щоб визначати чи знаходиться близько свій-чужий пристрій у додаток до знань про WiFi клієнтів.
