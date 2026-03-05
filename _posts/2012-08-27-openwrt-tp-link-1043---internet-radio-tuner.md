---
layout: post
title: "OpenWRT, TP-Link 1043 - INTERNET RADIO tuner"
date: 2012-08-27 10:55:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/08/openwrt-tp-link-1043-internet-radio.html
---

OpenWRT, TP-Link 1043 -  простий інтернет радіо приймач  
  
Продовжуючи експерименти з [OpenWrt Attitude](http://lexxai.blogspot.com/2012/08/tp-link-wr1043n-openwrt-attitude.html) додав можливості використовувати Router TP-Link 1043, як Інтренет радіо тюнер.  
Навіщо ? По-перше цікаво, в друге іноді включаю радіо на вихідних але комп'ютер принципово не вмикаю - відпочиваю від нього, і по-третє це економія електроенергії,  бо мій комп'ютер: CPU Intel i7, RAM 24 GB... використовує електроенергії достатньо.  
  
Як було вказанно у попередній публікації я маю, док сктанцію:  
Notebook Dock Model #1500325A, що може через USB - підключати деякі
мультимедійні пристрої: LPT (PL-2305H), COM (PL-2303HX), SOUND 5.1
(C-Media Electronics, Inc. Storm HP-USB500 5.1 Headset), Ehternet (ASIX
AX88772), USB Hub.  

[![](/assets/images/blog/80603912b90c3a0b-5a8d856e95401372.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga-Pkr6zGFwnUhEJmV9274uqZr1CeOKXSqSV7o3VvWe7poIsQfsu2CWfVxRsoohFmgAgX_3wohLeRKtB39lRvKzoWzEQV917tIjdx0ocHBIdC3lAeSlsLNa_zl7VxjEE-tB9jMhe_kTVlu/s1600/IMG_7577.JPG)  
*Notebook Dock Model #1500325A*

  
  
  

[![](/assets/images/blog/50016aed45dcf3c7-4fea40a817a62175.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeh87CxcBs0hpQbuwaZg7G_q1VFCTaFVRRWW37HDLv1vevYNS3ZFfzgczVvJ5IUl6k3YCMkELlXeSooe7pnAB6mUw6dQvrkPVI7z-wT7lZ2B-F2ob_zJkv5PgIsKxvw4ylZSqEKnngzTda/s1600/IMG_7578.JPG)  
*Notebook Dock Model #1500325A*

  

[![](/assets/images/blog/b8dfc1dcc9cdaffc-e6c4804e88388aa1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh256wVmFTE3g6wnTIZiFM3tkbkwWmfijqXMwGdwWD-QleXb5B08UnFvp-PA9ifguhOZMUFU8Don0L-CjukmZsdZM2uw_kCnqhSqHIxnrb7_0KH5tRp2Suj9vyniRCxQDZwUtJFa-b047xs/s1600/IMG_7580.JPG)  
*Notebook Dock Model #1500325APL-2303HX. PL-2305H*

До док станції, на малюнку, підключено USB накопичувач, RS-232 (COM) кабель для MPCS, стерео вихід до аудіо системи, USB кабель CDMA терміналу, підключення до TP-Link 1043 через USB, та блок зовнішнього живлення. Ще також підключається принтер "EPSON R220" за потребою у вільний USB.  
  
Радіо прослуховувати можна такою командою, wget -c -q -O - $url | madplay. Якщо використовується mp3 кодек. Не підтримуються mp4, ogg. Для більшого функціоналу потрібно використовувати пакет [MPD](http://mpd.wikia.com/wiki/Music_Player_Daemon_Wiki).   
Я налаштував роутер так що - кнопка  роутера WPS може, вмикати радіо якщо утримувати її 5 сек, вимикати радіо якщо утримувати її 10 сек, та переключати послідовно радіостанції з заздалегідь створеного списку (.m3u), якщо утримувати її до 1 сек.  
  
***Робоча тека**:*  
*/etc/radiotuner*  
***Файли**:*   
*playlist.m3u* - m3u play list  
*radio-load.sh* - завантажує програвач до фонового програвання  
*radio-start.sh* - відтворює першу радіостанцію зі списку, або бажану, частина назва котрої була передана через параметр.  
*radio-stop.sh* - зупиняє програвач, та поточну радіостанцію.  
*radio-next-station.sh* - зупиняє поточну радіостанцію, і програвач переходить до наступної радіостанції.  
*/var/run/radiotuner.pid* - ідентифікатор запущеного процесу програвача.  
  
Використувується пакет madplay - MAD is an MPEG audio decoder.  

```
#opkg install madplay
```

radio-start.sh:
  

```
#!/bin/sh
#http://www.lexxai.pp.ua 
want=$1
pl=/etc/radiotuner/playlist.m3u
while true; do
 cat "$pl" | \
  while read url; do \
    curr=$url
    if [ $(echo "$url" | grep -E -v "^#") ];  then
      name=$(echo "$before"|awk -F , '/^#EXTINF/ {print ($2)}')
      if [  -z $want ] || [ ! -z $(echo "$name" |grep -oi -e "$want") ] ; then
       echo "play: $name" |logger -t 'online-radio'
       wget -c -q -O - $url 2>/dev/null| madplay -q  -
      fi
    fi
    before=$curr
  done
done
```

radio-next-station.sh:
  

```
#!/bin/sh
killall wget
```

radio-stop.sh:  

```
#!/bin/sh
killall radio-start.sh
killall wget
```

radio-load.sh:
  

```
#!/bin/sh
./radio-start.sh $1 &
```

playlist.m3u:
  

```
#EXTM3U
#EXTINF:0,"eradio.com.ua: E-radio"
http://62.149.13.129:8500/eradio_hi
#EXTINF:0,"eradio.com.ua: E-radio rock"
http://etoradio.cc.colocall.com:8500/e-rock_hi
#EXTINF:0,"MFM"
http://urg.adamant.net:8080/online128
#EXTINF:0,"uh-radio"
http://online.uhradio.com.ua:8001/efir
#EXTINF:0,"Radio 24"
http://stream1.lux.fm:9010
#EXTINF:0,"Radio Rocks"
http://online.radioroks.com.ua:8000/RadioROKS
#EXTINF:0,"DJFM"
http://media.brg.ua:8010/;stream.nsv
#EXTINF:0,"PerecFM"
http://radio.stilnoe.fm:8080/radio-stilnoe
#EXTINF:0,"Sharmanka"
http://217.20.164.163:8006/;
```

### Buttons

Використанні матеріали з [ http://wiki.openwrt.org/doc/howto/hardware.button ]  
  
Для керування плеєром використовуються можливості клавіші WPS, для її функціювання створимо файл:     
  
/etc/hotplug.d/button/00-button:
  

```
. /etc/functions.sh
do_button () {
        local button
        local action
        local handler
        local min
        local max
        config_get button $1 button
        config_get action $1 action
        config_get handler $1 handler
        config_get min $1 min
        config_get max $1 max
        [ "$ACTION" = "$action" -a "$BUTTON" = "$button" -a -n "$handler" ] && {
                [ -z "$min" -o -z "$max" ] && eval $handler
                [ -n "$min" -a -n "$max" ] && {
                        [ $min -le $SEEN -a $max -ge $SEEN ] && eval $handler
                }
        }
}
config_load system
config_foreach do_button button
```

І внесемо параметри для різного функціоналу з залежності від тривалості натискання клавіші WPS.  
  
/etc/config/system:   
  

```
config button
        option button 'wps'
        option action 'released'
        option handler '/etc/radiotuner/radio-next-station.sh'
        option min '0'
        option max '1'

config button
        option button 'wps'
        option action 'released'
        option handler '/etc/radiotuner/radio-load.sh'
        option min '3'
        option max '8'

config button
        option button 'wps'
        option action 'released'
        option handler '/etc/radiotuner/radio-stop.sh'
        option min '9'
        option max '14'

config button
        option button 'wps'
        option action 'released'
        option handler 'logger wifi, timed pressed: 19-50s'
        option min '19'
        option max '50'
```

  
  
Результати роботи протоколюються до лог файлу.  

```
#logread -f 
Aug 25 03:31:16 OpenWrt user.notice online-radio: next station
Aug 25 03:31:25 OpenWrt user.notice online-radio: Loading
Aug 25 03:31:25 OpenWrt user.notice online-radio: play: "eradio.com.ua: E-radio"
Aug 25 03:31:51 OpenWrt user.notice online-radio: next station
Aug 25 03:31:56 OpenWrt user.notice online-radio: play: "eradio.com.ua: E-radio rock"
Aug 25 03:32:15 OpenWrt user.notice online-radio: next station
Aug 25 03:32:19 OpenWrt user.notice online-radio: play: "MFM"
Aug 25 03:32:29 OpenWrt user.notice online-radio: next station
Aug 25 03:32:33 OpenWrt user.notice online-radio: play: "uh-radio"
Aug 25 03:32:39 OpenWrt user.notice online-radio: next station
Aug 25 03:32:43 OpenWrt user.notice online-radio: play: "Radio 24"
Aug 25 03:32:51 OpenWrt user.notice online-radio: next station
Aug 25 03:33:00 OpenWrt user.notice online-radio: play: "Radio Rocks"
Aug 25 03:33:07 OpenWrt user.notice online-radio: next station
Aug 25 03:33:11 OpenWrt user.notice online-radio: play: "DJFM"
Aug 25 03:33:16 OpenWrt user.notice online-radio: next station
Aug 25 03:33:23 OpenWrt user.notice online-radio: play: "PerecFM"
Aug 25 03:33:29 OpenWrt user.notice online-radio: next station
Aug 25 03:33:33 OpenWrt user.notice online-radio: play: "Sharmanka"
Aug 25 03:33:40 OpenWrt user.notice online-radio: next station
Aug 25 03:33:48 OpenWrt user.notice online-radio: play: "eradio.com.ua: E-radio"
Aug 25 03:33:59 OpenWrt user.notice online-radio: Already started
Aug 25 03:34:27 OpenWrt user.notice online-radio: stopping
```

  
  
Використана: OpwnWRT ATTITUDE ADJUSTMENT (Bleeding Edge, r33206.   
У якості USB Audio вважаю також можуть бути використанні, USB колонки, або USB sound card, [наприклад](http://www.amazon.com/External-Channel-Sound-Adapter-Notebook/dp/B000N35A0Y). Що будуть сумусні за Linux DSP.  
  
Додано з [форуму](http://cyber-place.ru/showthread.php?t=363&page=4): можна використати "curl [http://......](http://109.123.116.202:8022/) | madplay - "  
  
  
Цікаві посиланя до теми Інтернет радіо:  

* [Joseph Innovations: Internet Radio -> Router + USB Sound-card](http://josephinnovations.blogspot.com/2011/11/internet-radio-router-usb-sound-card.html)
* [Tinkernut.com : How To Make A Cheap WiFi Radio](http://youtu.be/Bp26xFBwrJs)
* [klasseonline : Using the Asus WL-500g as Internet Radio Station](http://klasseonline.aboehler.at/cms/index.php?option=com_content&view=article&id=48:wlanradio&catid=34:linuxpc&Itemid=53)
* [habrahabr : Сделай сам: Wi-Fi-радио своими руками](http://habrahabr.ru/post/61880/)
* [MightyOhm : Building a Wifi Radio](http://mightyohm.com/blog/2008/10/building-a-wifi-radio-part-1-introduction)
* [WI-FI радіоприймач власноруч з роутера TP-LINK TL-MR 3020](http://neperesychna.blogspot.com/2014/09/wi-fi-tp-link-tl-mr-3020.html)
