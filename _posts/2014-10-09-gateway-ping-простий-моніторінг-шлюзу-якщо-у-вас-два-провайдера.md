---
layout: post
title: "Gateway Ping, простий моніторінг \"шлюзу\" якщо у Вас два провайдера"
date: 2014-10-09 15:53:00 +0000
tags: ["FreeBSD", "interfaces", "kernel", "net", "route", "router", "script", "WAN"]
blogger_orig_link: https://lexxai.blogspot.com/2012/07/gateway-ping.html
---

Взяв і дещо модифікував скрипт, до FreeBSD, що перевіряє працездатність інтернет провайдерів.  
  
Умови:  
Є два ISP провайдери, підключення іде за роутерами.  
  
Завдання:  
Якщо ping перестає іти за основним маршрутом, то перемикаємося на допоміжний маршрут іншого провайдера.  
Якщо відновися основний провайдер то повертаємо маршрутизацію.  
  
Реалізація:  
При компіляції ядра встановлена підтримка FIB=2.  

```
/usr/src/sys/i386/conf/GENERIC:
#multipath routing setfib
options ROUTETABLES=2
```

Налаштовуванні маршрути для setfib 0 та setfib 1.  

```
setfib 0 route add -net default ${setfib0_defaultroute} 
setfib 1 route add -net default ${setfib1_defaultroute}
```

  
  
Перевірмо провайдера за допомогою  
*setfib 0 ping $TESTIP* , *setfib 1 ping $TESTIP*, відповідно для кожного з провайдерів.  
Змінюємо маршрути за допомогою *route change default $GW*, згідно з адресою шлюзу провайдера.  
Ось цей скрипт допоможе автоматизувати цей процес.  
Де, TESTIP1,TESTIP2 відповідні IP адреси для перевірки провайдерів.   
  
**gwping.sh:**  
  

```
#!/bin/bash
# Copyright Angsuman Chakraborty, Taragana. Permission is granted for personal, non-commercial use.
# The script may not be re-distributed in any form without written permission 
# from Angsuman Chakraborty ( angsuman@taragana.com ).
# The script may be modified for personal use.
# THIS SOFTWARE IS PROVIDED ``AS IS'' AND WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES, 
# INCLUDING, WITHOUT LIMITATION, THE IMPLIED WARRANTIES OF MERCHANTIBILITY AND FITNESS 
# FOR A PARTICULAR PURPOSE. THE AUTHOR ACCEPTS NO RESPONSIBILITY IN ANY CONCEIVABLE MANNER.

# Conventionally 0 indicates SUCC in this script.

# Time between checks in seconds
SLEEPTIME=15

#IP Address or domain name to ping. The script relies on the domain being 
#pingable and always available

# for FIB0
TESTIP1=207.188.5.44

#for FIB1 
TESTIP2=195.5.5.203

#Ping timeout in seconds
TIMEOUT=15

#Gateway IP addresses. This is the first (hop) gateway, could be your router IP 
#address if it has been configured as the gateway
GW1=192.168.0.254
GW2=192.168.1.254

#Define setfib number for every ISP
sfIP1=0
sfIP2=1

# Broadband providers name; use your own names here.
NAME1=ISP 1
NAME2=ISP 2
```

```
 
```

```
# Waring EMAIL
```

```
WEMAIL=postmaster@someemail.ua
```

```
 
```

