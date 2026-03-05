---
layout: post
title: "Script for change interface bridge members to be private"
date: 2019-05-24 00:19:00 +0000
tags: ["ethernet", "FreeBSD", "FreeNAS", "network", "security"]
blogger_orig_link: https://lexxai.blogspot.com/2019/05/script-for-change-interface-bridge.html
---

[![](/assets/images/blog/ed32bc8e1a629eaf-a1a7b4835d356679.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEji7L3U_-YsL4j5VQqtpCcAr7DzEp2w-WUAa8wMg9DeH854QyLDIkgSq7n-tf-kBjKM9PDRgZDEvjCBD2Iy54cDezSPRV59Obxed0GcYHH2ir2j7lAKbJumivAiJ_JefJYyTDA7eP5mfTvl/s1600/bsd-bridge.png)

При створенні віртуальних середовищ використовуючи jails FreeBSD , наприклад у FreeNAS,  
стала задача ізолювати створенні jails у локальному сегменті мережі.  
Так FreeNAS для створення jails почав використовувати пакунок 'iocage', що досить ефективно автоматизує роботу з jails.  
Результатом роботи  у мене створенні декілька jails котрі ізольовані в окрему мережу котра  використовує VLAN.  
Результатом є створення мережевого моста bridge до котрого додані члени jails і інтерфейс роутера.  
  
  

```
$>ifconfig bridge0
bridge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 9000
        ether 02:00:00:07:19:00
        nd6 options=1<PERFORMNUD>
        groups: bridge
        id 00:00:00:00:00:00 priority 32768 hellotime 2 fwddelay 15
        maxage 20 holdcnt 6 proto rstp maxaddr 2000 timeout 1200
        root id 00:00:00:00:00:00 priority 32768 ifcost 0 port 0
        member: vnet0:4 flags=943<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 9 priority 128 path cost 2000
        member: vnet0:3 flags=943<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 11 priority 128 path cost 2000
        member: vnet0:2 flags=943<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 10 priority 128 path cost 2000
        member: vlan100 flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 7 priority 128 path cost 20000
```

  
Таким чином jails ізольовані від локальної мережі, але не ізольовані один від одного.  
Для ізоляцію можна скористатися [Bridge Interface Parameters](https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/network-bridging.html) - private:  
A private interface does not forward any traffic to any other port that is also designated as a private interface. The traffic is blocked unconditionally so no Ethernet frames will be forwarded, including ARP packets. If traffic needs to be selectively blocked, a firewall should be used instead.  
  
Тому я додав скрипт котрий модифікує існуючи bridge0 і додає private до кожного члена мосту що належать jail (vnet) окрім роутера (vlan100).  

```
#!/bin/sh
brigdeid=0
res=$(ifconfig bridge${brigdeid} | grep "member: vnet" | grep -v "PRIVATE" | grep -E vnet[0-9]:[0-9]* -o )
for i in ${res}; do
 ifconfig bridge${brigdeid} private ${i}
done 
```

Тепер результат роботи:  

```
$>ifconfig bridge0
bridge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 9000
        ether 02:00:00:07:19:00
        nd6 options=1<PERFORMNUD>
        groups: bridge
        id 00:00:00:00:00:00 priority 32768 hellotime 2 fwddelay 15
        maxage 20 holdcnt 6 proto rstp maxaddr 2000 timeout 1200
        root id 00:00:00:00:00:00 priority 32768 ifcost 0 port 0
        member: vnet0:4 flags=943<LEARNING,DISCOVER,PRIVATE,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 9 priority 128 path cost 2000
        member: vnet0:3 flags=943<LEARNING,DISCOVER,PRIVATE,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 11 priority 128 path cost 2000
        member: vnet0:2 flags=943<LEARNING,DISCOVER,PRIVATE,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 10 priority 128 path cost 2000
        member: vlan100 flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
                ifmaxaddr 0 port 7 priority 128 path cost 20000
```

Для тестування можна запустити  ping з одного jail до іншого.  
І тимчасово додавати або видаляти private для членів мосту [https://www.freebsd.org/cgi/man.cgi?ifconfig(8).](https://www.freebsd.org/cgi/man.cgi?ifconfig(8))  
Наприклад:   

```
ifconfig bridge0 private vnet0:4
ifconfig bridge0 -private vnet0:4
```
