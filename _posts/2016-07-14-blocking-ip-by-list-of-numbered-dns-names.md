---
layout: post
title: "Blocking ip by list of numbered dns names"
date: 2016-07-14 15:36:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/07/blocking-ip-by-list-of-numbered-dns.html
---

Щось дістали записи у протокольних фалах від не бажаної пошти, вирішив заблокувати по IP.  
Таким скриптом перебираю адреси, шукаю усі їх IP, колекціюю до файлу, сортую у пошуках унікальних, і передаю адресу скрипту з блокування адрес фаєрволом на певний час.

```
#!/bin/sh

echo "START"
for i in $(jot - 1 99); do
 hip="m$i.esputnik.com";
 ip=$(host -t a $hip | awk '{print $4}');
 for addr in $ip
  do
  echo "$addr" >> block_ip_loop.txt
 done
done

for addr in $(sort -u  block_ip_loop.txt).
do
 echo "blocking $addr"
 ./ipfw-blockip-by-hand.sh 9999999 $addr
done
rm  block_ip_loop.txt
echo "END"
```
