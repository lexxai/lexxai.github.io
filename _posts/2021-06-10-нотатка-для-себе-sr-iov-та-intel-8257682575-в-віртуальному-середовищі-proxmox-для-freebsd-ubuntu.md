---
layout: post
title: "Нотатка для себе. SR-IOV та Intel 82576/82575 в віртуальному середовищі Proxmox для FreeBSD, Ubuntu."
date: 2021-06-10 20:43:00 +0000
tags: ["FreeBSD", "Intel", "Proxmox", "SR-IOV", "ubuntu", "Virtualization"]
blogger_orig_link: https://lexxai.blogspot.com/2021/06/sr-iov-intel-8257682575-proxmox-freebsd.html
---

В Proxmox було встановлено мережеву карту 1Gb Intel 82576 з метою використання
функціоналу SR-IOV для ефективного використання для віртуальних мереж.

[![](/assets/images/blog/e3f6b57136e37842-b7dc254b66bcb718.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipyUIuqAFiiC5RgmY0eibLMeARj0V63emKDz02UZ7ugdJkPYugoYecQ4NqN49HeMzYOi-X6anw7X0PsiOc_9Q32wZhi4-uqaBwOXEep6XLhyphenhyphenBSaOZj8tJ8p2wn46QX5Cjzss3QaScmOiXG/s898/single_port_nic.png)  
*SR-IOV*

[![](/assets/images/blog/c787ee7888092aa3-fb6e4680401d3de2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAscP5kZj3BlShQCwg1kqUhHwQsO8y10HSi8kwhczNopNHQAkbBkK05Gq8twLek1MwmQwa1WDvuzzrDHM1h2VTNQFUhN1N3yCiJeUcYvaNRUdRiC2gpLqfvkE6ybMU-M-nC8bH35Ght_AK/s1069/SR-IOV_implementation.png)  
*SR-IOV*

  
Proxmox була налаштована для
[PCI(e) Passthrough , IOMMU](https://pve.proxmox.com/wiki/PCI(e)_Passthrough)  до VM.

### FreeBSD VM

[![](/assets/images/blog/1b8935798f8e988f-2a16f27f4e9108aa.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgx13PXWhmINyidIaOJIlU4eKqXr_UOQ5zXfsuudLaMHqNpUBY1eKLf0HjiPGUevcDKkjTKtIC9oQNhvrN95BG4cf0wFn7N4lXdjOrj-IKl2ekb8jbW57LRwePjeISYJrhFZoRAeghrf5g5/s939/pr82576-01.PNG)  
*VM-FreeBSD13*

  

[![](/assets/images/blog/25fee55a04188a65-372d649d6d5bb99a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzl23fI2MsPXHOQO5qZi7OWzyKVYmojvIrzrd13QZR75rAel4J3yz3JlxpmLCwdIXHM1AsiINQ7CPDnVhrsHAjhFOsysXPJDt7oFEk-BFqmd5Fu5GX2H6oPx6tktYuesPGwIsmc7EwrhgP/s722/pr82576-01a.PNG)  
*VM-FreeBSD13 pciconf*

FreeBSD не може проініціювати 82576 Virtual Adapter:  

[![](/assets/images/blog/11ae614fd34eea91-f9d3e9cac7e74df7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiH83dpCg3D2GmeMEPJ1pnW990HauyJE1i30nmv0CCzCG01aFBvDaQEJrMGvCyIUbg3L4ztvcy35iL5EkcWWdqqwaGzr57vwupeWzgYzu8JBUNryF8SO3uv2mEUJu5ZTOmS0U70IcMYyF0Q/s728/pr82576-02.PNG)  
*VM-FreeBSD13 dmsg , igb0*

Назначаємо mac адресу.

```
  ip link set enp8s0f0 vf 0  mac 40:1b:21:36:6b:8b
```

[![](/assets/images/blog/3d048777c7bd4eff-d6d44ec970f58634.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjP42OetgOGZ6KXBtnPRd2jHJd5wyUrno-XxmFqwXRTCyM8X41qhWXUO1rWWDO9D1lkz11vpjO59Ez2jijPfM_eiJe3PbXTHDmODld1APMFRE3UqJM-ENMiSIaSqIKN61RknU1U6KHj3YY4/s791/pr82576-m01.PNG)  
*mac list for VF*

[![](/assets/images/blog/518dedc3fa1eaabe-1639c1c0ab43a5fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6abTkuDDeaVaWa0nOeiUzHnrrOCgA1s_5BBsJ5poeug-9nqE2Aq_dRVJUEPq2OEwRJ_8TiMEYbLCvMHP5FmeZ7Oawn-C8aqv2ewuG6BtkV-tXoIVafgzOYbWiHSj7KVsKoQsUSnTYnqlD/s779/pr82576-m02.PNG)  
*new MAC for VF 0*

І тепер FreeBSD VM працює.

[![](/assets/images/blog/20cf5eca0bcf6e9b-dcdf460fab7cdf1a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMSyuPNbeGtvQ7pyPyDHlMUXRedYhSK6HfeAzxmWr9euE2MQj1MwNVGUqaziNX7baEjMwbMZunywkOyCd0CgYGgUg1WtopmpD0Sn_XxC-V3h5MAj2ChddAjYhx9mQjb0bvyUeuX3-L4U0T/s724/pr82576-m03.PNG)  
*FreeBSD13 SRV-IO Virtual Function Ethernet VF 0*

### Ubuntu VM

[![](/assets/images/blog/ec980b326540b3bd-9f8fba56916191d9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEBMgBy4XYBAfRhL2bNQWSAzkBwJgeL3jGyaZbuXrC_oqfA1byWImIFV0wz0LfK4z2ir2Gw9YUBCp6YPOgZQP-L91d-WufNzg2BSk-Y0il_P-YsX1KbHMheNRDnthcGiS27mtlZgz_Kzvw/s925/pr82576-03.PNG)  
*VM-Ubuntu*

  

[![](/assets/images/blog/2584ea310eb6e17c-595b310d06eda841.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiu_X4OoUdE8evYNWIhpJV8YG8ZSFWXrCbQF2VudaKViHas-ltHOpJcOxQiA3ogPwgiHWW_woiRCbhExvXtU4eQO-Ua52RdyZpGXbvfiOHx_dKBy3BQfT94qjzP3-KHI55tzwVPyyADkZ1p/s965/pr82576-04.PNG)  
*VM-Ubuntu igbvf*

За тих самих налаштувань для VM в Ubuntu 82576 Virtual Adapter  успішно
отримав MAC адресу і назначив IP адресу.

### 10GB FreeBSD VM

Тестування
[10Gb адаптера Intel X710](/2021-05-25-нотатка-для-себе-sr-iov-vlan-linux-proxmox.md), у VM FreeBSD13 успішно ввімкнулася без маніпуляцій з mac:

### 

```
#dmesg | grep  iavf
iavf0: <Intel(R) Ethernet Virtual Function 700 Series> mem 0xfe400000-0xfe40ffff,0xfe414000-0xfe417fff at device 16.0 on pci0  
iavf0: PF API 1.1 / VF API 1.1  
iavf0: VSIs 1, QPs 4, MSI-X 5, RSS sizes: key 52  lut 64  
iavf0: Using 1024 TX descriptors and 1024 RX descriptors  
iavf0: Using 1 RX queues 1 TX queues  
iavf0: Using MSI-X interrupts with 2 vectors  
iavf0: Ethernet address: XX:XX:XX:XX:XX:XX  
iavf0: netmap queues/slots: TX 1/1024, RX 1/1024
```

```
#ifconfig iavf0  
iavf0: flags=8822<BROADCAST,SIMPLEX,MULTICAST>  metric 0 mtu 1500  
     options=4e507bb<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,JUMBO_MTU,VLAN_HWCSUM,TSO4,TSO6,LRO,VLAN_HWFILTER,VLAN_HWTSO,RXCSUM_IPV6,TXCSUM_IPV6,NOMAP>  
     ether XX:XX:XX:XX:XX:XX      
     media: Ethernet  autoselect  
     status: no carrier  
     nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
```

....

More links:

* [[Tutorial] Enabling SR-IOV for Intel NIC (X550-T2) on Proxmox 6 : Proxmox](https://www.reddit.com/r/Proxmox/comments/cm81tc/tutorial_enabling_sriov_for_intel_nic_x550t2_on/)
