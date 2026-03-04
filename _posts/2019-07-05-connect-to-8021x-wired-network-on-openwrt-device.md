---
layout: post
title: "Connect to 802.1X wired network on OpenWRT device"
date: 2019-07-05 21:45:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/07/connect-to-8021x-wired-network-on.html
---

Source: OpenWRT Access point.   
Task: auth by  IEEE 802.1X on wired connection.  
  
 opkg update  
 opkg remove wpad-mini  
 opkg install wpad  

[![](/assets/images/blog/5d9a64963f018def-a39a7c54fa7fb7d3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipjo1rfGFMdTNxhL8WWR9n4KCZpVbqFEQdZunl5fxyGaM4XJJ42RA_gHTRQvuNOzUh9x_YIBucM41J84f_3IIk0wBuW55w91GIIjkj85aXAzhAQ7_ZQzBFQr3wNLsrHI7bjecGwSRpvs5Z/s1600/wrt-wpa-supplicant.PNG)

  
  
  
  
 wpasupplicant.conf:  

```
# The directory that will be created for UNIX domain sockets
ctrl_interface=/var/run/wpa_supplicant

# Access control for the control interface
ctrl_interface_group=0

# IEEE 802.1X/EAPOL version
eapol_version=2

# This mode must be used when using wired Ethernet drivers
ap_scan=0

network={
 key_mgmt=IEEE8021X
 eap=TTLS
 phase1=""
 phase2="auth=PAP"
 ca_cert="PathToYourCertificateFile"
 identity="YourUserName"
 password="YourPassword"
 eapol_flags=0
}
```

  
Certificate file must be in pem or der format. For other authentication methods and more information,  
Now we can run for testing!  
  
wpa\_supplicant -i eth0 -D wired -c PathToYourConfigFile -dd -t  
  
Startup wpasupplicant.init:  

```
#!/bin/sh /etc/rc.common

START=50
STOP=15

start() {
 sleep 20
 wpa_supplicant -i eth0 -D wired -c PathToYourConfigFile -dd -t -B
 sleep 5
}

stop() {
 killall wpa_supplicant
 sleep 2
}
```

  
Put this file in /etc/init.d directory, and enable it:  
/etc/init.d/wpasupplicant.init enable  
(Remember to chmod it before executing!)  
  
  
Reference:  
<https://windsketch.cc/8021x-wired-network-tp-link-tl-wr841nd/>  
<https://christiantietze.de/posts/2013/09/wpa-enterprise-wired-openwrt-wa901nd/>  
<https://weekly-geekly.github.io/articles/91015/index.html>
