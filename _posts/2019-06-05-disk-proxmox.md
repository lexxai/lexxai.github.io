---
layout: post
title: "Disk & Proxmox"
date: 2019-06-05 21:04:00 +0000
tags: ["convert", "disk", "Hyper-V", "Proxmox", "VirtualBox", "Virtualization"]
blogger_orig_link: https://lexxai.blogspot.com/2019/06/disk-proxmox.html
---

## Роблю для себе нотатки: Proxmox та робота з накопичувачами.

## [Physical disk to kvm](https://pve.proxmox.com/wiki/Physical_disk_to_kvm)

```
lshw -class disk -class storage
...

           *-disk
                description: ATA Disk
                product: ST3000DM001-1CH1
                vendor: Seagate
                physical id: 0.0.0
                bus info: scsi@3:0.0.0
                logical name: /dev/sda
                version: CC27
                serial: Z1F41BLC
                size: 2794GiB (3TB)
                configuration: ansiversion=5 sectorsize=4096
...
```

```
ls -l /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC
ls -l /dev/disk/by-id | grep Z1F41BLC
```

add to kvm :  

```
qm set  592  -virtio2 /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC
update VM 592: -virtio2 /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC
```

check:
  

```
grep Z1F41BLC /etc/pve/qemu-server/592.conf
virtio2: /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC,size=2930266584K
```

#### 

#### 

#### [How to add an existing virtual disk to Proxmox](https://dae.me/blog/2340/how-to-add-an-existing-virtual-disk-to-proxmox/)

# 

The first step is to place the disk image into the directory belonging to the virtual machine, on my system that would be /var/lib/vz/images/<id>/

Obviously the disk image should be in one of the formats supported by Proxmox, such as qcow2 or raw. Use [qemu-img convert](https://docs.openstack.org/image-guide/convert-images.html) to convert between formats.

Next, go to Proxmox and check if the disk shows up under “Hardware” as an unused disk:

[![](/assets/images/blog/2a8159f1a4085b12-f653c070bd93add5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidO0P90bbIHbSgN_CuuRtVuCI6We4xxjV6yEnfy3srGqt2XuFfSjRsZE8q_BFyG7mdDX5ZS2Jeb-9QidydPh-d5wg3DKLsI9ilI-NuXDO7U7eYW8MptnTGOdWM4JXFU07if83dMnLNGc5a/s1600/proxmox-unused-virtual-disk-476x282.png)

#### [Converting between image formats](https://docs.openstack.org/image-guide/convert-images.html)

### qemu-img convert: raw, qcow2, qed, vdi, vmdk, vhd

qemu-img convert -f raw -O qcow2 image.img image.qcow2

qemu-img convert -f vmdk -O raw image.vmdk image.img

### VBoxManage: VDI (VirtualBox) to raw

VBoxManage clonehd ~/VirtualBox\ VMs/image.vdi image.img --format raw

#### 

#### Import existing disk image to VM

## 

```
$zfs list
NAME                  USED  AVAIL  REFER  MOUNTPOINT
zpool                43.4G  2.59T    96K  /zpool
zpool/images         1.13G  2.59T  1.13G  /zpool/images

$qm importdisk 100  /zpool/images/FreeBSD-12.0-RELEASE-amd64.qcow2  zpool

$zfs list
NAME                  USED  AVAIL  REFER  MOUNTPOINT
zpool                43.4G  2.59T    96K  /zpool
zpool/images         1.13G  2.59T  1.13G  /zpool/images
zpool/vm-100-disk-0  10.3G  2.60T    56K  -
zpool/vm-100-disk-1  32.0G  2.62T  1.81G  -
```

[![](/assets/images/blog/b7fb44b110f57c4b-06a84a7c3807d1e3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_GL9E2MeW0_jUBq7zzJGFjLg4Gigg_Z2ESn84c7S11EOP7kuk2taTUSwF6dh0rzQuJNEnRzX2b1c2pMdGk6A1aEhlE4ZUSmGFpzOiRbbdzIkz_bFWXR6C4JyRuOErTJGIkUYm40ZQYFYz/s1600/proxmox-unused-virtual-disk-1.png)  
*Unused Disk 0*
