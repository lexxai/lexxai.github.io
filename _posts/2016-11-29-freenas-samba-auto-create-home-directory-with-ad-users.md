---
layout: post
title: "FreeNAS SAMBA auto create home directory with AD users"
date: 2016-11-29 01:38:00 +0000
tags: ["FreeNAS", "samba"]
blogger_orig_link: https://lexxai.blogspot.com/2016/11/freenas-samba-auto-create-home.html
---

### FreeNAS-9.10.1-U4 (ec9a7d3)

### Samba (SMB)

Проблема в тому що при визначенні домашньої теки, вона не створювалася там де треба, а в /home/%D/%U.  
І користувачі не могли попасти до теки, тому що самба давала вірний шлях, але тек там не було.  
Пере визначення template dir допомогло.  

[![](/assets/images/blog/38838ebe40f9699a-263cf453cb5d5c89.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj88rC6Z7QQbNSknEZ-oG4_wUwpC-toFtcbBd46s-bEks9IRCw7Wi7Nl5UxWlV6XHBE3rqnc2ZgHidF6K1sndH7gnvHNcPdaAv4E7nMc41E0GnHmS3gsup6aDm8F4WlUQxDmDG01vA3h2a8/s1600/smbh-02.PNG)  
*Властивості домашньої теки*

  

[![](/assets/images/blog/5d19f9379e20649f-0fd6930493e1c75e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRvzJPNkHxgH6VyB8Tefcx8ZfVlq6X9y-nlPwu3WVco1rstk7pVU3APF8T05rnEIYeodcakZ5CkmlRqGuMwCLM3i2_85gejLj6bFi0jd_N2HxirnsDrbyTw8uRdo_MoUlMpYhefdYSF_aZ/s1600/smbh-01.PNG)  
*Пере визначаю template dir*

  

[![](/assets/images/blog/19da5f4cbb363214-4ca322df1baac37d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghV8ngx9PUNo2V46jGqfwJ13agawmI5GEHOVzs4bjuHy4RgnIriuHoU-ezsqdZNGnyeLub6lZcW8k3vK95hG_YC22PtVwTm0jhmwFJz2IXPVqEabVoXHB7p8ai5T3EhQAkrY5GxhyphenhyphenClNo9/s1600/smbh-03.PNG)  
*Ось так виглядає домашня тека*
