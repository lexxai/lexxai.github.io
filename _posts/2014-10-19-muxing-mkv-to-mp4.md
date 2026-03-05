---
layout: post
title: "Muxing MKV to MP4"
date: 2014-10-19 21:54:00 +0000
tags: ["mkv", "mp4", "multimedia", "mux", "muxing", "мультимедіа"]
blogger_orig_link: https://lexxai.blogspot.com/2014/10/muxing-mkv-to-mp4.html
---

Якщо у Вас фільм створено  у контейнері MKV, а потрібно обробити його, то для деяких програм він підходить. Наприклад програма Pinacle Studio 17 Ultimate може працювати з ними.   
Для робіт пов'язаних з монтажем звуку у відеофільмах у програмі Вегас потрібно мати справу з контейнером MP4. Можна у Вегас і передати .264 файл, але працювати не можливо.  
Тому перетворимо  MKV до MP4, без пере стискання форматів.  
Якщо розібрати MKV на складові за допомогою програми MKVExtractGUI  

[![](/assets/images/blog/c4e960c7f6c545a3-3385e774365af786.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxtdelPkbrjbALrKzxDpV3ltiUgYonvB65vzeav92RKan9M9AzzNDNlLsC9V1dgJk5KgoeQ4sBYyeCvOVwst2qJbgt0d3xQVHX7TPvl5qKfSl608iOEFEO1EArJxFDCujRHyiMV73vj1Gx/s1600/m01.PNG)  
*MKVExtractGUI*

То отримаємо два файли .264, та .AC3.  
АС3 перетворимо до контейнеру AAC , програмою MeGUI  

[![](/assets/images/blog/d5316e0c694b8161-e66989c3b6fde204.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_0H6YnJfZPHh4ZwDKQCraI8qfKXoojS5yC6RdDu9zW_7SyjsQp8BPqMHL_hkgteBGwOPSvC8AKvcZbaJlByloMk9pbXLVZd8LlYSjQuTuP880AmrwAOKYYyyCtCQnYKmfCNc0-YJVR42z/s1600/m02.PNG)  
*MeGUI, Open File*

Вибравши файл .AC3 і профіль Nero AAC: \*scratchpad\* (без перестискання).  

[![](/assets/images/blog/96bd00431de6e9cb-516945c6889410d2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtV46t1l9rJKCHIHt_qiXb8l2FDUA0r0A9xmwkmBpf1YIhEg0_aGPiLlh2BnZpY3uwFq5MUohQW7zFdslnUjTV_EpzFeAvzAjrhgoBUcbErBx0rgZX4Wzfnf7AwtjUIWhbLl0DOHF9xgMM/s1600/m03.PNG)  
*MeGUI, Nero AAC: \*scratchpad\**

Переносимо завдання в чергу, натиснув клавішу "Queue". Переходимо до закладки "Queue", і там запускаємо обробку чегри натиснув кнопку "Start".  

[![](/assets/images/blog/0bd3fdb1bac9cd70-eef9970077f2992c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiShz9JNVfAhu9l3vVPfC63o8s8LleTYqAKUePmDFbN1G_qBq5EnwvY8e4chxu1Uwkg6sdmokc2_C7da43mvvjciI6PKDk4nBiQV_MC9qKxTBG6iX9iS9Qus-Qqv7xuUFT0GdPNGG71d2PP/s1600/m04.PNG)  
*MeGUI, обробка черги*

 Отримані файли .h264 та .m4a об'єднуємо за допомогою MP4Box фіксуючи значення fps.  

```
mp4box.exe -add MuhtesemYuzyil.S04E119.1080p.Ukr.Tur.HURTOM-lexxai_track1_und.h264 -fps 25 \
 -add MuhtesemYuzyil.S04E119.1080p.Ukr.Tur.HURTOM-lexxai_track3_tur.m4a \
  MuhtesemYuzyil.S04E119.1080p.Ukr.Tur.HURTOM-lexxai_track3_tur.mp4
```

```
AVC-H264 import - frame size 1920 x 1080 at 25.000 FPS
AVC Import results: 173499 samples - Slices: 14459 I 159040 P 0 B - 0 SEI - 14459 IDR
IsoMedia import MuhtesemYuzyil.S04E119.....tur.m4a - track ID 1 - Audio (SR 48000 - 2 channels)
Saving to MuhtesemYuzyil.S04E119....._tur.mp4: 0.500 secs Interleaving
```

Або скориставшись графічним інтерфейсом MeGUI, Muxing MP4 (Ctrl+5).   

[![](/assets/images/blog/a6ac22fb7e8c9a64-2fc76f082d2c9fc2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaf1nOFBe2kzNStvCSHNjRjmwNcTCiWQhrwUNH_pPNB8skGam0rYGhBT794NpB2PqR6ka_2FFbp4bwkhyIJB0-WD4dMFCpW6ovdkfoVzVNFoO1-L-Fk0__z23lZneOy_zaTol9JIB5RBFc/s1600/MEGUI-muxmp4.PNG)  
*MeGUI, Muxing MP4*

Або скориставшись графічним інтерфейсом YAMB.  

[![](/assets/images/blog/072a755ed4782d4e-1bce70261c9b0c75.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEig49nuCOm1pt8H2iUiXx3oTz3MdtinvoyuObFBtCG9zFMesCOKXJQmBGy7CSocjGGR9rGHnocOc5m_zjQ-OxY-hotPMQwRLdSyrNJubhDC71PAcuftIbT0tGG3W5NL8YFL2sy4IvrXwZ20/s1600/m05.PNG)  
*YAMB, додавання файлів*

[![](/assets/images/blog/d477ee4c344129f1-f9c11ff280b2ea9a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2i1UBP9kKsqMDi86VD7HV3gc1mUYyQwpAkIH7cs0iN38h-fvtFLHvZrZjdZXsZYdifV9yzKzKvIH1-pQ8jU1bfYHHn6IsubuZPCLBt8dL5UuDQ5Peo48z4eILZ2ydww3j_asYyHjGhqx2/s1600/m06.PNG)  
*YAMB, MUX*

  
У результаті отримано файл mp4.  

### Media Info - mp4 файл:

```
General
Complete name                            : MuhtesemYuzyil.S04E119.1080p....._und.mp4
Format                                   : MPEG-4
Format profile                           : Base Media
Codec ID                                 : isom
File size                                : 3.33 GiB
Duration                                 : 1h 55mn
Overall bit rate mode                    : Variable
Overall bit rate                         : 4 116 Kbps
Encoded date                             : UTC 2014-10-18 21:42:42
Tagged date                              : UTC 2014-10-18 21:42:42

Video
ID                                       : 1
Format                                   : AVC
Format/Info                              : Advanced Video Codec
Format profile                           : High@L4.0
Format settings, CABAC                   : No
Format settings, ReFrames                : 1 frame
Format settings, GOP                     : M=1, N=12
Codec ID                                 : avc1
Codec ID/Info                            : Advanced Video Coding
Duration                                 : 1h 55mn
Bit rate                                 : 3 996 Kbps
Maximum bit rate                         : 7 551 Kbps
Width                                    : 1 920 pixels
Height                                   : 1 080 pixels
Display aspect ratio                     : 16:9
Frame rate mode                          : Constant
Frame rate                               : 25.000 fps
Color space                              : YUV
Chroma subsampling                       : 4:2:0
Bit depth                                : 8 bits
Scan type                                : Progressive
Bits/(Pixel*Frame)                       : 0.077
Stream size                              : 3.23 GiB (97%)
Encoded date                             : UTC 2014-10-18 21:42:42
Tagged date                              : UTC 2014-10-18 21:43:21

Audio
ID                                       : 2
Format                                   : AAC
Format/Info                              : Advanced Audio Codec
Format profile                           : LC
Codec ID                                 : 40
Duration                                 : 1h 55mn
Bit rate mode                            : Variable
Bit rate                                 : 118 Kbps
Maximum bit rate                         : 150 Kbps
Channel(s)                               : 2 channels
Channel positions                        : Front: L R
Sampling rate                            : 48.0 KHz
Compression mode                         : Lossy
Stream size                              : 97.8 MiB (3%)
Encoded date                             : UTC 2014-10-18 21:43:15
Tagged date                              : UTC 2014-10-18 21:43:21
```

### Media info MKV файл:

```
General
Complete name                            : MuhtesemYuzyil.S04E119......Tur.HURTOM-lexxai.mkv
Format                                   : Matroska
Format version                           : Version 4 / Version 2
File size                                : 3.59 GiB
Duration                                 : 1h 55mn
Overall bit rate                         : 4 446 Kbps
Encoded date                             : UTC 2014-10-19 19:05:58
Writing application                      : mkvmerge v7.1.0 ('Good Love') 64bit
Writing library                          : libebml v1.3.0 + libmatroska v1.4.1
DURATION                                 : 01:55:39.968000000
NUMBER_OF_FRAMES                         : 216874
NUMBER_OF_BYTES                          : 166559232

Video
ID                                       : 1
Format                                   : AVC
Format/Info                              : Advanced Video Codec
Format profile                           : High@L4.0
Format settings, CABAC                   : No
Format settings, ReFrames                : 1 frame
Format settings, GOP                     : M=1, N=12
Codec ID                                 : V_MPEG4/ISO/AVC
Duration                                 : 1h 55mn
Bit rate                                 : 3 910 Kbps
Width                                    : 1 920 pixels
Height                                   : 1 080 pixels
Display aspect ratio                     : 16:9
Frame rate mode                          : Constant
Frame rate                               : 25.000 fps
Color space                              : YUV
Chroma subsampling                       : 4:2:0
Bit depth                                : 8 bits
Scan type                                : Progressive
Bits/(Pixel*Frame)                       : 0.075
Stream size                              : 3.16 GiB (88%)
Default                                  : Yes
Forced                                   : No

Audio #1
ID                                       : 2
Format                                   : AC-3
Format/Info                              : Audio Coding 3
Mode extension                           : CM (complete main)
Format settings, Endianness              : Big
Codec ID                                 : A_AC3
Duration                                 : 1h 55mn
Bit rate mode                            : Constant
Bit rate                                 : 192 Kbps
Channel(s)                               : 2 channels
Channel positions                        : Front: L R
Sampling rate                            : 48.0 KHz
Bit depth                                : 16 bits
Compression mode                         : Lossy
Stream size                              : 159 MiB (4%)
Title                                    : 1+1 mix by lexxai
Language                                 : Ukrainian
Default                                  : Yes
Forced                                   : Yes


Audio #2
ID                                       : 3
Format                                   : AC-3
Format/Info                              : Audio Coding 3
Mode extension                           : CM (complete main)
Format settings, Endianness              : Big
Codec ID                                 : A_AC3
Duration                                 : 1h 55mn
Bit rate mode                            : Constant
Bit rate                                 : 256 Kbps
Channel(s)                               : 2 channels
Channel positions                        : Front: L R
Sampling rate                            : 48.0 KHz
Bit depth                                : 16 bits
Compression mode                         : Lossy
Stream size                              : 212 MiB (6%)
Language                                 : Turkish
Default                                  : No
Forced                                   : No
```