```
# No of repeats of SUCC or FAIL before changing status of connection
SUCCRCNT=3
FAILRCNT=5

# Do not change anything below this line
# Last link status indicates the macro status of the link we determined.
# This is down initially to force routing change upfront. Don't change these values.
LLS1=1
LLS2=1

# Last ping status. Don't change these values.
LPS1=1
LPS2=1

# Current ping status. Don't change these values.
CPS1=1
CPS2=1

# Change link status indicates that the link needs to be changed.
# Don't change these values.
CLS1=1
CLS2=1

# Count of repeated up status or down status. Don't change these values.
COUNT1=0
COUNT2=0

while : ; do
    setfib $sfIP1 ping -t $TIMEOUT -c 5 -o  -q $TESTIP1 > /dev/null  2>&1
    RETVAL=$?
    if [ $RETVAL -ne 0 ]; then
         echo `date` $NAME1 Down
         CPS1=1
     else
         CPS1=0
    fi
    if [ $LPS1 -ne $CPS1 ]; then
        echo `date` Ping status changed for $NAME1 from $LPS1 to $CPS1
        COUNT1=1
    else
      if [ $LPS1 -ne $LLS1 ]; then
         COUNT1=`expr $COUNT1 + 1`
      fi
    fi
    if [[ $COUNT1 -ge $SUCCRCNT || ($LLS1 -eq 0 && $COUNT1 -ge $FAILRCNT) ]]; then
       echo `date` Uptime status will be changed for $NAME1 from $LLS1
       CLS1=0
       COUNT1=0
       if [ $LLS1 -eq 1 ]; then
         LLS1=0
       else
         LLS1=1
       fi
    else 
       CLS1=1
    fi
    LPS1=$CPS1
    setfib $sfIP2 ping -t $TIMEOUT -c 5 -o  -q  $TESTIP2 > /dev/null  2>&1
    RETVAL=$?
    if [ $RETVAL -ne 0 ]; then
        echo `date` $NAME2 Down 
           CPS2=1
        else
           CPS2=0
        fi
        if [ $LPS2 -ne $CPS2 ]; then
        echo `date` Ping status changed for $NAME2 from $LPS2 to $CPS2
           COUNT2=1
        else
           if [ $LPS2 -ne $LLS2 ]; then
              COUNT2=`expr $COUNT2 + 1`
           fi
        fi
        if [[ $COUNT2 -ge $SUCCRCNT || ($LLS2 -eq 0 && $COUNT2 -ge $FAILRCNT) ]]; then
          echo `date` Uptime status will be changed for $NAME2 from $LLS2 
          CLS2=0
          COUNT2=0
            if [ $LLS2 -eq 1 ]; then
                LLS2=0
            else
                LLS2=1
            fi
    else
        CLS2=1
    fi
    LPS2=$CPS2
    if [[ $CLS1 -eq 0 || $CLS2 -eq 0 ]]; then
        if [[ $LLS1 -eq 1 && $LLS2 -eq 0 ]]; then 
            echo `date` Switching to $NAME2
            echo Set default route $GW2
            route change default $GW2
            mail -s "[GW] changed" $WEMAIL <<< $(echo `date` Set default route $NAME2: $GW2) 
        elif [[ $LLS1 -eq 0 && $LLS2 -eq 1 ]]; then
            echo `date` Switching to $NAME1
            echo Set default route $GW1
            route change default $GW1
            mail -s "[GW] changed" $WEMAIL <<< $(echo `date` Set default route $NAME1: $GW1)
        elif [[ $LLS1 -eq 0 && $LLS2 -eq 0 ]]; then
            echo `date` Restoring default load balancing
            echo Set default route $GW1
            route change default $GW1
            mail -s "[GW] changed" $WEMAIL <<< $(echo `date` Restoring... $NAME1: $GW1)
        fi
    fi
sleep $SLEEPTIME
done
```

  
Ініціалізація скрипту  
**gwping\_init:**  

```
#!/bin/sh
# lexxai.pp.ua
#
# PROVIDE: gwping-init
# REQUIRE: NETWORKING LOGIN
#

. /etc/rc.subr
name=gwping_init
manscript="/usr/local/etc/manage_scripts/"
command="${manscript}gwping.sh"
pidfile="/var/run/${name}.pid"
start_precmd="setup"
start_postcmd="getpid"
stop_cmd="stopk"

stopk(){
 kill `cat ${pidfile}`
 rm  ${pidfile}
 rm ${manscript}nohup.out
}

getpid(){
 echo `ps -x -o pid,command | grep "gwping.sh" | head -n 1 | sed 's/ \/.*//'` > ${pidfile}
}

setup() {
    cd ${manscript}
    command="nohup ${command}&"
}

run_rc_command "$1"
```

P.S.  
  
У FreeBSD 10 у мене працює і без компіляції ядра для FIB.  
  
/boot/loader.conf:
  

```
net.fibs=2
```

  
А маршрути можна і так описати для FIBs  
  
rc.conf:  

```
defaultrouter="192.168.0.254"
static_routes="defIsp1"
route_defIsp1="default 192.168.1.254 -fib 1"
```

  
P.S.S. Додано оповіщення на пошту.
