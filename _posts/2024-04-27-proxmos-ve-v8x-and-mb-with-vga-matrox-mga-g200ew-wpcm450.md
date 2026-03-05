---
layout: post
title: "Proxmos VE v.8.X and MB with VGA Matrox MGA G200eW WPCM450"
date: 2024-04-27 23:02:00 +0000
tags: ["console", "grub", "linux", "Proxmox"]
blogger_orig_link: https://lexxai.blogspot.com/2024/04/proxmos-8-and-mb-with-vga-matrox-mga.html
---

Зараз є проблема оновлення з 7 до 8 версій Proxmos у власників не нових
серверів з  VGA Matrox MGA G200eW WPCM450 -[є чорний екран](https://forum.proxmox.com/threads/black-screen-vga-on-proxmox-ve-8-1-installer-boot.137408/).

Собі щоб не забути зробив нотатки.

### Upgrade 7 to 8

Спочатку [етапи оновлення](https://pve.proxmox.com/wiki/Upgrade_from_7_to_8) але без перезавантаження.

```
lspci | grep VGA
08:01.0 VGA compatible controller: Matrox Electronics Systems Ltd. MGA G200eW WPCM450 (rev 0a)
```

Основні зміни в /etc/default/grub:

```
GRUB CMDLINE LINUX="nomodeset"
GRUB TERMINAL=console
GRUB GFXMODE=nomodeset
```

[![](/assets/images/blog/92a7e02e41474679-69d96effb36bf37e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVgY_jP2KaQNn12DiMr3m41-oW7LRQI9Jf6RkWC116-RoeifLQ52Hra9CnMD86ZziIt-EEqwWgZxPw3KXYR3kAjNbMDKRYn0nbQrjLHqwAcV4JQODansoz-TIQQ7OJL8NWwzrWq9uVoCc2qgaLsnu78syM7Z5RLKgXgDJmnK5YVVTPgKTwA7XCStBj5fr6/s948/Screenshot%202024-04-28%20043528.png)  
*/etc/default/grub*

```
grub-update
reboot
```

[![](/assets/images/blog/6fbb223ec2883d77-1dd14686ba64b5fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9RaCV4Ph8cCY1bX03Hc5JzRTjFGUcj-BbD4_TJj_JfsNpD_IBwPaTX9D7PwKu2F2pJoiFq5KMLLboTVYyEscVBB8u2ffxsLrCSLX_72oBVt4NQYMy6R9yd8FODGUUhslbKukyEt2FJ4om91U6IHCIzH8M0dGwYR2bPZt33BDjBuaI2Q91MWnrxk7EvpgW/s1381/Screenshot%202024-04-28%20020707.png)  
*PROXMOX VE 8.2.2*

### Install ISO

### 

[![](/assets/images/blog/5fea2c1089fa7fc7-9366c97a5cee7e9e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUL8KiC7rJn9HfXKijWmHV-pPebGpXUwefv59BkT0NDA6ImSCLx_YDZ9f8eGnGUUhDAn8c_qEdJ2jf9zQwTk59f2DoTKtnW_ajT9DeZ50bslRjRpQaRgg6KcshDs_1nVINx5I5p7_gq5WDgAM5a6yttkKou3p8_bCmLOQYkVG6iBXDDFER2CVAbMwbg9MD/s1440/Screenshot%202024-04-28%20185520.png)
