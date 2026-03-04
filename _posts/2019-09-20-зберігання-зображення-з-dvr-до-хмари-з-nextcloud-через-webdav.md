---
layout: post
title: "Зберігання зображення з dvr до хмари з nextcloud через webdav"
date: 2019-09-20 00:55:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/09/dvr-nextcloud-webdav.html
---

[![](/assets/images/blog/7f1ef0eab09ff42b-26c67f58ef67149e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqT9UKFI-5629Ay7C4u7KMhKhbLVF7N6w-osTfpXDlbaoLoLsazpaUXEq5IFKqR9zj8g9wdD_ZsN0omzVXWWMguRmZTqjws1ZA6xtYdYDHJxgzFjbrTWZCoKLCnpNLnDNQ088IZHXL0DCt/s1600/cloud2.png)

Є задача скидати копію зображень з відеореєстратора системи безпеки у хмару.  
Є роутер  TP-Link TL-WR1043N/ND v2 з [OpenWrt](https://openwrt.org/) 18.06.2.  
Є власна віддалена хмара на основі [nextcloud](https://nextcloud.com/) ([owncloud](https://owncloud.org/)).  
У хмарі створюємо користувача для вивантажування зображень, і авторизуємося.  
У openwrt будемо отримувати доступ до хмари через [webdav](https://docs.nextcloud.com/server/16/user_manual/files/access_webdav.html).  
  

#### Для цього встановимо пакунок davfs2.

```
opkg install davfs2
```

Налаштування davfs2 знаходяться у файлі:  

```
/etc/davfs2/davfs2.conf
```

додаємо рядок щоб не використовувати блокування, [так як з цим є проблеми](https://github.com/owncloud/core/issues/9176) у nextcloud  

```
use_locks 0
```

Створюємо файл з паролем, і надаємо відповідні права :  

```
touch /etc/davfs2/secrets
chmod 600 /etc/davfs2/secrets
vi  /etc/davfs2/secrets
```

Додаємо до файлу secrets параметри підключення:   

```
https://cloud.url/remote.php/webdav/ cloudusername cloudpassword
```

#### Отримання з реєстратора інформації про час та дані про переміщення

Для цього встановимо пакунок curl.

```
opkg install curl
```

Так як оперативної пам'яті в пристрої не багато, прийшлося перейти з python або php до скриптів /bin/sh котрі запускаються з певним інтервалом. Код не оптимальний і заточний під виявлення тільки двох каналів, які відображаються у квадраторі зображення у DVR.
Але для отримання статусу про зміни в реальному часі, і запис відео у форматі .dv4 потрібне вже використання sockets, і python скрипти працюють але за умови включення файлу під качки наприклад через swapon. Якщо у Вас є зовнішній накопичувач HDD то це не проблема, але якщо флеш диск (як у мене), то це пряма дорога до "вбивання" накопичувача постійними циклами запису.  
/root/getImages-wait.sh:   

```
#!/bin/sh
securityhost=DVR.url
securityport=80
securityuser=dvradmin
securitypwd=dvrapwd

url_machine="http://${securityhost}:${securityport}/cgi-bin/nobody/Machine.cgi?action=get_capability"
url_smartm="http://${securityhost}:${securityport}/cgi-bin/guest/SmartMonitor.cgi"

#--------------------
function islive_pingdvr()
{
#test security device on live state.
ping ${securityhost}  -c 1 -W 2 > /dev/null 
if [ $? -gt 0 ];then
 echo host  ${securityhost} down.
 needwait=1
 sleep 5
else
 needwait=0
fi
}

function get_dvrmachine()
{
dvrmachine=$(curl -s \
   --max-time 1\
   -X GET ${url_machine} | head -n 2 | tail -n 1)
if [  -z "${dvrmachine}" ];then
  echo " error get machine , may be device frozen ?"
  sleep 5
  needwait=1
else
  needwait=0
fi
}

#------------------
needwait=0
while  true ; do
 echo Start ping... 
 islive_pingdvr
 if [ ${needwait} -eq 0 ];then
   get_dvrmachine
 fi
 if [ ${needwait} -eq 0 ];then
  break; 
 fi 
done

echo Start monitoring
while true; do
triggerd=0
triggered_ch=0
triggered_time=0
while read result; do
  #echo "READ: ${result}"
  remainder=$result
  first="${remainder%%=*}"; remainder="${remainder#*=}"
  second="${remainder%%=*}"; remainder="${remainder#*=}"
  case ${first} in
    "SmartMonitor") 
        #echo SM is $second
       if [ "${second}" == "Start"  ];then
        triggerd=1
       fi
       ;;
    "Channel") 
        #echo CH is $second
       if [ ${triggerd} -eq 1 ];then
        triggered_ch=$second
       fi
       ;;
    "Time") 
        #echo Time is $second
       if [ ${triggerd} -eq 1 ];then
        triggered_time=$(date  -D'%Y/%m/%d %H:%M:%S' -d "${second}" +%s)
       fi
       ;;
  esac
done << EOF
 $(curl -s \
   --list-only \
   --keepalive-time 30 \
   --max-time 0 \
   --ignore-content-length \
   --user ${securityuser}:${securitypwd} \
   -H "Content-Type: application/x-www-form-urlencoded" \
   -H "Connection: keep-alive" \
   --user-agent "AVTECH/1.0" \
   -X POST ${url_smartm} | egrep "^SmartMonitor=Start|^Channel=|^Time=")
EOF

# MAIN ENGINE AFTER EVENT
#echo +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++ loop

if [ ${triggerd} -eq 1 ];then
 #echo Triggered ${triggered_ch} ${triggered_time}
 /root/getImg.sh  ${triggerd} ${triggered_ch} ${triggered_time} & >/dev/null
fi

done
```

  
Даний скрипт завантажує з реєстратора дані про переміщення у форматі:
  

```
0
OK
SmartMonitor=Alive
Channel=0x2

0
OK
SmartMonitor=Start
Channel=02
Time=2019/09/28 17:25:22
```

  
Знаходить зміну у періщенні коли SmartMonitor=Start і запускає скрипт завантаження зображень getImg.sh.  
  
Хороший приклад від іншого автора є код написаний на php: [AVTECH Motion Detection link to Domoticz](https://www.domoticz.com/forum/viewtopic.php?t=17448).  

#### Підготування теки для монтування:

```
mkdir /mnt/dav
touch  /mnt/dav/.notmounted
```

#### Отримання зображень з реєстратора

У кожного [він може бути свій](https://lexxai.blogspot.com/2018/12/avtech-dvr-hiden-commands.html) URL для завантаження. Так для отримання JPEG зображень з реєстратора я використовую протокол http.  

```
http://security.url/cgi-bin/guest/Video.cgi?media=JPEG&resolution=4CIF
```

Цей запит може потребувати авторизації користувача. Його можна передати програмою завантаження, або у URL як http://username:password@security.url/....  

#### Скрипт для завантаження getImg.sh:

```
#!/bin/sh
if [ -z "$1" ];then
 echo need parameters, exit
 exit 
fi

securityhost=DVR.url
securityport=80
securityuser=dvradmin
securitypwd=dvrapwd

url_media="http://${securityhost}:${securityport}/cgi-bin/guest/Video.cgi?media=JPEG&resolution=4CIF"
url_machine="http://${securityhost}:${securityport}/cgi-bin/nobody/Machine.cgi?action=get_capability"
url_smartm="http://${securityhost}:${securityport}/cgi-bin/guest/SmartMonitor.cgi"

triggerd=$1
triggered_ch=$2
triggered_time=$3

moving_delay=12
deltatime=0

# LIB -----------------------------------

function get_dvrmachine()
{
dvrmachine=$(curl -s \
   --max-time 1\
   -X GET ${url_machine} | head -n 2 | tail -n 1)
if [  -z "${dvrmachine}" ];then
  echo " error get machine , may be device frozen ?"
  exit
fi
}

#--------------------
function islive_pingdvr()
{
#test security device on live state 
ping ${securityhost}  -c 1 -W 2 > /dev/null ; 
if [ $? -gt 0 ];then
 echo host  ${securityhost} down 
 exit
fi
}

#--------------------
function check_mounted()
{
if [ -f /mnt/dav/.notmounted ] ;then
 echo "/mnt/dav umounted, exit"
 exit
fi
}

#--------------------
function check_remotefilesystem()
{
#check mouning remote file system
if [ -f /mnt/dav/.notmounted ] ;then
 echo not mounted, try mount
 /usr/sbin/mount.davfs -o noexec https://cloud.ctph.com.ua/remote.php/webdav /mnt/dav
fi
check_mounted
}

#--------------------
function check_remotefilesystem_video()
{
if [ ! -d /mnt/dav/video ] ;then
 echo "/mnt/dav/video umounted, exit"
 /usr/sbin/umount.davfs /mnt/dav/
 kill $(cat /var/run/mount.davfs/mnt-dav.pid)
 rm /var/run/mount.davfs/mnt-dav.pid
 rm -r /tmp/davfs2/*
 exit
fi
}

#--------------------
function make_folders()
{
mdate=$(date '+%Y-%m-%d')
mhdate=$(date '+%Y-%m-%d-%H')
odir=/mnt/dav/video/${mdate}/${mhdate}

if [ ! -d ${odir} ] ;then
 mkdir -p ${odir}
fi
}

#--------------------
function loging()
{
  echo ${cdate}: ${get_motion_output} >> /root/getvideo.log
}

#--------------------
function get_image()
{
  cdate=$(date '+%Y-%m-%d-%H%M%S')
#  loging
  suff="-${triggered_time}-${triggered_ch}"
  fname="${cdate}${suff}.jpg"
#  echo $fname
  wget -q -T 6 \
    -O ${fname}  \
    -P ${odir} \
    --user=${securityuser} \
    --password=${securitypwd} \
    ${url_media}
}

# MAIN ----------------------------------

#islive_pingdvr
#get_dvrmachine
check_remotefilesystem
make_folders
check_remotefilesystem_video

if [ ${triggerd} -eq 1 ];then
 #echo Triggered ${triggered_ch} ${triggered_time}
 ltime=$(date '+%s')
 until [ ! $(($(date '+%s')-${ltime})) -le ${moving_delay} ] ; do
  #echo try load image `date`
  check_mounted
  get_image
  #sleep 5 
done
fi
```

Цей скрипт перевіряє чи змонтована тека, для вивантаження /mnt/dav  
Створює підтеку з поточним днем та для оптимізації використання dav підтеку з поточної годиною.  Завантажує зображення jpg до неї у форматі поточної дати і часу.  
повторює завантаження декілька разів.  
Зберігаємо скрипт до домашньої теки /root і даємо права на виконання  

```
chmod +x /root/getImg.sh
```

#### Для очищення старих версій файлів (30 діб) створюємо скрипт purgeold.sh:

```
#!/bin/sh
if [ -f /mnt/dav/.notmounted ] ;then
 mount.davfs  https://cloud.ctph.com.ua/remote.php/webdav /mnt/dav
fi
if [ -f /mnt/dav/.notmounted ] ;then
 exit
fi
/usr/bin/find /mnt/dav/video -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;
```

Зберігаємо скрипт до домашньої теки /root і даємо права на виконання   

```
chmod +x /root/purgeold.sh
```

#### Запускаємо скрипти за розкадом

```
crontab -e
```

```
0 0 * * *   /root/purgeold.sh
```

[![](/assets/images/blog/adc1febcca4f6397-8b76357620b33ecc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjT3y4_lPD7PRhJ9UB5OvMTETYGyMHMYd2znAQCG-XypvtGUydcn3aciuOdoWAjOK6xtjT7GIyrLwq69uBUdSS1kQ9RLOLF0rxCL8QnDsoq1n35pJOAPFzuwfcSIzObw2PWDWQAaeSvEn8f/s1600/cloud1.png)  
*Вивантаженні файли у хмарі*

В результаті у хмару вивантажуються зображення з реєстратора з інтервалом кожні 10 сек, зберігаються у теках по дням, термін зберігання 30 діб.  
  
За матеріалами:
  

* [Монтування хмарного диска по WebDav | Erinome Lane](http://tt.erinome.net/2012/11/360)
* [AVTECH DVR HIDDEN COMMANDS](https://lexxai.blogspot.com/2018/12/avtech-dvr-hiden-commands.html)

  
[Scripts on github.com](https://github.com/lexxai/storing-DVR-images-to-remote-storage)
