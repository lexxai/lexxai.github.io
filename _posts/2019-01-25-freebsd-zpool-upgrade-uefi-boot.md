---
layout: post
title: "FreeBSD, zpool upgrade, UEFI boot"
date: 2019-01-25 14:08:00 +0000
tags: ["FreeBSD", "UEFI boot", "zpool upgrade"]
blogger_orig_link: https://lexxai.blogspot.com/2019/01/freebsd-zpool-upgrade-uefi-boot.html
---

Використовую [FreeBSD](https://uk.wikipedia.org/wiki/FreeBSD) 12.0, завантаження через [UEFI](https://uk.wikipedia.org/wiki/UEFI) , віртуальна машина [Hyper-V](https://en.wikipedia.org/wiki/Hyper-V).  
Після оновлення системи  файлова [ZFS](https://uk.wikipedia.org/wiki/ZFS) попередила (zpool status) про те що є нові функції і можна оновити їх. Що я і зробив.  

[![](/assets/images/blog/67464f4114b1a765-150ca0af957c3c33.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8s8badA5Q3bTyuzAtpY8gJd0fzPSDRKkGlR5hf0LLIyxYVydh0fR7DVvS38uGevT9LWnqvJg7wcNvKcqh_KyFFAK5Hs6mWJ9IsN0pyz2T5TYFXo_XvC72L9tZ1PYb0V5JiqxTz_5gEPpq/s1600/zroot-update.PNG)  
*zpool status*

```
zpool upgrade zroot 
This system supports ZFS pool feature flags.

Enabled the following features on 'zroot':
  large_dnode
  device_removal
  obsolete_counts
  zpool_checkpoint
  spacemap_v2

If you boot from pool 'zroot', don't forget to update boot code.
Assuming you use GPT partitioning and da0 is your boot disk
the following command will do it:

gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 da0
```

Але я маю завантаження через UEFI і треба оновити за '[handbook/zfs-zpool](https://www.freebsd.org/doc/handbook/zfs-zpool.html)': boot1.efifat.  

```
For systems using EFI to boot, execute the following command:
gpart bootcode -p /boot/boot1.efifat -i 1 da0
```

Краще якби  'zpool upgrade' показувало нагадування і про "boot1.efifat" а не тільки про "gptzfsboot".  
  
Так я забув про UEFI і система не завантажилася після update boot code gptzfsboot.  
  
Але так я використовував знімки віртуальної системи Hyper-V, я відновив попередній стан   
FreeBSD і виконав тільки оновлення для  boot1.efifat:   

```
gpart bootcode -p /boot/boot1.efifat -i 1 da0
```

  
Зараз система завантажилася нормально.
