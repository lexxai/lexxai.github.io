---
layout: post
title: "PF firewall log alarm"
date: 2014-02-25 21:17:00 +0000
tags: ["firewall", "FreeBSD", "pf", "security"]
blogger_orig_link: https://lexxai.blogspot.com/2014/02/pf-firewall-log-alarm.html
---

[![](/assets/images/blog/d94dbb99818e934c-234ae3c78acb716b.gif)](http://derekneely.com/wp-content/uploads/2009/06/openbsd.gif)

Завдання.  
Заносимо до таблиці pf  <attackers> значення адрес хто мав спроби підключитися до NTP служби у внутрішній мережі.  
І таким чином блокуємо повний доступ до сервера і локальної мережі цим адресам.  
Додатково використаний порт [Simple Log Watcher](http://sourceforge.net/projects/swatch/)  
  
  
pf.conf:
  

```
block in  quick on $ext_if from <attackers> to any
block out quick on $ext_if from any to <attackers>

block in log quick on $ext_if inet proto { tcp, udp } from any to $internal_net port ntp
block in log quick on $ext_if inet6 proto { tcp, udp } from any to $internal_net6 port ntp
```

pflog.sh:   

```
#!/bin/sh
killall tcpdump
tcpdump -N -n -e -t -i pflog0 '(ip6 or ip) and dst port 123 and not (dst host ntp.local)' | logger -t pf -p local0.info &
```

/etc/syslog.conf:  

```
local0.info                                     /var/log/pf.log
```

/var/log/pf.log:   

```
Feb 25 11:43:54 <local0.info> 12.12.12.1 pf: rule 55..16777216/0(match): block in on rl0: 111.111.111.11.80 > 12.12.12.12.123: NTPv2, Reserved, length 8
```

/etc/rc.conf:  

```
swatch_enable="YES"
swatch_rules="1"
swatch_1_flags="--tail-file=//var/log/pf.log --config-file=/usr/local/etc/swatch/.pf-alert  --daemon --pid-file=/var/run/swatch_1.pid"
swatch_1_pidfile="/var/run/swatch_1.pid"
```

/usr/local/etc/swatch/.pf-alert:  

```
watchfor /: block in on rl0: (.*) \> (.*): /i
    exec /usr/local/etc/pf-alert/parse_ip.php '$1|$2' >> /var/log/pf-block-alert.log
```

/usr/local/etc/pf-alert/parse\_ip.php:  

```
#!/usr/local/bin/php
<?php
$dir="/usr/local/etc/pf-alert";
$WL=$dir."/whitelist.txt";

/**
 * Validate an IPv4 IP address
 *
 * @param  string $ip
 * @return boolean - true/false
 */

function isWrongIP($ip)
{
   return ( false === filter_var($ip, FILTER_VALIDATE_IP, FILTER_FLAG_IPV4) );
}

/**
 * Validate an IPv6 IP address
 *
 * @param  string $ip
 * @return boolean - true/false
 */

function isWrongIPv6($ip) {
   return (false === filter_var($ip, FILTER_VALIDATE_IP, FILTER_FLAG_IPV6));
}


function array_find($needle, $haystack){
   foreach ($haystack as $item)
   {
      if (strpos($needle,$item) !== FALSE)
      {
         return true;
         break;
      }
   }
return false;
}

function parse_ip($src) {
    $k=preg_split("/\./", $src, -1, PREG_SPLIT_NO_EMPTY);
    $ip=implode('.',array_slice($k,0,-1));
    if (isWrongIP($ip) && isWrongIPv6($ip)){
            $ip=false;
    }
    return $ip;
}


##############
# store db
##############

function StoreToDatabase($IP){
    $info="";
    result=shell_exec( /sbin/pfctl -qv -t attackers -T add $ip | /usr/bin/grep -e "^A");
    result=shell_exec( /sbin/pfctl -qv -t attackers -T test $ip | /usr/bin/grep -e "^M");
    return $result;
}

#####################################
#   MAIN
#####################################

$T1=$argv[1];
$wlist=file($WL, FILE_SKIP_EMPTY_LINES | FILE_IGNORE_NEW_LINES);
list($src, $dest) = split('[|]', $T1);
echo "$src,  $dest"."\r\n";
$ip=parse_ip($src);
#check white list
if (!array_find($ip, $wlist)) {
    $res=StoreToDatabase($ip);
    echo "src ip=$ip, stored=$res\r\n";
}
```
