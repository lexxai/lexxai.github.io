---
layout: post
title: "FreeBSD , FIB"
date: 2014-06-20 20:11:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/06/freebsd-fib.html
---

[![](/assets/images/blog/3b5d456ae2e09d37-d6724a32ac0eef39.jpg)](http://itstolytsa.ua/public_files/freebsd.gif.jpg)

  
Раніше для ініціалізації маршрутів у FreeBSD для кожного з FIB, використовував додатковий скрипт, на кшталт цього setfib1 для rc.d:  
  
  
  
  

```
. /etc/rc.subr
name="setfib1"
rcvar=`set_rcvar`
load_rc_config $name
[ -z "$setfib1_enable" ] && setfib1_enable="NO"
[ -z "$setfib1_defaultroute" ] && setfib1_defaultroute=""
start_cmd="${name}_start"
stop_cmd="${name}_stop"
setfib1_start()
{
 if [ ${setfib1_defaultroute} ]
  then
   setfib 1 route add -net default ${setfib1_defaultroute}
  else
   echo "Can not set default route for fib 1 - setfib1_defaultroute is not assigned in rc.conf!"
  fi
}
setfib1_stop()
{
     setfib 1 route del -net default
}
run_rc_command "$1"
```

Зараз вивчаючи FreeBSD 10, занйшов такий приклад для визначення маршрутів для кожного з провайдерів через FIB у rc.conf:
  

```
static_routes="defISP1 defISP2"
route_defISP1="default 192.168.0.1 -fib 1"
route_defISP2="default 192.168.1.1 -fib 2"
```

  
Таким чином , можна не компілювати ядро з опцією ROUTETABLES   

```
echo 'net.fibs=2' >> /boot/loader.conf. 
```

```
 
```

```
#freebsd #FIB
```
