---
layout: post
title: "OpenWrt Attitude Adjustment канали WiFi 12,13"
date: 2013-02-16 19:27:00 +0000
tags: ["ar71xx", "firmware", "openwrt", "Wi-Fi", "Wireless"]
blogger_orig_link: https://lexxai.blogspot.com/2013/02/openwrt-attitude-adjustment-wifi-1213.html
---

Для вирішення проблеми з використанням у OpenWrt Attitude Adjustment каналів WiFi 12,13 метод з підміною файлу regulatory не підходить, потрібно компілювати під себе прошивку. А ось знайшов метод без компіляції, а методом "патчування" файлів ath.ko, cfg80211.ko.  
  
На цьому сайті <http://luci.subsignal.org/~jow/reghack/> є інструкція  [README.txt](http://luci.subsignal.org/~jow/reghack/README.txt). Згідно неї файл [reghack.elf](http://luci.subsignal.org/~jow/reghack/reghack.elf) робить зміни у вище згаданих файлах.  
  
Я повторив та перевірив на версії "OpenWrt Attitude Adjustment r33276", дійсно працює - канали 12,13 стали доступні для використання зараз навіть якщо стоїть країна US.  
  

```
cd /tmp/
wget http://luci.subsignal.org/~jow/reghack/reghack.elf
chmod +x reghack.elf
cp /lib/modules/*/ath.ko .
cp /lib/modules/*/cfg80211.ko

./reghack.elf ath.ko
Patching @ 0x000001ec: radar frequency check
Patching @ 0x0000251c: ath world regdomain with 5 rules in ath/regd.o
Patching @ 0x0000259c: ath world regdomain with 4 rules in ath/regd.o
Patching @ 0x00002604: ath world regdomain with 4 rules in ath/regd.o
Patching @ 0x0000266c: ath world regdomain with 5 rules in ath/regd.o

./reghack.elf cfg80211.ko
Patching @ 0x0001f520: core world6 regdomain in cfg80211/reg.o
Patching @ 0x0001fe70: embedded 00 regdomain in cfg80211/regdb.o
Patching @ 0x000227c8: embedded US regdomain in cfg80211/regdb.o

mv *.ko /lib/modules/*/
reboot
```
