---
layout: post
title: "Overclocking Intel Core i7 LGA1366 processors"
date: 2013-02-12 21:53:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/02/overclocking-intel-core-i7-lga1366.html
---

На сьогодні публікація є дещо застарілою, але для мене дуже цікавий опис методології розгону процесорів Intel i7 першого покоління.  

**[Overclocking Intel Core i7 LGA1366 processors](http://vip.asus.com/forum/view.aspx?id=20081220191040237&board_id=1&model=P6T+Deluxe&page=1&SLanguage=en-us) (**[Hiker](http://vip.asus.com/forum/profile_view.aspx?login=p04WojoVG1+0kx7jSgNncimLGAhQDBha&SLanguage=en-us)**)**

**Preface**

With the Core i7 900 series processors, all frequencies originate in a Base Clock (BCLK) with a default frequency of 133.33 MHz. From this base frequency, all other frequencies are derived by applying various multipliers.

The purpose of this text is mainly to bring an understanding of how to work with the Core i7 900 systems and how different BIOS settings relate to each other, and not so much to present a list of overclocking settings. Hopefully this will help you in finding an overclocking solution for your particular components.

**Overclocking**  

Easiest to overclock, are the Extreme Editions, Core i7 965 EE, Core i7 975 EE, Core i7 980X and Core i7 990X EE, due to their unlocked CPU multiplier. To overclock the CPU to e.g. 4 GHz, we simply need to raise the CPU multiplier (CPU Ratio Setting) from default 24/ 25/ 25/ 26 (3.20/ 3.33/ 3.33/ 3.46 GHz) to 30 (4.00 GHz), while keeping BCLK at its default frequency, 133 MHz.

To achieve the same overclock (4 GHz) on the models Core i7 950 and Core i7 960, the BCLK has to be increased, since their CPU multipliers are upwards locked at 24 for i7 950 and 25 for i7 960. The change of BCLK means that all other clock frequencies are influenced (memory, Uncore and QPI).

As an example, overclocking a Core i7 950 to 4 GHz, using the default CPU multiplier (23), would call for an increase of BCLK from default 133 MHz to 174 MHz (4000 / 23 = 174 MHz). That is a substantial overclock that will put stress on different components on the processor chip, which besides the CPU cores also holds the Uncore (the IMC, Integrated Memory Controller, and the L3 cache). There is also the QPI (Quick Path Interconnect), which is a fast bidirectional link between the CPU and the IOH (I/O Hub).

The DRAM frequency, the Uncore (UCLK) frequency and, perhaps, the QPI frequency may need to be adjusted according to the new BCLK.

|  |  |  |
| --- | --- | --- |
| **Core i7 900 series processors – frequency relations** | | |
| BCLK Frequency  (Base Clock) | Range: 100 – 500  Default: 133.33 MHz | Stability common up to BCLK ~180.  Max out @ BCLK between 180 and 225. |
| CPU Frequency = | BCLK x CPU Multiplier | 4 core, 45nm:  i7 920 – 2.66, i7 930 – 2.80, i7 940 – 2.93,  i7 950 – 3.06, i7 960 – 3.20,  i7 965 EE – 3.20, i7 975 EE – 3.33 GHz  6 core, 32nm:  i7 970 – 3.20, i7 980 – 3.33,  i7 980X EE – 3.33, i7 990X EE – 3.46 GHz |
| DRAM Frequency = | BCLK x Memory Multiplier | Memory Multipliers**\***: 6, 8, 10, 12, 14, 16, 18 |
| UCLK Frequency = | BCLK x Uncore Multiplier | ≥ 2 x DRAM Frequency |
| QPI Frequency = | BCLK x QPI Multiplier | QPI Multipliers: 18 (36), 22 (44), 24 (48) |

**\*** Memory Multipliers are invisible in the BIOS of ASUS motherboards. ASUS has chosen to use the resulting DRAM frequencies (BCLK x Memory Multipliers) instead.

|  |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Multipliers / Frequencies @ BCLK = 133 MHz**  **Note that all frequency options below change with the BCLK** | | | | |
| Processor  Model | CPU Ratio Setting  (Multiplier) | Memory  Multiplier | UCLK (Uncore)  Multiplier | QPI  Multiplier |
| Core i7 920  2.67 GHz | 12 – 21  Default 20 | 6, 8, 10, 12,  14, 16, 18  (DDR3-800)  (DDR3-1066)  (DDR3-1333)  (DDR3-1600)  (DDR3-1866)  (DDR3-2133)  (DDR3-2400) | ≥ 2 x Memory Multiplier  12, 13, 14, 15, ….., 26  (1600MHz)  (1733MHz)  (1866MHz)  (2000MHz)  (2133MHz)  (2266MHz)  (2400MHz)  (2533MHz)  (2666MHz)  (2800MHz)  (2933MHz)  (3066MHz)  (3200MHz)  (3333MHz)  (3466MHz) | 18, 22, 24  2400MHz  2933MHz  3200MHz  or  (36, 44, 48)  (4800 MT/s)  (5866 MT/s)  (6400 MT/s) |
| Core i7 930  2.80GHz | 12 – 22  Default 21 |
| Core i7 940  2.93 GHz | 12 – 23  Default 22 |
| Core i7 950 **\*\***  3.06 GHz | 12 – 24  Default 23 |
| Core i7 960 **\*\***  3.20 GHz | 12 – 25  Default 24 |
| Core i7 965  Extreme  3.20 GHz | 12 – 63  Default 24 |
| Core i7 975  Extreme  3.33 GHz | 12 – 63  Default 25 |
| Core i7 970 \*\*  6 core, 32nm  3.20 GHz | 12 – 25  Default 24 |
| Core i7 980 \*\*  6 core, 32nm  3.33 GHz | 12 – 26  Default 25 |
| Core i7 980X  6 core, 32nm  eXtreme  3.33 GHz | 12 – 63  Default 25 |
| Core i7 990X \*\*  6 core, 32nm  eXtreme  3.46 GHz | 12 – 63  Default 26 |

**\*\*** Presently manufactured model

ASUS has chosen to use the resulting frequencies instead of the multipliers in BIOS.

The available options for DRAM Frequency depend on the BCLK.

The available options for UCLK depend on the DRAM Frequency.

The available options for QPI depend on the BCLK.

**Intel® TurboMode Tech** (enabled by default)

When TurboMode Tech is enabled, the CPU multiplier may automatically increase with 1. With only 1 core active, the CPU Multiplier may increase with 2.

The CPU Ratio Setting has to be set to [Auto] to allow this function.

**Strategy for overclocking**

To simplify a stable overclocking, different strategies can be used. I’ll here focus on the Core i7 models Core i7 950/960. The Extreme Editions (Core i7 965, Core i7 975, Core i7 980X and Core i7 990X) are easier to overclock, due to their unlocked CPU multiplier, which eliminates the need to raise the Base Clock (BCLK).

You may want to raise BCLK to match the rated memory speed with the desired CPU speed though.

**Core i7 950**:

How to calculate the overlock - example.

A good starting point could be to calculate which BCLK you would need, to achieve your desired CPU speed. Let’s say that you would like a CPU speed of 3.6 GHz. The needed BCLK then is: 3600 / 24 = 150MHz, using the highest option for the CPU Ratio Setting, which is 24, manually set, for Core i7 950. CPU speed will be: BCLK x CPU Ratio Setting = 150 x 24 = 3600MHz (3.6GHz)

Let’s say that you want to use a DDR3-1600MHz memory. Determine the memory multiplier as DRAM Frequency / BCLK (1600 / 150 = 10.67) Use the nearest memory multiplier, which is 10. The nearest selectable option for DRAM Frequency will then be BCLK x memory multiplier (150 x 10 = 1500). Select DRAM Frequency [DDR3-1500] (which means a rather insignificant underclock of the memory).

The UCLK (Uncore) multiplier should be greater than, or equal to, 2 x the memory multiplier: 2 x 10 = 20.

Uncore Frequency is BCLK x UCLK multiplier (150 x 20 = 3000). Select UCLK Frequency [3000MHz].

This setting can also be left at [Auto].

The QPI (Quick Path Interconnect) is a high speed, bi-directional, link between the CPU and the X58 IOH (I/O Hub). There is hardly anything to gain from increasing the multiplier from the default value (18) so we leave it at that. This means a QPI Frequency of 150 x 18 = 2700MHz, or 150 x 36 = 5400MT/s (Mega Transfers per second).

This setting is preferably set manually to the lowest option when overclocking by raising the BCLK.

Next step is to lower the CPU Ratio Setting to 14 and run Memtest86+ v4.20 (or later) to verify the stability for memory, Uncore and QPI, without the risk of limitations due to the CPU speed. If errors occur, try to increase the QPI/DRAM Core Voltage.

If Memtest86+ runs error free, set the CPU Ratio Setting to 24 or [Auto]. The setting [Auto] means default 23 for i7 950, but with TurboMode Tech enabled, the CPU Ratio Setting will normally be 24.

Finally, running Prime95 for a total of 8 virtual cores, the PC has to be stable for at least overnight.

**Manual configurations**

**Ai Overclock Tuner**

|  |  |
| --- | --- |
| **Manual** | Allows you to individually set overclocking parameters. (**Recommended when overclocking**). |
| **Auto** | Loads the standard settings for the system. |
| **D.O.C.P.** | Overclocks DRAM Frequency by adjusting the BCLK frequency. |
| **X.M.P.** | If you install memory modules supporting eXtreme Memory Profile (XMP) technology, choose this item to set the profile supported by your memory modules for optimizing the system performance. |

**CPU Voltage** [1.20V – 1.375V] (total range: 0.85V-1.70V (2.10V)).

Initially I recommend leaving this setting at [Auto]. That means that the CPU Voltage is set according to the VID (Voltage Identification Code). VID is automatically adjusted, in steps, according to the CPU frequency. Since the setting [Auto] in many cases gives an unnecessary high CPU Voltage, resulting in an unnecessary high CPU temperature, I recommend tweaking this voltage manually when you are happy with your overclock (see Voltage optimization below).

**CPU PLL Voltage** [Auto] or [1.80V – 2.10V] (total range: 1.80V-2.50V).

**DRAM Bus Voltage** [max 1.65V] (1.64V-1.66V) (total range: 1.50V-1.90V (2.46V)) Intel states, that a DRAM Bus Voltage beyond 1.65V may permanently damage the processor.

If a lower voltage than 1.65V is specified for your memory, then use the specified voltage.

For DDR3-800MHz - DDR3-1333MHz memory, the voltage normally is 1.5V (JEDEC standard voltage for DDR3 memory).

**QPI/DRAM Core Voltage** [1.20V – 1.40V] (total range: 1.20V-1.70V (1.90V)).

QPI/DRAM Core Voltage must be within 0.5V of the DRAM Bus Voltage. This could only be an issue when the DRAM Bus Voltage is set very high (1.71V-2.46V).

This voltage setting is very important. DDR3-1600MHz memory may need this voltage to be 1.35V and DDR3-2000MHz may need as much as 1.65V. I recommend setting this voltage manually, and when you are happy with your overclock, tweak the voltage (see Voltage optimization below).

The other voltages may be left at default (Auto).

**BCLK Frequency** [XXX] (default 133 MHz, range: 100-500 MHz)

**DRAM Frequency** [DDR3-XXXX] (range: DDR3-800MHz – DDR3-2000MHz)

According to Intel’s specification, only DDR3-800MHz and DDR3-1066MHz are supported. Using DDR3-1333MHz memory, and higher frequencies, would mean overclocking (of the IMC).

(Tests have shown that there is a very small performance gain in using faster memory than DDR3-1333MHz).

**PCIE Frequency** [100] (locks the PCIE frequency)

Tweaking in the range of 100-110 may be necessary.

**Voltage optimization**

At least CPU Voltage and QPI/DRAM Core Voltage should be set manually.

When you are satisfied with your overclock, I recommend optimizing CPU Voltage and QPI/DRAM Core Voltage, which means finding the lowest possible voltage where 100% Prime95 stability is maintained for at least overnight.

Start with optimizing the CPU Voltage by lowering the CPU Voltage by 1-2 notch(es) at a time, until Prime95 stops the execution within 10 minutes – then increase the voltage by 1 notch at a time and test again. In the end, Prime95 should be able to run for at least overnight.

Use Prime95, the same way as described for the CPU Voltage, to find the lowest possible QPI/DRAM Core Voltage that allows the PC to run error free under stress for at least overnight.

Finally, run Memtest86+ (version 4.20, or later) to verify that Memtest86+ runs totally error free for at least overnight. If any error, try with increasing QPI/DRAM Core Voltage by 1 notch at a time until Memtest86+ runs error free.

|  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Overclockings for Core i7 950 / Core i7 960** | | | | | |
| Base Clock  (BCLK)  MHz | Memory  Multiplier  DRAM Freq. MHz | Uncore  Multiplier  UCLK Freq.  MHz | QPI  Multiplier  QPI Freq. MT/s | CPU i7 950  Multiplier  Freq. GHz | CPU i7 960  Multiplier  Freq. GHz |
| 133.33 | 6 ó DDR3-800 | 12 ó 1600 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 8 ó DDR3-1066 | 16 ó 2133 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 10 ó DDR3-1333 | 20 ó 2666 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 12 ó DDR3-1600 | 24 ó 3200 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 14 ó DDR3-1866 | 28 ó 3733 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 16 ó DDR3-2133 | 32 ó 4266 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 133.33 | 18 ó DDR3-2400 | 36 ó 4800 | 18 (36) ó 4800 | 24 ó 3.20 | 25 ó 3.33 |
| 143 | 14 ó DDR3-2002 | 28 ó 4004 | 18 (36) ó 5148 | 24 ó 3.43 | 25 ó 3.57 |
| 150 | 10 ó DDR3-1500 | 20 ó 3000 | 18 (36) ó 5400 | 24 ó 3.60 | 25 ó 3.75 |
| 155.67 | 12 ó DDR3-1868 | 24 ó 3736 | 18 (36) ó 5604 | 24 ó 3.73 | 25 ó 3.89 |
| 160 | 10 ó DDR3-1600 | 20 ó 3200 | 18 (36) ó 5760 | 24 ó 3.84 | 25 ó 4.00 |
| 166.67 | 8 ó DDR3-1333 | 16 ó 2666 | 18 (36) ó 6000 | 24 ó 4.00 | 24 ó 4.00 |
| 166.67 | 10 ó DDR3-1666 | 20 ó 3333 | 18 (36) ó 6000 | 24 ó 4.00 | 24 ó 4.00 |
| 166.67 | 12 ó DDR3-2000 | 24 ó 4000 | 18 (36) ó 6000 | 24 ó 4.00 | 24 ó 4.00 |
| 177.67 | 6 ó DDR3-1066 | 12 ó 2132 | 18 (36) ó 6396 | 22 ó 3.91 | 22 ó 3.91 |
| 180 | 8 ó DDR3-1440 | 16 ó 2880 | 18 (36) ó 6480 | 22 ó 3.96 | 22 ó 3.96 |
| 185 | 8 ó DDR3-1480 | 16 ó 2960 | 18 (36) ó 6660 | 22 ó 4.07 | 22 ó 4.07 |
| 190 | 8 ó DDR3-1520 | 16 ó 3040 | 18 (36) ó 6840 | 21 ó 3.99 | 21 ó 3.99 |
| 191 | 8 ó DDR3-1528 | 16 ó 3056 | 18 (36) ó 6876 | 21 ó 4.01 | 21 ó 4.01 |
| 195 | 8 ó DDR3-1560 | 16 ó 3120 | 18 (36) ó 7020 | 21 ó 4.09 | 21 ó 4.09 |
| 200 | 6 ó DDR3-1200 | 12 ó 2400 | 18 (36) ó 7200 | 20 ó 4.00 | 20 ó 4.00 |
| 200 | 8 ó DDR3-1600 | 16 ó 3200 | 18 (36) ó 7200 | 20 ó 4.00 | 20 ó 4.00 |
| 180 | 10 ó DDR3-1800 | 20 ó 3600 | 18 (36) ó 6480 | 22 ó 3.96 | 22 ó 3.96 |
| 186.67 | 10 ó DDD3-1867 | 20 ó 3734 | 18 (36) ó 6720 | 21 ó 3.92 | 21 ó 3.92 |
| 200 | 10 ó DDR3-2000 | 20 ó 4000 | 18 (36) ó 7200 | 20 ó 4.00 | 20 ó 4.00 |

The CPU Ratio Setting (CPU Multiplier) is above set manually to the highest option (in most cases). CPU Ratio Setting at [Auto] with TurboMode Tech enabled (default), would normally mean an effective CPU Ratio Setting of 24 for i7 950 and 25 for i7 960.

Note that there is no “guarantee” that the processor is able to run above 3.6 GHz. If adjusting CPU Voltage and/or QPI/DRAM Core Voltage doesn’t make the PC stable, the CPU Ratio Setting or the BCLK can be lowered.

The table below shows possible combinations of Memory Multiplier and BCLK for running the memory at rated speed. The table may help you to choose the right memory modules for your goals.

**\*\*\*** Although the Memory Multipliers are not visible in BIOS, the available DRAM Frequency options are still based upon them (DRAM Frequency = BCLK x Memory Multiplier). When changing the BCLK, the DRAM Frequency options will also change automatically.

The real obtained DRAM frequencies may differ slightly from what you see below because of small variations in the BCLK Frequency.

BCLK settings that run memory at rated speed

|  |  |  |  |
| --- | --- | --- | --- |
| **Rated**  **DRAM Frequency**      [MHz] | **BCLK**        [MHz] | **Memory**  **Multiplier**  (Invisible in BIOS)  **\*\*\*** | **Obtained**  **DRAM Frequency**  (BCLK x Memory Multiplier)    [MHz] |
| DDR3-1066 | **177**(.67)  **133**(.33)  106(.67)  N/A  N/A  N/A | **6**  **8**  10  12  14  16 | DDR3-1066  DDR3-1066  DDR3-1066  N/A  N/A  N/A |
| DDR3-1333 | 222  **166**(.67)  **133**(.33)  111  N/A  N/A | 6  **8**  **10**  12  14  16 | DDR3-1332  DDR3-1333  DDR3-1333  DDR3-1332  N/A  N/A |
| DDR3-1600 | (266.67)  **200**  **160**  **133**(.33)  114(.33)  100 | 6  **8**  **10**  **12**  14  16 | (DDR3-1600)  DDR3-1600  DDR3-1600  DDR3-1600  DDR3-1600  DDR3-1600 |
| DDR3-1800 | (300)  (225)  **180**  **150**  128(.67)  112(.67) | 6  8  **10**  **12**  14  16 | (DDR3-1800)  DDR3-1800  DDR3-1800  DDR3-1800  DDR3-1801  DDR3-1802 |
| DDR3-1866 | (311)  (233)  **186**(.67)  **155**(.67)  **133**(.33)  116(.67) | 6  8  **10**  **12**  **14**  16 | (DDR3-1866)  (DDR3-1866)  DDR3-1866  DDR3-1868  DDR3-1866  DDR3-1866 |
| DDR3-2000 | (333.33)  (250)  **200**  **166**(.67)  **143**  125 | 6  8  **10**  **12**  **14**  16 | (DDR3-2000)  (DDR3-2000)  DDR3-2000  DDR3-2000  DDR3-2002  DDR3-2000 |

Rules and relations:

BCLK Frequency (Base Clock – default 133 MHz, range: 100-500)

CPU speed = BCLK x CPU Multiplier (CPU Multiplier = CPU Ratio Setting)

DRAM Frequency = BCLK x Memory Multiplier (6, 8, 10, 12, 14, 16, 18)

UCLK Frequency = BCLK x Uncore Multiplier (Selectable options: 1600-5600 MHz)

QPI Frequency = BCLK x QPI Multiplier, 18(36), 22(44), 24(48). (Selectable options: 4800/5866/6400 MT/s @ BCLK 133 MHz). (The absolute maximum speed (no guarantee) is around 8000 MT/s)

UCLK (Uncore) Multiplier (frequency) must be at least twice the Memory Multiplier (frequency)

Use CPU-Z to watch the obtained settings

|  |  |  |
| --- | --- | --- |
| **CPU-Z utility** | | |
| **BIOS setting** | **CPU-Z location** | **Comment** |
| - | CPU tab, **Core Speed** | MHz |
| CPU Ratio Setting | CPU tab, **Multiplier** | CPU multiplier |
| BCLK Frequency | CPU tab, **Bus Speed** | MHz |
| QPI Link Data Rate | CPU tab, **QPI Link (Rated FSB)** | MHz (x2 for MT/s) |
| UCLK Frequency | Memory tab, **NB Frequency** | MHz (Uncore) |
| DRAM Frequency | Memory tab, **DRAM Frequency** | x2 for DDR3 frequency |
| - | Memory tab, **FSB:DRAM**, e.g. 2:**8** | Memory Multiplier is the second figure |
| DRAM timings | Memory tab | E.g. 8-8-8-24 … |
| - | SPD tab | Shows the memory profiles |

Use the Icrontic Nehalem Overclocking Calculator to calculate your own settings. If you select the middle QPI multiplier, remember to change the value from 20 à 22 (just type in 22 over 20).

Download: [Here](http://icrontic.com/uploads/2008/11/nehalemcalc.zip)

New version:  
[Icrontic Nehalem Calculator](http://icrontic.com/apps/nehalem-calc/)

**CPU Cooling**

An overclocked system will need better CPU cooling than what the standard (stock) Intel cooler can provide.

Examples of the very best air coolers are:

- Noctua NH-U12P SE2 or NH-14D (they both come with 2 quiet, high quality fans for push-pull operation)

- Thermalright Ultra-120 Extreme 1366 RT (no fan included)
