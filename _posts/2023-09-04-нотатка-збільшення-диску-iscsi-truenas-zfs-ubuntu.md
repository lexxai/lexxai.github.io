---
layout: post
title: "Нотатка: збільшення диску iscsi, TrueNAS, zfs, ubuntu"
date: 2023-09-04 22:40:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/09/iscsi-truenas-zfs-ubuntu.html
---

Блочний пристрій сервер Ubuntu - sda = 500G

```
lsblk
sda           8:0    0   500G  0 disk
└─sda1        8:1    0   500G  0 part
```

Збільшую том іscsi до 600G (TrueNAS).

[![](/assets/images/blog/dbab9d15004771eb-e3dc5192e7fcadea.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYim7hpL49VZapZG0eXZkj5FgJM7-4TTSiqIgRnzJC6iX5ImccbLpIGEuhGA6tOJGlkJmU27wp15TRqUUX754p-REQ8AGYYrYCJlkWf-lSkHzYv6lYpmDyFrfCLXh-41GVRh3_B_LWgyINubp2-0j9NK98oUE5hkL6yA5wnWphZnCO7A4pN0szgOkTfd2D/s1607/Screenshot%202023-09-05%20012707.png)  
*Новий розмір тому для iscsi target*

Перезавантажив іscsi службу (TrueNAS).

Блочний пристрій сервер Ubuntu sda = 600G, розділ 1 500G

```
lsblk
sda           8:0    0   600G  0 disk
└─sda1        8:1    0   500G  0 part
```

Збільшую розділ 1 до на всі 100%

```
parted /dev/sda resizepart
Warning: Not all of the space available to /dev/sda appears to be used, you can fix the GPT to use all of the space (an extra 209715200 blocks) or continue with the current
setting?
Fix/Ignore? fix
Partition number? 1
End?  [537GB]? 100%
Information: You may need to update /etc/fstab.
```

```
lsblk
sda           8:0    0   600G  0 disk
└─sda1        8:1    0   600G  0 part
```

zfs list - вже має новий розмір
якщо ні то:
zfs online

```
df -h  /mnt/media
Filesystem      Size  Used Avail Use% Mounted on
mdatai/media    580G  443G  138G  77% /mnt/media
```
