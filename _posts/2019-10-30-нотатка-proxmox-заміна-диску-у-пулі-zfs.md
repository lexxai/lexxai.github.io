---
layout: post
title: "Нотатка: Proxmox заміна диску у пулі ZFS"
date: 2019-10-30 00:34:00 +0000
tags: ["gpt", "hdd", "Proxmox", "ZFS"]
blogger_orig_link: https://lexxai.blogspot.com/2019/10/proxmox-zfs.html
---

Використвую Proxmox VE  
Пул  ZFS  Mirror вийшов з ладу один з дисків, як замінити ?  
  
1) Replace the physical failed/offline drive, /dev/sdc  
  
Initialize Disk  
2) From the WebUI, Servername -> Disks -> Initialize Disk with GPT (/dev/sdc)  
or fdisk /dev/sdc, -g, -w.  
  
Copy the partition table from /dev/sdb to /dev/sdc  
3) sgdisk --replicate=/dev/sdc /dev/sdb  
  
Ensure the GUIDs are randomized  
4) sgdisk --randomize-guids /dev/sdc  
  
Then replace the disk in the ZFS pool,  
5) zpool replace rpool /dev/sdc1  
  
За матеріалами:  
<https://forum.proxmox.com/threads/zfs-disk-replacement.41230/>
