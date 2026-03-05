---
layout: post
title: "Нотатка. Shell, PHP script. GeoIP detect TimeZone."
date: 2020-05-02 00:27:00 +0000
tags: ["api", "bash", "date", "GeoIP", "MaxMind", "php", "script", "timezone", "unix"]
blogger_orig_link: https://lexxai.blogspot.com/2020/05/shell-script-geoip-detect-timezone.html
---

Скрипт за IP адресою, визначає часовий пояс.   
ip-timezone-info.sh  

```
!/bin/bash

IP=$1

if [ -z "${IP}" ];then
 echo "-- Missed paramter of IP"
 exit
fi


function install()
{
howinstalled=$(apt list --installed 2>/dev/null  | grep  "^$1" | wc -l)
if [ "${howinstalled}" -lt 1 ] ;then
 echo !!!!!!!!! MISSED FILE "$1", please install it : sudo apt install $1
 sudo apt-get install $1
 #exit
fi
}

install mmdb-bin

TZCity=$(mmdblookup --file GeoLite2-City.mmdb --ip ${IP} location time_zone |\
 awk 'match( $0, /"([a-z|A-Z|/]*)"/ ,arr ) { print  arr[1] }' )
TZone=$(TZ=${TZCity} date +%z)
echo "${TZCity} GMT${TZone%??}"
```

  
  
  

### Приклад:

```
./ip-timezone-info.sh 8.8.8.8
America/Chicago GMT-05 
```

#### Оновлення бази

ip-update.sh:
  

```
#!/bin/bash

LICENSE_KEY=XXXXXXXXXXXXXXXXXX
URL="https://download.maxmind.com/app/geoip_download?edition_id=GeoLite2-City&license_key=${LICENSE_KEY}&suffix=tar.gz"
mkdir -p "./IPUPDATES"
wget -qO- "${URL}" | tar -xvz -C ./IPUPDATES --wildcards "*/*.mmdb"
mv  ./IPUPDATES/*/*.mmdb /usr/local/share/GeoIP/
rm -f ./IPUPDATES
```

#### PHP

[MaxMind GeoIP2 PHP API](https://maxmind.github.io/GeoIP2-php/)

[GeoIP2 PHP API v2.10.0](https://maxmind.github.io/GeoIP2-php/doc/v2.10.0/classes/GeoIp2.Record.Location.html)

```
<?php
require 'geoip2.phar';
use GeoIp2\Database\Reader;

$ip=$_SERVER['REMOTE_ADDR'];
//$ip="8.8.8.8";
try {
 $reader = new Reader('/usr/local/share/GeoIP/GeoLite2-City.mmdb');
 if ( !$reader ) return;
 $record = $reader->city($ip);
 $tzone = $record->location->timeZone;
 $tz = new DateTimeZone($tzone);
 $dateTimeIP = new DateTime("now", $tz);
 $timeOffset = timezone_offset_get ($tz, $dateTimeIP)/3600;
 if (isset($tzone)){
  print($tzone);
  if (isset($timeOffset)){
   print(" GMT". sprintf("%+'03d", $timeOffset));.
  }
  print("\n");.
 }
}
catch (Exception $e) {
    //echo 'ERROR: ',  $e->getMessage(), "\n";
}
```

### Приклад:

```
America/Chicago GMT-05 
```
