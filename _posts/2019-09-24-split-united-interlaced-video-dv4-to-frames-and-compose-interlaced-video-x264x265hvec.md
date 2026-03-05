---
layout: post
title: "Split united interlaced video (.dv4) to frames and compose interlaced video x264,x265(hvec)"
date: 2019-09-24 00:48:00 +0000
tags: ["DVR", "ffmpeg", "HEVC", "multimedia", "security", "x265", "безпека"]
blogger_orig_link: https://lexxai.blogspot.com/2019/09/split-united-interlaced-video-to-frames.html
---

Є  відеофайлу з відео реєстратора  (AVtech DVR). Вихідне ім'я у нього .dv4 (Bosch Security Systems CCTV Video File) для його програвання є рідний [програвач VideoPlayer.exe](https://spreadys.wordpress.com/2014/09/13/avtech-and-dv4-files/).  
Задача зробити програвання відео з DVR відеореєстратора формату .dv4, засобами opensource.   
  
За аналізом [MedioInfo](https://mediaarea.net/uk/MediaInfo), це відео формату AVC ([x264](https://www.videolan.org/developers/x264.html)), прогресивний:  

```
Загальна інформація
Повна назва                              : video.dv4
Формат                                   : AVC
Формат/Відомості                         : Advanced Video Codec
Розмір файлу                             : 55.6 МіБ
FileExtension_Invalid                    : avc h264 264

Відео
Формат                                   : AVC
Формат/Відомості                         : Advanced Video Codec
Профіль формату                          : Baseline@L3
Налаштування формату                     : 1 Ref Frames
Налаштування формату, CABAC              : Ні
Налаштування формату, RefFrames          : 1 кадр
Ширина кадру                             : 720 пікс.
Висота кадру                             : 576 пікс.
Співвідношення сторін екрану             : 5:4
Простір кольору                          : YUV
Субдискретизація хроматичності           : 4:2:0
Бітова глибина                           : 8 біт
Тип сканування                           : Прогресивний
```

Тому для програвання підходить програвач  ffplay з пакунку [ffmpeg](https://ffmpeg.org/).  

```
ffplay -i video.dv4
```

Але так як ресторатор записує відео у черезрядковому форматі ([interlaced](https://en.wikipedia.org/wiki/Interlaced_video)), то відео записано об'єднаному форматі по вертикалі, де перше поле (А) зверху, друге поле (В) знизу.  

[![](/assets/images/blog/78a47af767373f17-a5aaa1dc2e800fc6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRxaxT-2h7gq5ueDc3pw9qoiKnkwdVnDCAqNpe8SoZriF3uKTd8fFlwL8IG09VhE0KR6RFOSW11XhSdc5ngdWgk0IZjMZrD6WjB18T7VfFwNm8ox4RKKTeDsV_BHAIH0dGnZH8k-dcv1-s/s1600/split-00.png)  
*Оригінальне відео*

  
Тому для програвання  треба дещо обробити  зображення, розділити на поля, і об'єднати знову але у традиційному відеоформаті interlaced з послідовними кадрами .  
  

[![](/assets/images/blog/2b536cb5fa33a0a0-be60b9c21d9bb5f9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhlUgvXNnih2czIGSmIEFAhzG8D-QFyoLosGXtc5JViABX7vISebgVyQfTgH6UCQBYTOepjkYKn9j7GOS7jHXu2tVdyv9qHB7sBpMDN_37imNujUL5mUEHgxaI9niiO2p-875oanZ2O9ktG/s1600/split-01.png)  
*Алгоритм розподілу кадрів*

Для виконання алгоритму використовуємо фільтри [ffmpeg](https://ffmpeg.org/ffmpeg-filters.html): [split](https://ffmpeg.org/ffmpeg-filters.html#split_002c-asplit), [crop](https://ffmpeg.org/ffmpeg-filters.html#crop), [scale](https://ffmpeg.org/ffmpeg-filters.html#scale-1), [framepack](https://ffmpeg.org/ffmpeg-filters.html#framepack).  

```
ffplay -i video.dv4  -vf "split [maint][mainb];[maint] crop=iw:ih/2:0:0 [ftop];[mainb] crop=iw:ih/2:0:ih/2 [fbot]; [ftop]scale=iw:ih[f1]; [fbot]scale=iw:ih[f2]; [f1][f2]framepack=lines"
```

Якщо треба вивести зображення з фільтром  deinterlace - yadif:  

```
ffplay -i video.dv4  -vf "split [maint][mainb];[maint] crop=iw:ih/2:0:0 [ftop];[mainb] crop=iw:ih/2:0:ih/2 [fbot]; [ftop]scale=iw:ih[f1]; [fbot]scale=iw:ih[f2]; [f1][f2]framepack=lines,yadif"
```

[![](/assets/images/blog/1e1ca96bd9581074-86e237a2734b2ad9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgUHUjZiUQV_cCz-TrxEUvYSa6ZfCGJ8WLErzTtFY2G0RmSHnvyPmTODNArqm6CtZ6kyrcTk0hSzLCKrEY9FDepmUPAkNXNizSlS3XtloWTTfmkPZ4-vJkoG8AEa90TMzQaknBt8cXX1V4H/s1600/split-02.png)  
*Результат програвання об'єднаних полів*

За бажанням можна зберегти файл для подальшого зберігання у рідному х264 форматі interleave.  
Розмір файлу video.dv4 56М   

```
ffmpeg -i video.dv4  -vf "split [maint][mainb];[maint]crop=iw:ih/2:0:0[ftop];[mainb]crop=iw:ih/2:0:ih/2[fbot];ftop]scale=iw:ih[f1];[fbot]scale=iw:ih[f2];[f1][f2]framepack=lines" -flags +ildct -c:v libx265 out-264.mp4
```

Розмір файлу out-264.mp4 39М  

```
Загальна інформація
Повна назва                              : out-264.mp4
Формат                                   : MPEG-4
Профіль формату                          : Base Media
Ідентифікатор кодеку                     : isom (isom/iso2/avc1/mp41)
Розмір файлу                             : 39.2 МіБ
Тривалість                               : 1 хв. 8 сек.
Загальний бітрейт                        : 4 832 Кбіт/сек
Програма кодування                       : Lavf58.27.102

Відео
Ідентифікатор                            : 1
Формат                                   : AVC
Формат/Відомості                         : Advanced Video Codec
Профіль формату                          : High@L3
Налаштування формату                     : CABAC / 4 Ref Frames
Налаштування формату, CABAC              : Так
Налаштування формату, RefFrames          : 4 кадр(-и,-ів)
Ідентифікатор кодеку                     : avc1
Ідентифікатор кодеку/Відомості           : Advanced Video Coding
Тривалість                               : 1 хв. 8 сек.
Бітрейт                                  : 4 830 Кбіт/сек
Ширина кадру                             : 720 пікс.
Висота кадру                             : 576 пікс.
Співвідношення сторін екрану             : 5:4
Режим частоти кадрів                     : Постійний
Частота кадрів                           : 25.000 кадр/сек
Стандарт                                 : PAL
Простір кольору                          : YUV
Субдискретизація хроматичності           : 4:2:0
Бітова глибина                           : 8 біт
Тип сканування                           : MBAFF
Послідовність сканування                 : Непарні - перші
Біт/(Пікселі*Кадри)                      : 0.466
Розмір потоку                            : 39.2 МіБ (100%)
Бібліотека кодування                     : x264 core 157 r2970 5493be8
Налаштування бібліот. кодування          : cabac=1 / ref=3 / deblock=1:0:0 / analyse=0x3:0x113 / me=hex / subme=7 / psy=1 / psy_rd=1.00:0.00 / mixed_ref=1 / me_range=16 / chroma_me=1 / trellis=1 / 8x8dct=1 / cqm=0 / deadzone=21,11 / fast_pskip=1 / chroma_qp_offset=-2 / threads=12 / lookahead_threads=2 / sliced_threads=0 / nr=0 / decimate=1 / interlaced=tff / bluray_compat=0 / constrained_intra=0 / bframes=3 / b_pyramid=2 / b_adapt=1 / b_bias=0 / direct=1 / weightb=1 / open_gop=0 / weightp=0 / keyint=250 / keyint_min=25 / scenecut=40 / intra_refresh=0 / rc_lookahead=40 / rc=crf / mbtree=1 / crf=23.0 / qcomp=0.60 / qpmin=0 / qpmax=69 / qpstep=4 / ip_ratio=1.40 / aq=1:1.00
Codec configuration box                  : avcC
```

  
Або до іншого формату [x265 (hevc):](https://uk.wikipedia.org/wiki/X265)  

```
ffmpeg -i video.dv4  -vf "split [maint][mainb];[maint]crop=iw:ih/2:0:0[ftop];[mainb]crop=iw:ih/2:0:ih/2[fbot];[ftop]scale=iw:ih[f1];[fbot]scale=iw:ih[f2];[f1][f2]framepack=lines" -c:v libx265 -x265-params interlace=tff  out-265.mp4
```

Розмір файлу out-265.mp4 8.5М  

```
Загальна інформація
Повна назва                              : out-265.mp4
Формат                                   : MPEG-4
Профіль формату                          : Base Media
Ідентифікатор кодеку                     : isom (isom/iso2/mp41)
Розмір файлу                             : 8.26 МіБ
Тривалість                               : 1 хв. 8 сек.
Загальний бітрейт                        : 1 017 Кбіт/сек
Програма кодування                       : Lavf58.27.102

Відео
Ідентифікатор                            : 1
Формат                                   : HEVC
Формат/Відомості                         : High Efficiency Video Coding
Профіль формату                          : Main@L3@Main
Ідентифікатор кодеку                     : hev1
Ідентифікатор кодеку/Відомості           : High Efficiency Video Coding
Тривалість                               : 1 хв. 8 сек.
Бітрейт                                  : 1 015 Кбіт/сек
Ширина кадру                             : 720 пікс.
Висота кадру                             : 576 пікс.
Співвідношення сторін екрану             : 5:4
Режим частоти кадрів                     : Постійний
Частота кадрів                           : 25.000 кадр/сек
Стандарт                                 : PAL
Простір кольору                          : YUV
Субдискретизація хроматичності           : 4:2:0
Бітова глибина                           : 8 біт
Тип сканування                           : Прогресивний
Біт/(Пікселі*Кадри)                      : 0.098
Розмір потоку                            : 8.24 МіБ (100%)
Бібліотека кодування                     : x265 3.0_Au+18-768ab38fd5fd:[Windows][GCC 8.3.1][64 bit] 8bit+10bit
Налаштування бібліот. кодування          : cpuid=1049071 / frame-threads=3 / numa-pools=8 / wpp / no-pmode / no-pme / no-psnr / no-ssim / log-level=2 / input-csp=1 / input-res=720x576 / interlace=1 / total-frames=0 / level-idc=0 / high-tier=1 / uhd-bd=0 / ref=3 / no-allow-non-conformance / no-repeat-headers / annexb / no-aud / no-hrd / info / hash=0 / no-temporal-layers / open-gop / min-keyint=25 / keyint=250 / gop-lookahead=0 / bframes=4 / b-adapt=2 / b-pyramid / bframe-bias=0 / rc-lookahead=20 / lookahead-slices=0 / scenecut=40 / radl=0 / no-splice / no-intra-refresh / ctu=64 / min-cu-size=8 / no-rect / no-amp / max-tu-size=32 / tu-inter-depth=1 / tu-intra-depth=1 / limit-tu=0 / rdoq-level=0 / dynamic-rd=0.00 / no-ssim-rd / signhide / no-tskip / nr-intra=0 / nr-inter=0 / no-constrained-intra / strong-intra-smoothing / max-merge=2 / limit-refs=3 / no-limit-modes / me=1 / subme=2 / merange=57 / temporal-mvp / weightp / no-weightb / no-analyze-src-pics / deblock=0:0 / sao / no-sao-non-deblock / rd=3 / no-early-skip / rskip / no-fast-intra / no-tskip-fast / no-cu-lossless / no-b-intra / no-splitrd-skip / rdpenalty=0 / psy-rd=2.00 / psy-rdoq=0.00 / no-rd-refine / no-lossless / cbqpoffs=0 / crqpoffs=0 / rc=crf / crf=28.0 / qcomp=0.60 / qpstep=4 / stats-write=0 / stats-read=0 / ipratio=1.40 / pbratio=1.30 / aq-mode=2 / aq-strength=1.00 / cutree / zone-count=0 / no-strict-cbr / qg-size=32 / no-rc-grain / qpmax=69 / qpmin=0 / no-const-vbv / sar=0 / overscan=0 / videoformat=5 / range=0 / colorprim=2 / transfer=2 / colormatrix=2 / chromaloc=0 / display-window=0 / max-cll=0,0 / min-luma=0 / max-luma=255 / log2-max-poc-lsb=8 / vui-timing-info / vui-hrd-info / slices=1 / no-opt-qp-pps / no-opt-ref-list-length-pps / no-multi-pass-opt-rps / scenecut-bias=0.05 / no-opt-cu-delta-qp / no-aq-motion / no-hdr / no-hdr-opt / no-dhdr10-opt / no-idr-recovery-sei / analysis-reuse-level=5 / scale-factor=0 / refine-intra=0 / refine-inter=0 / refine-mv=0 / refine-ctu-distortion=0 / no-limit-sao / ctu-info=0 / no-lowpass-dct / refine-analysis-type=31 / copy-pic=1 / max-ausize-factor=1.0 / no-dynamic-refine / no-single-sei / no-hevc-aq / no-svt / qp-adaptation-range=1.00
Codec configuration box                  : hvcC
```
