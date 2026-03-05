---
layout: post
title: "Нотатки : Простий скрипт для MQTT в OpenWRT : відсилання Telegram повідомлень з Emoji. Tasmota."
date: 2021-01-10 01:38:00 +0000
tags: ["api", "bash", "Emoji", "Mosquitto", "MQTT", "openwrt", "script", "shell", "SONOFF", "TASMOTA", "telegram"]
blogger_orig_link: https://lexxai.blogspot.com/2021/01/mqtt-openwrt-telegram-emoji.html
---

Маю роутер з #[OpenWRT](https://uk.wikipedia.org/wiki/OpenWrt), і локально інстальованим брокером #[MQTT](https://uk.wikipedia.org/wiki/MQTT)  #[mosquitto](https://mosquitto.org/).

[![](/assets/images/blog/d09d4d1adc9ba07f-e34d34d24853f9a5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtvNncFWd1shulMsfItcIcqA-LUgt2Ze_3HdX2aT_sAasHX8xo24TfKaQA57Q5PiMkBpa89SGtZV3NUY0xKCSJieGzS-iPqdaBjaV98laxvZIvyglXgd6gnvjR0gqRc8KRQXUmGY1dpI9O/s989/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-10+03-11-31.png)  
*Роутер - OpenWRT, ext USB disk, RAM 64M*

#### Сповіщення

Що б виконати простіші операції з сповіщення в телеграм каналі, написаний
даний скрипт, що надсилає до телеграм каналу повідомлення при певних
умовах.  
Такі умови можуть бути від різних сенсорів розумного будинку,
наприклад:

* стан вхідних дверей : зачинені / відчинені
* стан замку в дверях : закрито / відкрито
* стан охорони :  поставлено на охорону / знято з охорони
* стан пристрою - датчику руху : працює / не працює
* стан пристрою - лампи освітлення : працює / не працює

#### Emoji

Завдання це стан цих сенсорів показати відповідними зрозумілими зображеннями
використовуючи Emoji:

[![](/assets/images/blog/d9c22bb83272823b-46d626ffbd57c322.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh72ejzA34kwnE75nIF1alCfvwhWT36u58GM1P82jSaqqHFcdKjoMFnM7PrDxhdxby4pzwx_TaIIeEzyV4q2PWIMgLWf_zKvusKFJbFQHA34bPSjPS1_ts43Vfy7fa0d6djvno9BfYntjo_/s341/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-10+02-05-16.png)  
*Приклад використання зображень - емодзі*

Список доступних зображень
["Full Emoji List, v13.1"](https://www.unicode.org/emoji/charts/full-emoji-list.html)

#### OpenWRT скрипт mqtt-job.sh

В скрипті використовуються змінні для кожного зображення записаного в
байтовому форматі коду, наприклад: iON=$'\xf0\x9f\x9f\xa9'.

Для перекодування з Unicode до Bytes я використовував 
["Convert Unicode to Bytes - Online Unicode Tools"](https://onlineunicodetools.com/convert-unicode-to-bytes)
при цьому просто копіював зображення і вставляв через буфер і отримував код.

[![](/assets/images/blog/d7dfcc38a7ae23de-b22bdaee06564ed3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqnRRkWOiVuvglleOagAXhyphenhyphenSOyStLYAct9sjll2uB3nITFV9_EMPEm91U26OvHrvK7CqmFPeLmx5Ju2gg7He7H-sV_AJpxXdS2fxei70B7JWHZsG6GA4jX_fUAw2yDSkoNHaUqxJH3WFsN/s902/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-10+02-49-41.png)  
*Конвертування до bytes*

  
 Стан пристроїв публікуються до брокера
[#MQTT](https://uk.wikipedia.org/wiki/MQTT)
модифікованим пристроєм "[SONOFF R3](https://tasmota.github.io/docs/devices/Sonoff-Basic/#sonoff-basic-r3)" з прошивкою
[#Tasmota](https://tasmota.github.io/docs/) та
апаратними доопрацюваннями.

При зміні стану вхідних сенсорів посилається повідомлення до відповідних
топіків MQTT:

* door/closed
* door/opened
* door/locked
* door/armed
* door/alarm

Стан пристроїв датчику руху (pir\_01) та лампи освітлення (led\_01) публікується
у спеціальних топіках LWT:

* tele/pir\_01/LWT
* tele/led\_01/LWT

Тому скрипт підписався до прослуховування повідомлень від брокера MQTT використовуючи фільтри для топіків:

* door/#
* +/+/LWT

Також скрипт додатково прослідковує стан пристою WiFi лампи освітлення
(led\_01), і якщо стан топіку "tele/led\_01/LWT" стане Offline, то повідомляється
про це пристрою датчик руху (pir\_01) публікуючи до топіку "cmnd/pir\_01/event"
повідомлення "lwtled=Offline"

```
#!/bin/sh

function notify()
{
  #echo "to telegram $1"
  /root/telegram/totelegram.sh $1 $2 &
}

function publish()
{
 mosquitto_pub -t "$1" -m "$2"
}

#MAIN  UTF: #$'\360\237\224\224'

sleep 60
for i in 0 1 2 3 4 5 6 7 8 9 10; do
 if [ ! -d "/mnt/dav/video" ];then
  sleep 30
 else
  break
 fi
done

iON=$'\xf0\x9f\x9f\xa9'
iOFF=$'\xF0\x9F\x94\xB4'
iDoor=$'\xF0\x9F\x9A\xAA'
iLockON=$'\xf0\x9f\x94\x90'
iLockOFF=$'\xF0\x9F\x94\x93'
iBellON=$'\xF0\x9F\x94\x94'
iBellOFF=$'\xF0\x9F\x94\x95'
iOnline=$'\xf0\x9f\x99\x82'
iOffline=$'\xf0\x9f\x91\xbf'
iLED=$'\xf0\x9f\x94\x86'
iPIR=$'\xf0\x9f\x8f\x83'
iWarn=$'\xE2\x9A\xA0'
  
IFS=" "
while true; do
 mosquitto_sub -v -t 'door/#' -t '+/+/LWT'  -q 0  | \
  while read t v; do
    case "$t" in
     "door/closed")
      a=$(eval "echo \${i${v}}")
      notify "${iDoor}${a}" 0
     ;;
     "door/locked")
      a=$(eval "echo \${i${v}}").....
      b=$(eval "echo \${iLock${v}}")
      notify "${b}${a}" 0
     ;;
     "door/armed")
       a=$(eval "echo \${iBell${v}}")
       notify "$a" 0
     ;;
     "door/alarm")
       case "$v" in
        "close")
           a="${iWarn}${iDoor}${iOFF}"
           notify "$a" 0
        ;;
        "lock")
           a="${iWarn}${iLockOFF}${iOFF}"
           notify "$a" 0
        ;;
       esac
     ;;
     "tele/led_01/LWT")
       a=$(eval "echo \${i${v}}").....
       notify "${iLED}$a" 0
       publish "cmnd/pir_01/event" "lwtled=$v"
     ;;
     "tele/pir_01/LWT")
       a=$(eval "echo \${i${v}}").....
       notify "${iPIR}$a" 0
     ;;
    esac
  done
  sleep 5
  echo "restat loop"
done
```

Даний скрипт запускається в фоні при запуску роутера з OpenWrt, і працює постійно в режимі прослуховування відповіді від mosquitto\_sub.

[![](/assets/images/blog/48d2c8f957390e83-d88e40e4ae6210b9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvsXg7hy14KnWAFbCuGxKy_vQjnEhkhA9ich1OOeqA0wGcvrU8OBFtn70Lfnid3xD024k4Lvqc0QdrXvc6TtLxGTnFCNHLKLb9YY9uD86SkqjrV8iIut2_ZCbIqlmtRvITIUyPERqRnrYu/s1015/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-10+03-10-54.png)  
*Запуск скрипту при вмиканні роутера.*

  

#### OpenWRT - Telegram

Для відправлення повідомлень з роутера був створений телеграм бот, який може
відсилати повідомлення до відповідного каналу.

Для надсилання використовуються прості скрипти на основі використання API
Telegram через curl.

Для роботи скрипту використовується curl та jq, а результати номерів
повідомлень зберігаються у файлі хмари (/mnt/dav/telegram/telegram\_msgid.log)
яка змонтована за протоколом WebDev.

/root/telegram/totelegram.sh:

```
#!/bin/sh

MESSAGE="$1"
if [ -z "${MESSAGE}" ];then
 echo "message is empty"
 exit
fi
TOKEN="YYYYYY:XXXXX"
CHAT_ID='-100ZZZZZZZZZZ'
URL="https://api.telegram.org/bot$TOKEN/sendMessage"
LOG="/mnt/dav/telegram/telegram_msgid.log"
cdate=$(date '+%Y-%m-%d %H:%M:%S')
host="WRT"
if [ "$2" == "0" ];then
 MESSAGE1="${MESSAGE}"
else
 MESSAGE1="${cdate} [${host}]:${MESSAGE}$2"
fi
B=$(curl -s -X POST $URL \
 -d chat_id=$CHAT_ID -d text="${MESSAGE1}"|jq .result.message_id)
if [ ! -z "$B" ];then
 echo $B >> $LOG
fi
```

Також для видалення застарілих повідомлень з каналу телеграму, видаляються
раніше надісланні повідомлення і залишаються тільки останні 5.  
Скрипт
запускається раз на добу за розкладом через cron.

/root/telegram/telegram-delete-old.sh:

```
#!/bin/sh  

TOKEN="YYYYYY:XXXXX"
CHAT_ID='-100ZZZZZZZZZZ'
URL="https://api.telegram.org/bot$TOKEN/deleteMessage"
LOGFILE=/mnt/dav/telegram/telegram_msgid.log
cdate=$(date '+%Y-%m-%d %H:%M:%S')
LEAVED=5
if [ -n "$1" ];then
 LEAVED=$1
fi
echo LEAVED FILES: ${LEAVED}
MID=$(head -n -${LEAVED} ${LOGFILE})
#echo "MID ${MID}"
for id in ${MID}
do
 #echo "ID ${id}"
 r=$(curl ${URL} -s \
   --form-string chat_id=${CHAT_ID} \
   --form-string message_id="${id}" )
 if [ "$r" == '{"ok":true,"result":true}' ];then
  #echo "deleted MSG $id OK"
  grep -v $id ${LOGFILE} > ${LOGFILE}.tmp
  mv -f ${LOGFILE}.tmp ${LOGFILE}
 fi
done
```

#### Tasmota

Налаштування сенсорів:

[![](/assets/images/blog/b8155636724b178d-fbacec51ef99e7f3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRKlj4waM3O639RyH_8FOYABG-szbYjNyy2dyIdUCEGLMOosvlEcui-YMPY_IzZJaJr537TTCPhiAyGr193vtrFNX2N7wJFIgvsLYyQ6FnNWoQVy3xxRZ6NMmQkGuyRalAH_QuGpiKtlqA/s486/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-10+03-52-52.png)

  
Правила:  

```
Backlog
 SerialLog 0;
 Savedata 0;
 SetOption0 0;
 PulseTime1 3700;
 PowerOnState 0;
 Timezone +02;
 mem1 100;
 SetOption114 1;
 SwitchText1 PIR; 
 SwitchMode1 13;
 SwitchText3 DOOR;
 Switchmode3 2;
 SwitchText4 ARMED; 
 Switchmode4 1;
 SwitchText5 LOCK;
 Switchmode5 2;
    
Rule1 
 ON analog#Illuminance<25 1="" analog="" do="" endon="" lluminance="" on="" rule2="">25 DO Backlog Rule2 0;Power1 0 ENDON

rule1 5  
  
Rule2 
 ON PIR#Action=ON DO Backlog NoDelay;Power1 1;RuleTimer1 %mem1% ENDON

rule2 1
  
Rule3
 ON PIR#Action=ON DO publish cmnd/led_01/var2 P BREAK
 ON DOOR#Action DO Backlog Publish2 door/closed %value%;publish cmnd/led_01/var2 P;Power1 1 ENDON
 ON DOOR#Action=OFF DO RuleTimer1 %mem1% ENDON 
 ON ARMED#Action DO Publish2 door/armed %value% BREAK
 ON LOCK#Action DO Publish2 door/locked %value% BREAK
 ON Mqtt#Connected DO Backlog rule2 0;rule1 0;Power1 0 BREAK
 ON Mqtt#Disconnected DO Backlog rule2 1;rule1 1 BREAK
 ON event#lwtled=Offline DO Backlog rule2 1;rule1 1 BREAK
 ON event#lwtled=Online DO Backlog rule2 0;rule1 0;Power1 0 BREAK
 ON Rules#Timer=1 DO Power1 0 BREAK

rule3 1
```

GitHub:



* [mosquitto-shell-monitor](https://github.com/lexxai/mosquitto-shell-monitor)
