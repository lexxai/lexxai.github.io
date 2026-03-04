---
layout: post
title: "Intel С236 та HSIO Multiplexing"
date: 2019-01-10 21:51:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/01/intel-236-hsio-multiplexing.html
---

Для себе роблю нотатку, щоб зрозуміти якщо навантажити усі 8 SATA пристрої і використовувати також усі мережеві адаптери (GbE) .  
Чи буде конфлікт у SATA0 та GbE згідно "Skylake C236 HSIO (High Speed Input/Output) Multiplexing on PCH-H"?  

[![](/assets/images/blog/73ccaba276a3c80d-6a035bfffdbf63ee.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjXUBASS8y4_MWAqutEcuizwrbgFLvYJGmMsKI1yfe4lMFTYL421FdM2H4ILt14l_gNK3FmIEaT5eBvdABlWAViqa-qo_zp8rP4yHN3API9GK06MZwfetvxvrmalqF2SpKog0BJFxQ10PF/s1600/c236lanes02.PNG)  
*P10S-E/4L block diagram with Skylake C236*

  

[![](/assets/images/blog/887dc200a405d90c-917a3cd55223d17b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqs69lz3I45U1dXvonP33stHQ64AlPixcZ7wMDElBorIsEsX2YUeiNK4l51nEFVhxDyhI7NemjAgfkZhJrgVFeoEqDV5snABx3EKkXstzSMT3_PaRvLIIJRLErYu7e9aUTGfEOQ11EdVjv/s1600/c236lanes01.PNG)  
*Skylake C236 HSIO Multiplexing on PCH-H*

  
Поточна конфігурація пристроїв у системі
  

```
lspci -tv
-+-[0000:08]---00.0  Intel Corporation I210 Gigabit Network Connection
 +-[0000:07]---00.0  Intel Corporation I210 Gigabit Network Connection
 +-[0000:06]---00.0  Intel Corporation I210 Gigabit Network Connection
 +-[0000:03]---00.0-[04]--
 \-[0000:00]-+-00.0  Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Host Bridge/DRAM Registers
             +-14.0  Intel Corporation Sunrise Point-H Thermal subsystem
             +-14.0  Intel Corporation Sunrise Point-H USB 3.0 xHCI Controller
             +-16.0  Intel Corporation Sunrise Point-H CSME HECI #1
             +-17.0  Intel Corporation C600/X79 series chipset SATA RAID Controller
             +-1c.0-[01-02]--
             +-1c.0-[01-02]----00.0-[02]----00.0  ASPEED Technology, Inc. ASPEED Graphics Family
             +-1d.0-[05]--
             +-1d.0-[05]--
             +-1d.0-[05]--
             +-1d.0-[05]----00.0  Intel Corporation I210 Gigabit Network Connection
             +-1f.0  Intel Corporation Sunrise Point-H PMC
             +-1f.0  Intel Corporation Sunrise Point-H SMBus
             \-1f.0  Intel Corporation Sunrise Point-H LPC Controller
```

```
pciconf -lv
hostb0@pci0:0:0:0:      class=0x060000 card=0x19188086 chip=0x19188086 rev=0x07 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Host Bridge/DRAM Registers'
    class      = bridge
    subclass   = HOST-PCI
xhci0@pci0:0:20:0:      class=0x0c0330 card=0x868b1043 chip=0xa12f8086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H USB 3.0 xHCI Controller'
    class      = serial bus
    subclass   = USB
none0@pci0:0:20:2:      class=0x118000 card=0xa1318086 chip=0xa1318086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H Thermal subsystem'
    class      = dasp
none1@pci0:0:22:0:      class=0x078000 card=0xa13a8086 chip=0xa13a8086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H CSME HECI'
    class      = simple comms
ahci0@pci0:0:23:0:      class=0x010400 card=0x868c1043 chip=0x28268086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'C600/X79 series chipset SATA RAID Controller'
    class      = mass storage
    subclass   = RAID
pcib1@pci0:0:28:0:      class=0x060400 card=0xa1148086 chip=0xa1148086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
pcib3@pci0:0:28:5:      class=0x060400 card=0xa1158086 chip=0xa1158086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
pcib5@pci0:0:29:0:      class=0x060400 card=0xa1188086 chip=0xa1188086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
pcib6@pci0:0:29:1:      class=0x060400 card=0xa1198086 chip=0xa1198086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
pcib7@pci0:0:29:2:      class=0x060400 card=0xa11a8086 chip=0xa11a8086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
pcib8@pci0:0:29:3:      class=0x060400 card=0xa11b8086 chip=0xa11b8086 rev=0xf1 hdr=0x01
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PCI Express Root Port'
    class      = bridge
    subclass   = PCI-PCI
isab0@pci0:0:31:0:      class=0x060100 card=0xa1498086 chip=0xa1498086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H LPC Controller'
    class      = bridge
    subclass   = PCI-ISA
none2@pci0:0:31:2:      class=0x058000 card=0xa1218086 chip=0xa1218086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H PMC'
    class      = memory
none3@pci0:0:31:4:      class=0x0c0500 card=0xa1238086 chip=0xa1238086 rev=0x31 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'Sunrise Point-H SMBus'
    class      = serial bus
    subclass   = SMBus
pcib2@pci0:1:0:0:       class=0x060400 card=0x11501a03 chip=0x11501a03 rev=0x03 hdr=0x01
    vendor     = 'ASPEED Technology, Inc.'
    device     = 'AST1150 PCI-to-PCI Bridge'
    class      = bridge
    subclass   = PCI-PCI
vgapci0@pci0:2:0:0:     class=0x030000 card=0x85f91043 chip=0x20001a03 rev=0x30 hdr=0x00
    vendor     = 'ASPEED Technology, Inc.'
    device     = 'ASPEED Graphics Family'
    class      = display
    subclass   = VGA
pcib4@pci0:3:0:0:       class=0x060400 card=0x84891043 chip=0x10801b21 rev=0x04 hdr=0x01
    vendor     = 'ASMedia Technology Inc.'
    device     = 'ASM1083/1085 PCIe to PCI Bridge'
    class      = bridge
    subclass   = PCI-PCI
igb0@pci0:5:0:0:        class=0x020000 card=0x85571043 chip=0x15338086 rev=0x03 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'I210 Gigabit Network Connection'
    class      = network
    subclass   = ethernet
igb1@pci0:6:0:0:        class=0x020000 card=0x85571043 chip=0x15338086 rev=0x03 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'I210 Gigabit Network Connection'
    class      = network
    subclass   = ethernet
igb2@pci0:7:0:0:        class=0x020000 card=0x85571043 chip=0x15338086 rev=0x03 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'I210 Gigabit Network Connection'
    class      = network
    subclass   = ethernet
igb3@pci0:8:0:0:        class=0x020000 card=0x85571043 chip=0x15338086 rev=0x03 hdr=0x00
    vendor     = 'Intel Corporation'
    device     = 'I210 Gigabit Network Connection'
    class      = network
    subclass   = ethernet
```

```
devinfo -u
Interrupt request lines:
    0x0 (attimer0)
    0x1 (atkbdc0)
    0x3 (uart1)
    0x4 (uart0)
    0x5-0x7 (root0)
    0x8 (atrtc0)
    0x9 (acpi0)
    0xa-0x77 (root0)
    0x100 (hpet0)
    0x101 (hpet0)
    0x102 (hpet0)
    0x103 (hpet0)
    0x104 (hpet0)
    0x105 (hpet0)
    0x106 (hpet0)
    0x107 (hpet0)
    0x108 (xhci0)
    0x109 (ahci0)
    0x10a (igb0)
    0x10b (igb0)
    0x10c (igb0)
    0x10d (igb0)
    0x10e (igb0)
    0x10f (igb1)
    0x110 (igb1)
    0x111 (igb1)
    0x112 (igb1)
    0x113 (igb1)
    0x114 (igb2)
    0x115 (igb2)
    0x116 (igb2)
    0x117 (igb2)
    0x118 (igb2)
    0x119 (igb3)
    0x11a (igb3)
    0x11b (igb3)
    0x11c (igb3)
    0x11d (igb3)

PCI domain 0 bus numbers:
    0 (pci0)
    1-2 (pcib1)
    3-4 (pcib3)
    5 (pcib5)
    6 (pcib6)
    7 (pcib7)
    8 (pcib8)
    9-255 (root0)
```

```
lspci -vv

00:17.0 RAID bus controller: Intel Corporation C600/X79 series chipset SATA RAID Controller (rev 31)
        Subsystem: ASUSTeK Computer Inc. Device 868c
        Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
        Status: Cap+ 66MHz+ UDF- FastB2B+ ParErr- DEVSEL=medium TAbort- TAbort- MAbort- SERR- PERR- INTx-
        Latency: 0
        Interrupt: pin A routed to IRQ 255
        Region 0: Memory at de510000 (32-bit, non-prefetchable)
        Region 1: Memory at de51e000 (32-bit, non-prefetchable)
        Region 2: I/O ports at f050
        Region 3: I/O ports at f040
        Region 4: I/O ports at f020
        Region 5: Memory at de51d000 (32-bit, non-prefetchable)
        Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
                Address: fee00000  Data: 0032
        Capabilities: [70] Power Management version 3
                Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0-,D1-,D2-,D3hot+,D3cold-)
                Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-
        Capabilities: [a8] SATA HBA v1.0 BAR4 Offset=00000004


05:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
        Subsystem: ASUSTeK Computer Inc. Device 8557
        Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
        Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
        Latency: 0
        Interrupt: pin A routed to IRQ 255
        Region 0: Memory at de400000 (32-bit, non-prefetchable)
        Region 2: I/O ports at d000
        Region 3: Memory at de480000 (32-bit, non-prefetchable)
        Capabilities: [40] Power Management version 3
                Flags: PMEClk- DSI+ D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
                Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=1 PME-
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
                Address: 0000000000000000  Data: 0000
                Masking: 00000000  Pending: 00000000
        Capabilities: [70] MSI-X: Enable+ Count=5 Masked-
                Vector table: BAR=3 offset=00000000
                PBA: BAR=3 offset=00002000
        Capabilities: [a0] Express (v2) Endpoint, MSI 00
                DevCap: MaxPayload 512 bytes, PhantFunc 0, Latency L0s <512ns, L1 <64us
                        ExtTag- AttnBtn- AttnInd- PwrInd- RBE+ FLReset+ SlotPowerLimit 0.000W
                DevCtl: Report errors: Correctable- Non-Fatal- Fatal- Unsupported-
                        RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop+ FLReset-
                        MaxPayload 256 bytes, MaxReadReq 512 bytes
                DevSta: CorrErr+ UncorrErr- FatalErr- UnsuppReq+ AuxPwr+ TransPend-
                LnkCap: Port #0, Speed 2.5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <2us, L1 <16us
                        ClockPM- Surprise- LLActRep- BwNot- ASPMOptComp+
                LnkCtl: ASPM L1 Enabled; RCB 64 bytes Disabled- CommClk+
                        ExtSynch- ClockPM- AutWidDis- BWInt- AutBWInt-
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
                DevCap2: Completion Timeout: Range ABCD, TimeoutDis+, LTR-, OBFF Not Supported
                DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis-, LTR-, OBFF Disabled
                         AtomicOpsCtl: ReqEn-
                LnkCtl2: Target Link Speed: 2.5GT/s, EnterCompliance- SpeedDis-
                         Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
                         Compliance De-emphasis: -6dB
                LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete-, EqualizationPhase1-
                         EqualizationPhase2-, EqualizationPhase3-, LinkEqualizationRequest-
        Capabilities: [100 v2] Advanced Error Reporting
                UESta:  DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
                UEMsk:  DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
                UESvrt: DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+ ECRC- UnsupReq- ACSViol-
                CESta:  RxErr- BadTLP- BadDLLP- Rollover- Timeout- NonFatalErr+
                CEMsk:  RxErr- BadTLP- BadDLLP- Rollover- Timeout- NonFatalErr+
                AERCap: First Error Pointer: 00, ECRCGenCap+ ECRCGenEn- ECRCChkCap+ ECRCChkEn-
                        MultHdrRecCap- MultHdrRecEn- TLPPfxPres- HdrLogCap-
                HeaderLog: 00000000 00000000 00000000 00000000
        Capabilities: [140 v1] Device Serial Number 34-97-f6-ff-ff-5b-84-bf
        Capabilities: [1a0 v1] Transaction Processing Hints
                Device specific mode supported
                Steering table in TPH capability structure
```

```
lspci -vv | egrep "[0-9a-f]{2}:[0-9a-f]{2}\.[0-9a-f]|LnkSta:"
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Host Bridge/DRAM Registers (rev 07)
00:14.0 USB controller: Intel Corporation Sunrise Point-H Thermal subsystem (rev 31) (prog-if 30 [XHCI])
00:14.0 USB controller: Intel Corporation Sunrise Point-H USB 3.0 xHCI Controller (rev 31) (prog-if 30 [XHCI])
00:16.0 Communication controller: Intel Corporation Sunrise Point-H CSME HECI #1 (rev 31)
00:17.0 RAID bus controller: Intel Corporation C600/X79 series chipset SATA RAID Controller (rev 31)
00:1c.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #5 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1c.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #6 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #12 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #9 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train+ SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #10 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train+ SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-H PCI Express Root Port #11 (rev f1) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive+ BWMgmt+ ABWMgmt-
00:1f.0 ISA bridge: Intel Corporation Sunrise Point-H PMC (rev 31)
00:1f.0 ISA bridge: Intel Corporation Sunrise Point-H SMBus (rev 31)
00:1f.0 ISA bridge: Intel Corporation Sunrise Point-H LPC Controller (rev 31)
01:00.0 PCI bridge: ASPEED Technology, Inc. AST1150 PCI-to-PCI Bridge (rev 03) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
02:00.0 VGA compatible controller: ASPEED Technology, Inc. ASPEED Graphics Family (rev 30) (prog-if 00 [VGA controller])
03:00.0 PCI bridge: ASMedia Technology Inc. ASM1083/1085 PCIe to PCI Bridge (rev 04) (prog-if 00 [Normal decode])
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk- DLActive- BWMgmt- ABWMgmt-
05:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
06:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
07:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
08:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
                LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
 
```

  
  
Матеріали:
  

1. [ASUS P10S-E-4L - Manual](https://dlcdnets.asus.com/pub/ASUS/mb/socket1151/P10S-E-4L/Manual/E13686_P10S-E_4L_UM_V4_WEB.pdf)
2. [Intel® 100 Series Chipset Family PCH Datasheet, Vol. 1](https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/100-series-chipset-datasheet-vol-1.pdf)
