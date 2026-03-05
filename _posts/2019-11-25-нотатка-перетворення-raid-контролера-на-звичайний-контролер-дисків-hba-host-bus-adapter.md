---
layout: post
title: "Нотатка. Перетворення RAID контролера на звичайний контролер дисків HBA (Host Bus Adapter)"
date: 2019-11-25 19:53:00 +0000
tags: ["firmware", "hardware", "LSI", "raid"]
blogger_orig_link: https://lexxai.blogspot.com/2019/11/raid-hba-host-bus-adapter.html
---

[![](/assets/images/blog/46abfe9b2ea97a0c-1c7d46eb21df43eb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8bvO92-W-BILf902obNUTO3t3zcUojIKmkYi2KdlH11Q1JzrTn24uFkvjMEd26An_ropu6eK9_YCZJ3R-SRzyeCIdk_W4UGuGhUVucYdSYmi3pOMa0FQhlWk-n5oZhgqN81fejGpFJXcO/s1600/sasuc8i.jpg)

Є RAID контролер на базі  [LSI MegaRAID SAS 9240](http://www.lsi.com/products/storagecomponents/Pages/MegaRAIDSAS9240-8i.aspx).  
[LSI MegaRAID SAS 9240-8i](http://www.lsi.com/products/storagecomponents/Pages/MegaRAIDSAS9240-8i.aspx) - 2x4 port internal SAS vertical, no cache, no BBU, RAID 0, 1, 10, 5 and 50, can be crossflashed to LSI9211 IT/IR це SAS2008 based controllers.  
SAS2008 - 8x SAS v2.0, 3890MB/s, PCIe v2.0, PCIe 4x slot 1716MB/s, PCIe 8x slot 3433MB/s max throughput  
  
Задача перевести контролер у так званий IT режим (Initiator Target), у котрому буде доступ до всіх дисків. Це необхідно для роботи [ZFS](https://uk.wikipedia.org/wiki/ZFS).  
  
  
*Є багато матеріалів, тому просто поки тут перелічую і матеріал не перевірений особисто,  бо ще процес зміни режиму не виконанний.*  
  
[Flashing LSI HBA to IT mode or IR Mode – Geeks Unlimted Inc](https://geeksunlimitedinc.wordpress.com/2017/02/16/flashing-lsi-hba-to-it-mode-or-ir-mode)   
  
[SAS2008 (LSI9240/9211) Firmware files - Projects, Tools, Utilities & Customized INFs - LaptopVideo2Go Forums](https://forums.laptopvideo2go.com/topic/29059-sas2008-lsi92409211-firmware-files)   
  
[Detailed newcomers' guide to crossflashing LSI 9211/9300/9311 HBA and variants | iXsystems Community](https://www.ixsystems.com/community/resources/detailed-newcomers-guide-to-crossflashing-lsi-9211-9300-9311-hba-and-variants.54)  
  
[LSI RAID Controller and HBA Complete Listing Plus OEM Models | ServeTheHome and ServeThe.Biz Forums](https://forums.servethehome.com/index.php?threads/lsi-raid-controller-and-hba-complete-listing-plus-oem-models.599/)  
  
  
  

```
mfiutil show adapter
mfi0 Adapter:
    Product Name: LSI MegaRAID SAS 9240-8i
   Serial Number: P52222XXXX
        Firmware: 20.13.1-0208
     RAID Levels: JBOD, RAID0, RAID1, RAID5, RAID10, RAID50
  Battery Backup: not present
           NVRAM: 32K
  Onboard Memory: 0M
  Minimum Stripe: 8K
  Maximum Stripe: 64K
```

  
  
p.s. зараз вибрана опція JBOD + hw.mfi.allow\_cam\_disk\_passthrough = 1 in loader.conf (FreeNAS)  

[![](/assets/images/blog/b76aa7cdbdb8fe26-93aa5094118cccbb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUrkGkKRQ8r_97dvGoTddVEEJvMm6ueGkZQVkfMMWvLwval-kt7rUiy3shqW0OSpDxyD6lxP7_JjelVuKYpfqGc_1a2C7gdYVZC176vJIX9sfLqs8jJEZlKJPyanVxwZ_bZ5Vs3hXLCKgB/s1600/freenas-it2.PNG)  
*Tune loader.confon FreeNAS*

[![](/assets/images/blog/35aefbe4be2373fe-7b7ab6ba551faaf9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMSRaBB-FZ-PAIIBfqAKKPpKKpYX8Q4zJhjOi7lBzooc8xQyQ4CB8D3pLdoZuyUtwcj_xLQFltyNIJa31SIIKZwWXpm6S7rxQ_MABRQJRVXjPf6qaXYFlYqpyAsn_jtwUmkAl6P5qawGmE/s1600/freenas-it.PNG)  
*da0-da7 result of disk\_passthrough =1*
