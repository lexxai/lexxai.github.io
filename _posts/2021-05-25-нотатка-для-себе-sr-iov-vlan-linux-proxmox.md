---
layout: post
title: "Нотатка для себе. SR-IOV VLAN Linux Proxmox"
date: 2021-05-25 00:10:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/05/sr-iov-vlan-linux-proxmox.html
---

Для використання в віртуальних машинах у Proxmox віртуальних мережевих
інтерфейсів.

Пристрої SR-IOV можуть спільно використовувати один фізичний порт з
кількома віртуальними машинами.

Віртуальні функції мають майже рідну продуктивність і забезпечують
кращу продуктивність, ніж паравіртуалізовані драйвери та емуляційний
доступ. Віртуальні функції забезпечують захист даних між віртуальними
машинами на тому ж фізичному сервері, коли дані управляються та
контролюються апаратним забезпеченням.

[![](/assets/images/blog/375bbb8027de17d9-fb6e4680401d3de2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgww7Mb7MDt1o5Z7FT8vODHfgzz6yliiaR2KxWX6Gx3NWwAjj503bck-4jGHvmvNJWlQFVTZTDvQKp_tHOA61Oe6f7RTf0lmDMHUOQA5dEcY-glq0OO8k9kstgGyvhPqAzwiTdYEBdQuIJp/s1069/SR-IOV_implementation.png)  
*How SR-IOV works*

[16.2. PCI Device Assignment with SR-IOV Devices Red Hat Enterprise Linux
7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-pci_devices-pci_passthrough)

### Advantages of SR-IOV

SR-IOV devices can share a single physical port with multiple virtual
machines.  
When an SR-IOV VF is assigned to a virtual machine, it can be
configured to (transparently to the virtual machine) place all network traffic
leaving the VF onto a particular VLAN. The virtual machine cannot detect that
its traffic is being tagged for a VLAN, and will be unable to change or
eliminate this tagging.  
Virtual Functions have near-native performance
and provide better performance than paravirtualized drivers and emulated
access. Virtual Functions provide data protection between virtual machines on
the same physical server as the data is managed and controlled by the
hardware.  
These features allow for increased virtual machine density on
hosts within a data center.  
SR-IOV is better able to utilize the
bandwidth of devices with multiple guests.  

### Search SR-IOV devices

```
# lspci | grep Ethernet
  03:00.0 Ethernet controller: Intel Corporation 82583V Gigabit Network Connection
  07:00.0 Ethernet controller: Intel Corporation 82576 Gigabit Network Connection (rev 01)
  07:00.1 Ethernet controller: Intel Corporation 82576 Gigabit Network Connection (rev 01)
```

Ethernet controller: Intel Corporation 82576 - має SR-IOV feature.

### Activate Virtual Functions

Run the following command:

```
# echo ${num_vfs} > /sys/class/net/enp3s0f0/device/sriov_numvfs
```

### Make the Virtual Functions persistent

To make the Virtual Functions persistent across reboots, use the editor of
your choice to create an udev rule similar to the following, where you specify
the intended number of VFs (in this example, 8), up to the limit supported by
the network interface card. In the following example, replace enp3s0f0 with
the PF network device name(s) and adjust the value of ENV{ID\_NET\_DRIVER} to
match the driver in use:

```
# vim /etc/udev/rules.d/enp3s0f0.rules
ACTION=="add", SUBSYSTEM=="net", ENV{ID_NET_DRIVER}=="i40e", ATTR{device/sriov_numvfs}="8"
```

### Virtual Functions in Proxmox

Ось приклад додавання до віртуальної машини віртуальний інтерфейс.

[![](/assets/images/blog/eed855a9dead091e-b614bd3ba49d64f3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8pd3GW8gtV4FHWgTPGP3n1LuocnBIyOcLK1pA-yBtvE5dbVIalt20JpDav4wLBXMY5I419C4XWE5k-TLj6MDDh8raYuOqOiZBql1eHjUIHmsKWSU17RTEjX7EA8TTNVcQCPcMcaIEEnyB/s918/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-05-25+03-00-47.png)  
*Використання віртуальних мережевих інтерфейсів у VM через pass-thru
PCI*

  

### VLAN on SR-IOV

Для визначення який vlan буде використовувати віртуальний мережевий
інтерфейес. 

```
ip link set enp3s0f1 vf 0 vlan 200
ip link set enp3s0f1 vf 1 vlan 201
ip link set enp3s0f1 vf 2 vlan 202
ip link show enp3s0f1
4: enp3s0f1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000  
    link/ether XX:XX:XX:XX:XX:XX brd ff:ff:ff:ff:ff:ff  
    vf 0 MAC 00:00:00:00:00:00, vlan 200, spoof checking on, link-state auto, trust off  
    vf 1 MAC 00:00:00:00:00:00, vlan 201, spoof checking on, link-state auto, trust off  
    vf 2 MAC 00:00:00:00:00:00, vlan 202, spoof checking on, link-state auto, trust off  
    vf 3 MAC 00:00:00:00:00:00, spoof checking on, link-state auto, trust off  
    vf 4 MAC 00:00:00:00:00:00, spoof checking on, link-state auto, trust off  
    vf 5 MAC 00:00:00:00:00:00, spoof checking on, link-state auto, trust off  
    vf 6 MAC 00:00:00:00:00:00, spoof checking on, link-state auto, trust off  
    vf 7 MAC 00:00:00:00:00:00, spoof checking on, link-state auto, trust off
```

### VM FreeBSD

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

За матеріалами:

* [i40e Linux\* Base Driver for the Intel(R) Ethernet Controller 700
  Series](https://downloadmirror.intel.com/24411/eng/readme_2.15.9.txt)
* [PCI Device Assignment with SR-IOV Devices Red Hat Enterprise Linux 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-pci_devices-pci_passthrough)
