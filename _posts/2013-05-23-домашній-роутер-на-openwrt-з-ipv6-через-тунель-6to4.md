---
layout: post
title: "Домашній роутер на OpenWRT, з IPv6 через тунель 6to4"
date: 2013-05-23 20:51:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/05/openwrt-ipv6-6to4.html
---

![](/assets/images/blog/b56059e6b0f205ff-3776724c90b53b7b.png)

 Вивчаючи IPv6, я спробував різні варіанти :  

* реальний IPv6 що було наданно моїм провайдером, тимчасово.
* через тунельного брокера (Hurricane Electric) [he.net](http://www.he.net/)
* через тунель 6to4, так як я маю постійну IPv4 адресу.

Для вивчення пройшов сертифікацію [http://ipv6.he.net/certification,](http://ipv6.he.net/certification/scoresheet.php?pass_name=lexxai)  

[![](/assets/images/blog/1c59a442a39c6853-2d7acc8d9da89b7d.php)](http://ipv6.he.net/certification/create_badge.php?pass_name=lexxai&badge=2)

Для проходження сертифікації та отримання практичних знань, налаштував у віртуальному середовищі сервер FreeBSD 9.1, де працював чистий IPv6.  
Для використання IPv6 у домашній мережі, вирішив налатувати свій домашній роутер на основі прошивки "[OpenWrt Attitude Adjustment 12.09](https://openwrt.org/)".  
До встановив необхідні пакунки, думаю що були ці :  

```
6to4,radvd,kmod-ip6tables,kmod-ipv6,kmod-iptunnel4,kmod-iptunnel6,
iputils-traceroute6,luci-app-radvd,luci-proto-6x4
```

  
І ось скріншоти налаштувань у LuCI.  
  

[![](/assets/images/blog/951ce4a24fe6c206-921f4abaf5947ab9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDFz9bzJtIFvJ69L0Tsy1J9BNU4F5VzCM0jl4Texlhu0ghdytmsNJzROuTmcd7OTIB1LTjwf5GusPAhKy_6OA6bDRjfBt9ASwhDoB_O5nIuBT_oSBmfHl7G5EiIIHmRxefapmanFyM9ABb/s1600/openwrt-ipv6-6to4-01.png)

  

[![](/assets/images/blog/eb3668320777c5dd-0e0d4efa2fe7d950.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjanXl-yKfPOzffiaJqbsvOegrDu1vrrqorlc8K2dD6madC5n2QpYyV9BUtFH7jmL-hOjQtcH_GduSjkV0g-3lWb1Hq6QIoNufTn5LVDJmJ4TIPOUWbORwMUeLBEKnRU36HThqYin27Vze2/s1600/openwrt-ipv6-6to4-02.png)

  

[![](/assets/images/blog/14357a20ccbb4f31-24f8edb15c762dcd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlV7vv9qcytgDCt-bL2GC8y4gD8BE6tCnWsisPdCb_OvFNjHX8iy8SHZn5sVatpwmvzDCcKdLwWP1HNMxjM56mUR6UG4dZPrpNOJktLTw2FtP6pUij4TLKmJhuhmyJuX2PceMXzRPRcIIJ/s1600/openwrt-ipv6-6to4-03.png)

  

[![](/assets/images/blog/07cf150e9ceca86c-3f4a2d34f4227834.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_E6PePrA96NMVqMxt2KmPJdq5pMZMun4Uk_-yz9LS6kakStr2UOCPlIeEyspvpvA8fqCgvUoZZEkg31LYkVKiPWxs9ENaEftaGGXDQYCAm8S6XUD13O3on8X8CrsjhQt8aMz9dPfup5jd/s1600/openwrt-ipv6-6to4-04.png)

  

[![](/assets/images/blog/096e36a39503d3d7-13673692f5b154ac.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEir1o92XkLqPt3yENv3GWy4f85R-h5jSYWFRVO0AzvkCI9gqgFgqA3eIggWINQj4zXfqGmhvcYxL3eFAliet0uRvwyhq0cPXKC0Jg_fptPDUcCMBVP7DPIfc_tS_acvcrvSIxGsyA0MV7ix/s1600/openwrt-ipv6-6to4-05.png)

  

[![](/assets/images/blog/645fe78320cf18f9-584e888b6902a695.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1kkUKpKthBTAVS0JK4eMUxCNxoFLXBQWOrlS3ThL8UwAldxIaMyDUe6oV8VA3wgFXQA-gL_A_-WyFFOouHSM_0PZ4nOV2MkCuku-523ymyVOLOgNAwmbJjfduYumqfVMAracXAB0IZ_Qr/s1600/openwrt-ipv6-6to4-06.png)

  

[![](/assets/images/blog/27081270a4b1a065-b650f26b85898939.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhagjCjVhrMlkjJzW2driljmZBQUSVDhF_G0s5_P6O5te2lKr3Tl2PseG4IdBRfaG1JZSXZNsySNJo-h5_zrBlFQE1W6Bzgf5qQYhC8RU__OqmXDz457PJOtOomcc_yZVYoLDPnALpwYb3g/s1600/openwrt-ipv6-6to4-07.png)

  

[![](/assets/images/blog/ef1f79c4a3dfd2b6-a219a11bad559139.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuM5O1mL0NzcelArcV5O5Y08aqJJUhcUsS4co4RiVRVE9c3uZ_UzG2FRsoe3abY4swZKpkv95jB51mpBZ1rvjyR9HQJlRGyPqei64uYknij6S_XXOyO7mZMX2cjmvCmAYKusCx5IaKuLji/s1600/openwrt-ipv6-6to4-08.png)

  

[![](/assets/images/blog/86ed239e75fd9992-d1cbd3c7524bcfca.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXTPKoJuU1opiYTSQQgi5BEdP0dC68U8n6YyOjOjWOBx_em4kAZ4K3s3LviR-9APHQT1KCsRP1kkuNxoYzPOUsNqG6_wf2GeHh8-qbQ0sSpOLws6BW1Ls8fQipy8Xcbaex-Skj0Khvb_mz/s1600/openwrt-ipv6-6to4-09.png)

  

[![](/assets/images/blog/1b7d292bd8f78f94-b06b2fa0c3c85c6a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgvSXcHbdgohyphenhyphenQZ0Lw3tBKpoqaECJyah7UpzFI3_-SyDFe6heJkxxYR4mKPs36riQlZf6838TURosUWMlxJb3E81RAaAkjm0BQD8rFNOYLwYkRJQZncCD_sSdVkWcZXsPk4U2mVu23MXD-h/s1600/openwrt-ipv6-6to4-10.png)

  
/etc/config/network:  

```
config interface '6to4'
        option proto '6to4'
        option adv_interface 'lan'
```

/etc/config/6tunnel:  

```
config 6tunnel
        option tnlifname        'sixbone'
        # provide your tunnel providers' ipv4 address
        option remoteip4        ''
        # provide your public ipv4 address
        option localip4         ''
        # provide your local ipv6 address including prefix,
        # eg. 2001:610::2/64
        option localip6         ''
        # provide your tunnel providers' ipv6 address excluding
        # prefix, eg. 2001:610::1
        option remoteip6        ''
        # this should provide an mtu for your tunnel interface
        option mtu              '1280'
        # this should provide a ttl for your tunnel interface
        option ttl              '64'
        # provide your tunnel provider's delegated block (/64 or /48)
        option delegatedip6     ''
```

/etc/config/firewall:

```
config rule
        option target 'ACCEPT'
        option src 'wan'
        option family 'ipv4'
        option proto 'ipv6'
        option name 'Accept tunneling ipv6'
config zone
        option name 'wan6'
        option output 'ACCEPT'
        option network '6to4'
        option input 'REJECT'
        option forward 'REJECT'
config rule
        option target 'ACCEPT'
        option name 'Allow forward IPv6'
        option family 'ipv6'
        option proto 'all'
        option src 'lan'
        option dest 'wan6'
config forwarding
        option dest 'wan'
        option src 'lan'
config forwarding
        option dest 'wan6'
        option src 'lan'
```

  
Інші посилання на цю тему:  

* [http://wiki.rnet.ru: Настройка ipv6 на openwrt](http://wiki.rnet.ru/index.php/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0_ipv6_%D0%BD%D0%B0_openwrt)
* [OpenWrt Wiki » Documentation » The UCI System » IPv6 configuration reference](http://wiki.openwrt.org/doc/uci/network6)
* [Thomas Elsen Security Blog, Enable IPv6 on OpenWRT](http://www.rivy.org/2012/09/enable-ipv6-on-openwrt/)
* [SysAdminMan Blog, IPv6 tunnel on OpenWRT using tunnelbroker.net](http://sysadminman.net/blog/2012/ipv6-tunnel-on-openwrt-using-tunnelbroker-net-3480)
* [SysteMajik Consulting, Implementing IPv6 6to4 on OpenWRT](http://www.systemajik.com/blog/implementing-ipv6-6to4-on-openwrt/)
* [IPv6 (tutorial) - DD-WRT Wiki](http://www.dd-wrt.com/wiki/index.php/IPv6_%28tutorial%29)
