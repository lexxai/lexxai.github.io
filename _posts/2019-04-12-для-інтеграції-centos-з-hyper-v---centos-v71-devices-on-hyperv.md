---
layout: post
title: "Для інтеграції CentOS з Hyper-V - CentOS v7.1 devices on HyperV"
date: 2019-04-12 15:45:00 +0000
tags: ["CentOS", "ESET", "ESET Remote Administrator", "Hyper-V", "hyperv-daemons"]
blogger_orig_link: https://lexxai.blogspot.com/2019/04/centos-hyper-v-centos-v71-devices-on.html
---

Використовую ESET Remote Administrator virtual appliance - Microsoft Hyper-V

Для інтеграції CentOS з Hyper-V додаю:

### 

### Install daemons

```
# yum install hyperv-daemons
# systemctl enable hypervfcopyd
# systemctl start hypervkvpd hypervvssd hypervfcopyd
```

  

### 

```
# virt-what
hyperv
```

  
За матеріалами:  
<https://plone.lucidsolutions.co.nz/linux/hyperv/centos-v7.1-devices-on-hyperv>
