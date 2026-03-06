---
layout: post
title: "Нотатки : Простий скрипт для wifi device tracker в OpenWRT : відсилання повідомлень до MQTT"
date: 2021-01-31 01:56:00 +0000
tags: ["api", "bash", "Emoji", "Mosquitto", "MQTT", "openwrt", "script", "shell", "SONOFF", "telegram"]
blogger_orig_link: https://lexxai.blogspot.com/2021/01/wifi-device-tracker-openwrt-mqtt.html
---

Скрипт для ash сценарію в OpenWRT.  
Пошук активних WiFi пристроїв за їх MAC адресою, і якщо такий є то надсилання повідомлення до MQTT брокера зі станом пристрою при зміні стану. А [інший скрипт](/2021-01-10-нотатки-простий-скрипт-для-mqtt-в-openwrt-відсилання-telegram-повідомлень-з-emoji-tasmota.md) надсилає стан вже до Телеграм каналу..

[![](/assets/images/blog/4cc7aa71cbc5fc84-bd8198f39e977451.jpeg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCa8T0AQjYQlA7yXyQyfaoq5AqzLFSDQgXZUodt-ilZVHpQNeCABJqSnm8yUVzXB-aePKqHLNTy2IDNN4FvisDmR-sJMJIrA8PSoea6315KKuFCUpxaXvc85uYQCZuDG0XesCGgElQPYQY/s430/mqttw.jpeg)

  

```
#!/bin/sh

uUser1='00:13:33:44:11:77'
uUser2='00:3D:33:5D:11:39'
users="User1 User2"

next=

function publish()
{
 mosquitto_pub -t "$1" -m "$2" -r
}

#MAIN
sleep 120

for m in $users; do
  eval p${m}=0
done

while true; do
 for m in $users; do
  um=$(eval "echo \${u${m}}")
  us=$(iwinfo wlan0 assoclist | grep -m 1 -c $um )
  ps=$(eval "echo \${p${m}}")
  if [ "$us" -ne $ps ];then
   eval p${m}=$us
   if [ -n "$next" ];then
     publish "tele/wifi_$m/LWT" "$us"
   fi
  fi
 done
 next=1
 sleep 90
done
```

Так як масивів в ash не має, то приходиться використовувати eval для  симуляції віртуального масиву.

Створив версію для LUA: [Перший код в LUA. Скрипт для роутера з OpenWRT. MQTT та Telegram статус підключених WiFi clients.](/2021-12-17-перший-код-в-lua-скрипт-для-роутера-з-openwrt-mqtt-та-telegram-статус-підключених-wifi-clients.md)
