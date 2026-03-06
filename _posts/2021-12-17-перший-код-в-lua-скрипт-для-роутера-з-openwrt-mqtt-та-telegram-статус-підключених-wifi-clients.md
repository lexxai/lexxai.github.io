---
layout: post
title: "Перший код в LUA. Скрипт для роутера з OpenWRT. MQTT та Telegram статус підключених WiFi clients."
date: 2021-12-17 00:03:00 +0000
tags: ["adimistration", "LUA", "Mosquitto", "MQTT", "Open Source", "open-wrt", "openwrt", "router", "script", "telegram", "Wi-Fi", "wifi"]
blogger_orig_link: https://lexxai.blogspot.com/2021/12/lua-openwrt-mqtt-telegram-wifi-clients.html
---

З метою оптимізації [мого попереднього коду (Нотатки : Простий скрипт для wifi device tracker в OpenWRT : відсилання повідомлень до MQTT ) в SHELL script](/2021-01-31-нотатки-простий-скрипт-для-wifi-device-tracker-в-openwrt-відсилання-повідомлень-до-mqtt.md) так як його можливості дещо обмежені. Перевів код на [LUA -  швидка і компактна скриптова мова програмування](https://uk.wikipedia.org/wiki/Lua).

Мета коду - зробити автоматизацію вдома на основі присутності людей. Один з варіантів визначення присутності це за тим чи підключений мобільний телефон особи до домашньої [Wi-Fi](https://uk.wikipedia.org/wiki/Wi-Fi) мережі.

[![](/assets/images/blog/8321bd847cb4b411-84733cc81e475219.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEjysa66vBkv4ayeS7DYEXkStGJ3Vid0H7Y1LERj-9QX3oSpmW2ZknrJf6T0KDM1JzMrWFHLR7KBXny4b8GHqMBN8OXKJi28AcR7DpaI2sUHuWFXTQh_jJXb7AZrA58bX1uVYoprnOIOa7csAGLtYa2ewc_qbe-OdLJ_FAF8DDp8f09pUCUk42FR3hYDnQ=s480)  
*Результати роботи визначення статусу в телеграм групі*

Так як мобільний телефон при підключенні до мережі [Wi-Fi](https://uk.wikipedia.org/wiki/Wi-Fi) використовує унікальні [MAC адреси](https://uk.wikipedia.org/wiki/MAC-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%B0), то за активністю MAC адрес можна припустити по наявність особи вдома.

#### iwinfo

Прилад роботи команди в роутері: iwinfo

```
$ iwinfo wlan0 assoclist
AA:AA:AA:AA:AA:E6  -47 dBm / -93 dBm (SNR 46)  1860 ms ago
        RX: 6.0 MBit/s                                 47653 Pkts.
        TX: 72.2 MBit/s, MCS 7, 20MHz                    877 Pkts.
        expected throughput: 34.9 MBit/s

AA:AA:AA:AA:D3:12  -66 dBm / -93 dBm (SNR 27)  3224 ms ago
        RX: 6.0 MBit/s                                  7281 Pkts.
        TX: 120.0 MBit/s, MCS 5, 40MHz                  1461 Pkts.
        expected throughput: 39.1 MBit/s

AA:AA:AA:AA:58:39  -68 dBm / -93 dBm (SNR 25)  25836 ms ago
        RX: 24.0 MBit/s                                 8812 Pkts.
        TX: 65.0 MBit/s, MCS 6, 20MHz                   3922 Pkts.
        expected throughput: 27.9 MBit/s

AA:AA:AA:AA:34:77  -43 dBm / -93 dBm (SNR 50)  9316 ms ago
        RX: 6.0 MBit/s                                  3420 Pkts.
        TX: 72.2 MBit/s, MCS 7, 20MHz                   1050 Pkts.
        expected throughput: 34.9 MBit/s
```

В результаті вибираємо тарі параметри як "MAC" адреса, так час останньої активності "AGO".

За допомогою awk дещо фільтруємо результат виведення MAC, наприклад якщо активність була менша за останні 50 сек.

```
iwinfo wlan0 assoclist|awk '{if (($11) && ($9<50000)) print $1}'  
AA:AA:AA:AA:AA:E6  
AA:AA:AA:AA:D3:12  
AA:AA:AA:AA:58:39  
AA:AA:AA:AA:34:77
```

Тепер до скрипту в LUA - device-tracker.lua:

```
#!/usr/bin/lua
  
local os=require("os")  

users = {
["user1"] = 'AA:AA:AA:AA:34:77',
["user2"] = 'AA:AA:AA:AA:58:39'
}
ustate = {}
next = false

local function sleep(n)
  os.execute("sleep " .. tonumber(n))
end

local function publish (topic, mess)
 os.execute("mosquitto_pub -t '" .. topic .. "' -m '" .. mess .. "' -r -q 2" )
end

-- main code
sleep(120)
-- initialize previos users state tabe
for u,m in pairs(users) do
  ustate[u] = 0
end

while true do
  -- get list of active wifi mac addresess
 local us=io.popen( "iwinfo wlan0 assoclist|awk '{if (($11) && ($9<50000)) print $1}'" )
 maclist = {}
 for line in us:lines() do
  maclist[line]=true;
 end
 us:close()
 for u,m in pairs(users) do
  result = maclist[m] and 1 or 0
  if ustate[u] ~= result then
   ustate[u]=result
   if next then
     -- print( "state user :" .. u .. " changed" )
     publish("tele/wifi_" .. u .. "/LWT",result)
   end
  end
 end
 next=true
 sleep(5)
end
```

#### libiwinfo-lua

Якщо встановити пакунок OpenWRT - libiwinfo-lua : *opkg install libiwinfo-lua*, то необхідність запуску зовнішніх програм для визначення активних wifi клієнтів відпадає.

```
#!/usr/bin/lua
  
local os=require("os")  

-- opkg install libiwinfo-lua
local iwinfo=require("iwinfo")

-- MAC DataBase of users

local users = {
["user1"] = 'AA:AA:AA:AA:34:77',
["user2"] = 'AA:AA:AA:AA:58:39'
}
-- LIST of WiFi APs, 2.4GHz, 5GHz
local ifaces = {"wlan0"}

-- FUNCTIONS

local function sleep(n)
  os.execute("sleep " .. tonumber(n))
end

local function publish (topic, mess)
 os.execute("mosquitto_pub -t '" .. topic .. "' -m '" .. mess .. "' -r -q 2" )
end

-- MAIN CODE

local ustate = {}
local next = false

-- initialize previos users state tabe
for u,m in pairs(users) do
  ustate[u] = 0
end

-- list of wifi clients
local maxInactive = 70000
while true do
  -- get list of active wifi mac addresess
 local maclist = {}
 for _,iface in pairs(ifaces) do
  local ifacet = iwinfo.type(iface)
  local clients = iwinfo[ifacet].assoclist(iface)
  for mac,v in pairs(clients) do
   if v.inactive < maxInactive then
      -- print(iface, mac, v.inactive)
      maclist[mac]=true
   end
  end
 end
 for u,m in pairs(users) do
  local result = maclist[m] and 1 or 0
  if ustate[u] ~= result then
   ustate[u]=result
   if next then
     -- print( "state user: " .. u .. " changed: " .. result )
     publish("tele/wifi_" .. u .. "/LWT",result)
   end
  end
 end
 next=true
 sleep(5)
end
```

#### autorun

Скрипт автоматично завантажується при запуску системи.

[![](/assets/images/blog/8321bd847cb4b411-c6d45c0926e26795.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEgWW3pYNVaDuCsWai6N9rexQhzwN9M399tGQCQIBJ3ErGNjY_Jg7Wy8ag3A_gOL5K3m-kiexef-vkvklHiuX3_d030pkkxeQB9RDy0ZvxP6O4wwQSjlhGbLB8w79BGK0vRxi_B8tz_Eecpj_2ZXwXHcjn3xNt-rGn9copQxsl4YsRJj0qy-ROrkCAAIRg=s956)  
*Автоматичний запуск скрипту device-tracker.lua в OpenWRT*

#### mqtt

Результат роботи  є запуск команди: [mosquitto\_pub](https://mosquitto.org/man/mosquitto_pub-1.html), яка публікує повідомлення до [MQTT](https://uk.wikipedia.org/wiki/MQTT) брокера.  
В даному випадку [mqtt](https://mqtt.org/) брокер [mosquitto](https://mosquitto.org/) встановлений в цьому роутері.

Скрипт який відсилає повідомлення то Telegam було описано мною раніше: "[Нотатки : Простий скрипт для MQTT в OpenWRT : відсилання Telegram повідомлень з Emoji. Tasmota.](/2021-01-10-нотатки-простий-скрипт-для-mqtt-в-openwrt-відсилання-telegram-повідомлень-з-emoji-tasmota.md) "

#### lua-mosquitto

Якщо встановити пакунок OpenWRT - [lua-mosquitto](https://flukso.github.io/lua-mosquitto/docs/) : *opkg install lua-mosquitto*, функцію **publish** можна переписати так:

```
-- opkg install lua-mosquitto
local mqtt = require("mosquitto")

local function publish(t,m)
  client = mqtt.new()
  client.ON_CONNECT = function()
        client:publish(t,m,2,true)
  end
  client.ON_PUBLISH = function()
    client:disconnect()
  end
  client:connect()
  client:loop_forever()
end
```

Але розмір використаної оперативної пам'яті збільшиться в 2.4 рази, тому, я вважаю що недоцільно використовувати *lua-mosquitto* для одноразового використання, краще запустити зовнішню програму *mosquitto\_pub*.

[![](/assets/images/blog/8321bd847cb4b411-ff27807030986411.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEhEcELSF7VynQi1o6ApfW9ygq0daYzuGMqBW1Wv3McBrYnrVEw0ZHhEIVJm-_LlM1su1XxoMGhE6v0mzIas0bgJZmUwtt6YGcFZqrgUzjG1TcsLz7b2DmsEoIHBFoHwNOxkf5DTyk21OnBLmfDw12L3iT1BQQY4Gf9k1dmuIYmrnq_WLaGw0QJd2k5E7w=s703)

  

#### Оптимізація sleep

```
-- opkg install luasocket
local socket = require 'socket'

local function sleep(n)
  socket.sleep(tonumber(n))
--  os.execute("sleep " .. tonumber(n))
end
```

Розмір став 1916, але не має процесу sleep котрий займає 1250.
