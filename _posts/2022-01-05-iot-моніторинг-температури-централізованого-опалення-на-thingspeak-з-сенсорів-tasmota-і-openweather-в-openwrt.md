---
layout: post
title: "IoT. Моніторинг температури централізованого опалення на ThingSpeak з сенсорів Tasmota і OpenWeather в OpenWRT"
date: 2022-01-05 03:23:00 +0000
tags: ["IOT", "Monitoring", "MQTT", "open-wrt", "OpenWeather", "openwrt", "script", "shell", "TASMOTA", "thingspeak"]
blogger_orig_link: https://lexxai.blogspot.com/2022/01/iot-thingspeak-tasmota-openweather.html
---

## Графік змін температур



## Поточні температури

Поточна температура централізованого опалення

Поточна температура на вулиці

#### OpenWrt + OpenWeather + MQTT + ThingSpeak

```
#!/bin/sh

appid=........................
cityid=706483
topic=wrt/temp-inet
user=user01
passw=passw01

m=$(wget -q -O - "https://api.openweathermap.org/data/2.5/weather?id=${cityid}&appid=${appid}&units=metric&lang=ua")
echo $m |  mosquitto_pub -h 127.0.0.1 -u ${user} -P ${passw} -t ${topic}  -s -q 1 -r
m=$(echo ${m} | iconv -t ASCII -c )

. /usr/share/libubox/jshn.sh

json_load $m
json_select main
json_get_var B temp
#echo $B
api_key=...........
wget -q -O - "https://api.thingspeak.com/update?api_key=${api_key}&field2=${B}" 2>1 >/dev/null
```

OpenWeather MQTT JSON

```
wrt/temp-inet {"coord":{"lon":36.25,"lat":50},"weather":[{"id":741,"main":"Fog","description":"туман","icon":"50n"},{"id":601,"main":"Snow","description":"сніг","icon":"13n"}],"base":"stations","main":{"temp":-0.01,"feels_like":-4.3,"temp_min":-0.07,"temp_max":-0.01,"pressure":1005,"humidity":100},"visibility":500,"wind":{"speed":4,"deg":130},"snow":{"1h":1.15},"clouds":{"all":90},"dt":1641353924,"sys":{"type":1,"id":8905,"country":"UA","sunrise":1641360770,"sunset":1641390441},"timezone":7200,"id":706483,"name":"Kharkiv","cod":200}
```

#### OpenWrt + Tasmota + MQTT + ThingSpeak

```
#!/bin/sh

. /usr/share/libubox/jshn.sh
api_key=...........

function publish_data_1()
{
 json_load "$1"
 json_select DS18B20
 json_get_var B Temperature
 wget -q -O - "https://api.thingspeak.com/update?api_key=${api_key}&field1=${B}" 2>1 >/dev/null &
}
  
IFS=" "
while true; do
 mosquitto_sub -v -t  'tele/+/SENSOR' -t '+/+/LWT'  -q 0  | \
  while read t v; do
    case "$t" in
.....
     "tele/DualR2/SENSOR")
         publish_data_1 "${v}"
     ;;
     esac
  done
  sleep 1
done
```

Tasmota MQTT JSON

```
tele/DualR2/SENSOR {"Time":"2022-01-05T05:44:16","DS18B20":{"Id":"02131E2E4A22","Temperature":46.2},"TempUnit":"C"}
```

#### За матеріалами:

* [[OpenWrt Wiki] jshn: a JSON parsing and generation library in for shell scripts](https://openwrt.org/docs/guide-developer/jshn)
