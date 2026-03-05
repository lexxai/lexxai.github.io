---
layout: post
title: "Нотатка для себе. Відновити GPT таблицю, випадково стерту в Proxmox з ZFS."
date: 2021-06-08 00:58:00 +0000
tags: ["fdisk", "gpt", "hdd", "Proxmox", "restore", "ZFS", "zpool"]
blogger_orig_link: https://lexxai.blogspot.com/2021/06/restore-gpt-faulted-deleted-in-proxmox.html
---

Proxmox. ZFS. HDD 3 TB.

Так сталося що випадково проініціалізував не той диск за допомогою команди fdisk: g   create a new empty GPT partition table.

Так я зрозумів що накоїв і диск ZFS працював зі старою partitional table доки не перезавантажиться система, тому почав створювати свіжі резервні копії, на мережевий диск ceph. І відновив віртуальні машини на іншому сервері.

Після копіювання, перезавантажив систему - диск пустий.

### Відновлення

Спроба запуску TestDisk для аналізу і відновлення, знайдено в режимі Intel ост таку таблицю, вона не правдива. Не підходить.

[![](/assets/images/blog/57814eaf67e98db2-20cca49293074d8c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHPXFVc7DOZorxMf2Ep0YRgcrft6rDasJfONTdaCVDXYd3_GGWl2XnsAb1XOVVkqNrceGAfUBfr1GbMVgdi6j83Tp0sap7BM29D4ntnTN44T-RKza59dIwL0wL_IZFte0nABbqA27GtQ_O/s576/ns230-zfs-rec1c.png)  
*TestDisk scan ZFS*

Тому так як є інший Proxmox сервер з ZFS диском на 3ТБ, я виконав перенесення структури з робочого диску на не робочий.

[![](/assets/images/blog/e2b277c22c5d8a8a-438cd26ed586bd97.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3d8YMX0sC1HO7LSIfsKrm0HEMj3RSxO-rTMa8d98oIKTOt385I2_Uf_h6x6OyT-C04rLAga815V9Ht-Tgbzrm9HURkforOyPditHIunC25cq63skc6JSZi1tCCGBgLuVEZ7UnyTMWmhTh/s726/fdisk-part.PNG)  
*fdisk, export structure data*

```
fdisk /dev/sdb, O,  3tblayout.txt, q
scp 3tblayout.txt root@proxmox2:/root
ssh root@proxmox2
# cp 3btayout.txt  l.txt
# ee l.txt replace /dev/sdb  to /dev/sdc, tune Disk Indetifier to E79
```

Файл - l.txt:

```
label: gpt
label-id: 33C05FF2-DCB2-704E-BAB4-04D662404E79
device: /dev/sdc
unit: sectors
first-lba: 34
last-lba: 5860533134

/dev/sdc1 : start=        2048, size=  5860513792, type=6A898CC3-1DD2-11B2-99A6-080020736631, uuid=F11C26EA-9A4C-814C-9C92-8B5FCAFE357B, name="zfs-fc14f1d000b43d09"
/dev/sdc9 : start=  5860515840, size=       16384, type=6A945A3B-1DD2-11B2-99A6-080020736631, uuid=66B86B57-858F-424C-8007-B9252743E033
```

```
# fdisk /dev/sdc, I, l.txt, w
```

[![](/assets/images/blog/27dfb8c6aa0deaf4-4d79f684294e4b4f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfwM6f_IQDgpxBZmy5ur8QPI67tGyyBR5kDcwnnJOaGYuqKo2RhyN6PX6ilExTvcVnd7_Wm3dW3h3MUu5hp8YE1DUqqXUZdVOf4ahCCUStTpzjDg9opQcA5nj2l37OMonOQ-KxVGv8A1yy/s714/fdisk-part2.PNG)  
*fdisk import stucture data*

Після цього ZFS pool автоматично відновився.

[![](/assets/images/blog/41e8574f9a0c7bba-476fc02d0e063961.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghR06tqBp0Z6FFuI5mGJy7nniUZtgX9eLzkTJradeEayq_hm5Nizex4aOXm9M7CvU9icqUVI6ziLb9Q2S2wpSAF6Kwq4p_MmkOlA9xo1LRsiUSKrJrOWZSdrSymrr4_q0cvD19eWLjqgz3/s755/ns230-zfs-rec3.png)  
*Scrub ZFS*
