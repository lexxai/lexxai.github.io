---
layout: post
title: "Глюк з FreeBSD 12, та мережевих карт Intel (em)."
date: 2019-02-26 19:50:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/02/freebsd-12-intel-em.html
---

Після оновлення FreeBSD власники мережевих карт Intel (em) можуть не побачити свої мережеві інтерфейси після перезавантаження.  
  

```
dmesg
em0: <Intel(R) PRO/1000 Network Connection> port 0xc000-0xc01f mem 0xfe800000-0xfe81ffff,0xfe820000-0xfe823fff irq 46 at device 0.0 on pci3
em0: attach_pre capping queues at 1
em0: using 1024 tx descriptors and 1024 rx descriptors
em0: msix_init qsets capped at 1
em0: pxm cpus: 8 queue msgs: 0 admincnt: 1
em0: using 0 rx queues 0 tx queues
em0: Using MSIX interrupts with 1 vectors
em0: allocated for 0 tx_queues
em0: allocated for 0 rx_queues
em0: failed to allocate IRQ for rid 0, name irq0.
em0: iflib_legacy_setup failed 12
device_attach: em0 attach returned 12
```

  
Поки виправляється цей баг, то можна застосувати це:  
*Adding hw.pci.enable\_msix=0 to /boot/loader.conf and rebooting fixed it for me.
<https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=235147#c8>*  
  
Мені це допомогло.
