---
layout: post
title: "Нотатка. Оновлення запису DNS для DHCP CLIENT FreeBSD з використанням nsupdate"
date: 2022-02-03 00:32:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2022/02/dns-dhcp-client-freebsd-nsupdate.html
---

Є сервер FreeBSD, з використанням dhclient для мережевого інтерфейсу.

Задача, оновити  запис в власному сервері DNS з отриманим IP від DHCP сервером.

Встановлюю додатковий компонент pkg install samba-nsupdate.

[![](/assets/images/blog/8321bd847cb4b411-1e2077b2c16615a5.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEgMf7LR9kmYNtdididcrREs_On56d2iXDs9KRX2UaadB3jmc2Lfv7PMc-lt9ZZVpYM2kuvCP4WH86tCW2r-CElzeW8r_J1QWjXQAkCC1cAuBdCFTc-0qd5OYWs2vGV-JXqmsp0mo_GfZ9dT4kU8Z-xzBh99aNFqWhdWKiEq59DgvQUN7LhhhF_kd-OhcA=s718)  
*nsupdate*

#### dhclient

Створюю скрипт dhclient-exit-hooks для dhclient.

/etc/dhclient-exit-hooks

```
#!/bin/sh

/usr/bin/logger -t dhclient-exit-hooks REASON ${reason} IP: ${new_ip_address}
case "${reason}" in BOUND|RENEW|REBIND|REBOOT)
        echo ${new_ip_address} ${reason}
        /usr/local/etc/ddns/ddns.sh ${new_ip_address} ${reason}
;;
esac
```

 Створюю теку /usr/local/etc/ddns, там завантажую файл ключа ddns.key для оновлення DNS з RFC 2136 і також цей скрипт:

#### nsupdate

/usr/local/etc/ddns/ddns.sh

```
#!/usr/bin/env /bin/sh

IP=$1
KEY=/usr/local/etc/ddns/ddns.key
NSUPDATE=/usr/local/bin/samba-nsupdate
DNSSERVER=your_dns_server
DNSHOST=host.example.net

if [ ! -z ${IP} ];then
 echo updating:  ${IP} [ $2 ]  - ${USER}
 /usr/bin/logger -t dyndns updating:  ${IP} [ $2 ]  - ${USER}
 if [ -f ${KEY} ];then
  ${NSUPDATE} -4  -k ${KEY}  <<EOF
server ${DNSSERVER}  53
update delete ${DNSHOST}
update add ${DNSHOST} 300 A ${IP}
send
EOF
  else
   logger -t dyndns no key file ${KEY}
  fi
else
 echo IP missed
 logger -t dyndns IP missed
fi
```

#### BIND

ddns-confgen -a hmac-sha512 -z host.example.net

```
# To activate this key, place the following in named.conf, and
# in a separate keyfile on the system or systems from which nsupdate
# will be run:
key "ddns-key.host.example.net" {
        algorithm hmac-sha512;
        secret "SdimvwKG5SDqeS7hD+C6ap89sRN4I2EuYrJKHFXtJ83CuUF6kbELxrv5OgVeVctzyqbafcQTGJgQh/svkv+/Yg==";
};

# Then, in the "zone" definition statement for "host.example.net",
# place an "update-policy" statement like this one, adjusted as
# needed for your preferred permissions:
update-policy {
          grant ddns-key.host.example.net zonesub ANY;
};

# After the keyfile has been placed, the following command will
# execute nsupdate using this key:
nsupdate -k
```
