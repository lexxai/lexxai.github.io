---
layout: post
title: "Нотатка: VNC Startup Ubuntu DISPLAY:0"
date: 2019-11-19 01:04:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/11/vnc-startup-ubuntu-display0.html
---

[![](/assets/images/blog/d9f501c9fc25f2c6-bf39bc6b512835d7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7q_1_bJLTuOnU-ObKWY7uQnwXz-CsnVk9_vghpZoITMyZ2w0mrNkzncToX35856H5quD0E0j_auUxRTjX5m2Y6Hjux0mI7ejnZuZdi19y7x2N-5SEhMFaTN6ppfsb-4489p3goPrUenT2/s1600/Virtual_Network_Computing_%2528logo%2529.svg.png)

  
Задача запустити [VNC](https://uk.wikipedia.org/wiki/Virtual_Network_Computing) як аналог консольного дисплея з DISPLAY:0.  
Є скрипт.  

```
# ##################################################################
# Script Name : vnc-startup.sh
# Description : Perform an automated install of X11Vnc
#               Configure it to run at startup of the machine
# Date : Feb 2016
# Written by : Griffon
# Web Site :http://www.c-nergy.be - http://www.c-nergy.be/blog
# Version : 1.0
#
# Disclaimer : Script provided AS IS. Use it at your own risk....
#
# #################################################################

# Step 1 - Install X11VNC
# #################################################################
sudo apt-get install x11vnc -y
# Step 2 - Specify Password to be used for VNC Connection
# #################################################################
#sudo x11vnc -storepasswd /etc/x11vnc.pass


# Step 3 - Create the Service Unit File
# #################################################################

cat > /lib/systemd/system/x11vnc.service << EOF
[Unit]
Description=Start x11vnc at startup.
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/x11vnc -auth guess -forever -loop -noxdamage -repeat -rfbauth /etc/x11vnc.pass -rfbport 5900 -shared -localhost -o /var/lo
g/x11vnc.log

[Install]
WantedBy=multi-user.target
EOF

# Step 4 -Configure the Service
# ################################################################

echo "Configure Services"
sudo systemctl enable x11vnc.service
sudo systemctl daemon-reload

sleep  5s
sudo shutdown -r now
```

Додаємо,
first replace GDM with LightDM, because GDM does not start on the same Display as the user session, then disable Wayland, see /etc/gdm3/custom.conf
  

```
sudo apt install slick-greeter
```

  
За матеріалами:
  

* <https://serverfault.com/questions/848078/x11vnc-on-ubuntu-16-04-gnome-with-systemd>
* [https://lexxai.blogspot.com/2016/06/ubuntu-1604-x11vnc-startup.html](https://lexxai.github.io/2016/06/ubuntu-1604-x11vnc-startup.html)
