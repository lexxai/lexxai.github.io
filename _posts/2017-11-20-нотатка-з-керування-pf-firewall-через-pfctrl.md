---
layout: post
title: "Нотатка з керування PF firewall через pfctrl"
date: 2017-11-20 19:39:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/11/pf-firewall-pfctrl.html
---

[![](/assets/images/blog/2e85b51bb5193863-1e0320b3b90b936a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVrpBGgibZpXBUne-DaS0E9EUXkLtZof5QvFkbP9vMxXJRoDrROMzSxqB1SJPiT-6Nly6_fFpP7Dw28qyPeVolf-GK6YtL0k4_wjTPDWL6du4xEGNF-UO9E7SiRIabQeHYZDk_En_xCZhH/s1600/pfsence-logo.PNG)

  
https://gist.github.com/tracphil/4353170  

```
# basic pfctl control
# ==
# Related: http://www.OpenBSD.org
# Last update: Tue Dec 28, 2004
# ==
# Note:
# this document is only provided as a basic overview
# for some common pfctl commands and is by no means
# a replacement for the pfctl and pf manual pages.

#### General PFCTL Commands ####
# pfctl -d disable packet-filtering
# pfctl -e enable packet-filtering
# pfctl -q run quiet
# pfctl -v -v run even more verbose

#### Loading PF Rules ####
# pfctl -f /etc/pf.conf load /etc/pf.conf
# pfctl -n -f /etc/pf.conf parse /etc/pf.conf, but dont load it
# pfctl -R -f /etc/pf.conf load only the FILTER rules
# pfctl -N -f /etc/pf.conf load only the NAT rules
# pfctl -O -f /etc/pf.conf load only the OPTION rules

#### Clearing PF Rules & Counters ####
# pfctl -F all flush ALL
# pfctl -F rules flush only the RULES
# pfctl -F queue flush only queue’s
# pfctl -F nat flush only NAT
# pfctl -F info flush all stats that are not part of any rule.
# pfctl -z clear all counters
# note: flushing rules do not touch any existing stateful connections

#### Output PF Information ####
# pfctl -s rules show filter information
# pfctl -v -s rules show filter information for what FILTER rules hit..
# pfctl -vvsr show filter information as above and prepend rule numbers
# pfctl -v -s nat show NAT information, for which NAT rules hit..
# pfctl -s nat -i xl1 show NAT information for interface xl1
# pfctl -s queue show QUEUE information
# pfctl -s label show LABEL information
# pfctl -s state show contents of the STATE table
# pfctl -s info show statistics for state tables and packet normalization
# pfctl -s all show everything

#### Maintaining PF Tables ####
# pfctl -t addvhosts -T show show table addvhosts
# pfctl -vvsTables view global information about all tables
# pfctl -t addvhosts -T add 192.168.1.50 add entry to table addvhosts
# pfctl -t addvhosts -T add 192.168.1.0/16 add a network to table addvhosts
# pfctl -t addvhosts -T delete 192.168.1.0/16 delete nework from table addvhosts
# pfctl -t addvhosts -T flush remove all entries from table addvhosts
# pfctl -t addvhosts -T kill delete table addvhosts entirely
# pfctl -t addvhosts -T replace -f /etc/addvhosts reload table addvhosts on the fly
# pfctl -t addvhosts -T test 192.168.1.40 find ip address 192.168.1.40 in table addvhosts
# pfctl -T load -f /etc/pf.conf load a new table definition
# pfctl -t addvhosts -T show -v output stats for each ip address in table addvhosts
# pfctl -t addvhosts -T zero reset all counters for table addvhosts

###  To reset the states for one IP...
# pfctl -k x.x.x.x
# pfctl -k 0.0.0.0/0 -k x.x.x.x

### To reset all states
# pfctl -F state
```
