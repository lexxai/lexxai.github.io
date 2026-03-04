---
layout: post
title: "Microchip PIC10/12/16. Що таке read-modify-write (RMW)?"
date: 2014-06-07 15:07:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/06/microchip-pic101216-read-modify-write.html
---

[![](/assets/images/blog/5592271b6a041a7d-ff3d798fb72e8ba3.png)](http://www.microchip.com/_images/picmicrocontrollers/PIC-MCU-Chip.png)

1. Що таке read-modify-write (RMW)?  
read-modify-write - Читати-модифікувати-записати. Це означає що якщо ми записуємо до регістру певного порту мікроконтролера, то запис проходить не зразу, а послідовно: зчитуємо стан, модифікуємо і записуємо новий. Але якщо ви модифікували один біт, а що з іншими? А з іншими може бути проблема, так як буде зчитано їх електричний поточний стан, і те що "зчиталося" буде записано назад.  
  
Розглянемо наступний код мовою С:  

```
RB0 = 1;
RB1 = 1;
```

Розумний компілятор буде генерувати інструкції BSF (встановити біт регістра)  для кожного з цих тверджень. Інструкція BSF не просто встановлює значення одного біта. Внутрішньо, PIC читає всі вісім бітів з порту, встановлює зміни у потрібну біті, і записує всі вісім біт назад в порт. Для однієї команди BSF, це не викликає проблеми. Але коли дві і більше команд BSF на тому ж порту працюють разом, результати першої команди BSF можуть бути не такими як очікувалося.  
Проблема виникає, коли друга команда BSF читає стан порту. Якщо напруга на RB0 ще не піднялася до рівня логічної "1"(VIH), то PIC зчитуває значення логічного "0" для RB0, встановіть біт  RB1 у логічну "1", і запише результат назад в порт. Це залишить "0" в RB0, і "1" в RB1, що є не бажаним результатом.  
RMW може стати більш важливою проблемою, чим більш високі частоти PIC, тому що кількість часу між інструкціями менше, що вимагає більш швидкого реагування для змін значень порту, щоб уникнути читання неправильного стану.  
  
Є кілька різних виправлень для вирішення проблем RMW, вони перераховані в порядку кращого до гіршого:  

* На PIC18, PIC24/dsPIC, і enhanced midrange PIC16 (PIC16F1xxx), доступні засувки порту (port latch). Читання з засувки не читає назад стан виводів порту, тому інструкції BSF можна безпечно використовувати послідовно. На цих PIC, всі операції запису необхідно робити через регістри LAT, і операції читаня робити безпосередньо з регістрів порту. І ніколи не пишіть безпосередньо в регістри порту.
* При використанні baseline або standard midrange (PIC10/12/16), використовуйте тіньовий регістр (SHADOW) для порту. Наприклад:

  ```
  union {
      unsigned char byte;
      struct {
          unsigned RB0:1;
          unsigned RB1:1;
          unsigned RB2:1;
          unsigned RB3:1;
          unsigned RB4:1;
          unsigned RB5:1;
          unsigned RB6:1;
          unsigned RB7:1;
     } bits;
  } portb;
  portb.bits.RB0 = 1;
  portb.bits.RB1 = 1;
  PORTB = portb.byte;
  ```

  З тіньовим регістром, до фактичного порту запис робиться разом усіма восьма бітами (ніякі інструкції BSF не використовуються), тому немає питання RMW.

* Можна вставити затримку між записами до кожного виводу порту. Це дає час порту для досягнення необхідного стану так, щоб його стан що був зчитаним правильно при наступній команді BSF. Проблема з цим підходом в тому, що це не тривіально, щоб визначити, скільки затримки необхідно, і врахувати зміни що можуть бути внесені в майбутньому до тактової частоти PIC або розводки друкованої плати. Ці зміни можуть несподівано викликати питання знов про RMW.

Розділ 9.10.2  [PICmicro Mid-Range MCU Family Reference Manual](http://ww1.microchip.com/downloads/en/DeviceDoc/31009a.pdf) ([локальне дзеркало](http://www.xargs.com/pic/midrange-io.pdf)) має додаткові обговорення і тимчасові діаграми, які пояснюють, що відбувається в кожному такті мікроконтролера [1].  
Використовуючи вище сказане, створив для себе бібліотеку unionLATC.h:  

```
union {
    unsigned char byte;  // приведення підлеглої структури до типу unsigned char
    struct {
        unsigned RC0:1;
        unsigned RC1:1;
        unsigned RC2:1;
        unsigned RC3:1;
        unsigned RC4:1;
        unsigned RC5:1;
        unsigned RC6:1;
        unsigned RC7:1;
   } bits;
} LATPORTC;
#define LATPORTC_FLUSH PORTC = LATPORTC.byte //макрос для фізичного запису
```

Використовував раніше у проектах XC8 так [2]:  

```
#define LCD      PORTC            // визначення порту для керування LCD
#define LCD_TRIS TRISC            // визначення керуванням порту запис/читання
#define E        PORTCbits.RC3    // визначення біту порту для сигналу E
#define RW       PORTCbits.RC2    // визначення біту порту для сигналу RW
#define RS       PORTCbits.RC1    // визначення біту порту для сигналу RS
#define LCD_LED  PORTCbits.RC0    // визначення біту порту для сигналу кер. освіт.
```

  
Зараз використовую у проектах XC8 так:  
  

```
#include "unionLATC.h"
#define LCD         PORTC             // визначення порту для керування LCD
#define LCD_LAT     LATPORTC.byte     // визначення тіньового порту LAT для керування LCD
```

```
#define LCD_TRIS    TRISC             // визначення керуванням порту запис/читання
#define E_LAT       LATPORTC.bits.RC3 // визн. біту тіньового порту для сигналу E
#define RW_LAT      LATPORTC.bits.RC2 // визн. біту тіньового порту для сигналу RW
#define RS_LAT      LATPORTC.bits.RC1 // визн. біту тіньового порту для сигналу RS
#define LCD_LED_LAT LATPORTC.bits.RC0 // визн. біту тіньового порту для сигн. кер. осв.
#define LCD_LAT_FLUSH LATPORTC_FLUSH  // макрос для фіз. запису тіньового порту LAT

unsigned char temp1,temp2;
temp1=LCD;      // читання даних з реального порту
temp2=LCD_LAT;  // читання з тіньового порту
RW_LAT = 0;     // режим запису до індикатору (тіньовий порт)
RS_LAT = 0;     // 0-команда/1-дані (тіньовий порт)
E_LAT = 1;      // встановлюємо імпульс запису (тіньовий порт)
LCD_LAT_FLUSH;  // РЕАЛЬНИЙ ЗАПИС ДО ПОРТУ С з даних тіньового порту 
__delay_us(1);
E_LAT = 0;      // знімаємо імпульс запису (тіньовий порт)
LCD_LAT_FLUSH;  // РЕАЛЬНИЙ ЗАПИС ДО ПОРТУ С з даних тіньового порту
```

  
P.S.  
Використовуючи готові визначення для портів, можна скоротити запис визначення типу так :  

```
union {
  unsigned char byte;
  PORTCbits_t bits;
} LATPORTC;
```

  
Тому що файл для мого кристалу (pic16f690.h) має такі визначення:  

```
// bitfield definitions
typedef union {
    struct {
        unsigned RA0                    :1;
        unsigned RA1                    :1;
        unsigned RA2                    :1;
        unsigned RA3                    :1;
        unsigned RA4                    :1;
        unsigned RA5                    :1;
    };
} PORTAbits_t;

// bitfield definitions
typedef union {
    struct {
        unsigned                        :4;
        unsigned RB4                    :1;
        unsigned RB5                    :1;
        unsigned RB6                    :1;
        unsigned RB7                    :1;
    };
} PORTBbits_t;

// bitfield definitions
typedef union {
    struct {
        unsigned RC0                    :1;
        unsigned RC1                    :1;
        unsigned RC2                    :1;
        unsigned RC3                    :1;
        unsigned RC4                    :1;
        unsigned RC5                    :1;
        unsigned RC6                    :1;
        unsigned RC7                    :1;
    };
} PORTCbits_t;
```

  
За матеріалами:  

1. [Frequently Asked Questions (FAQ) about C on the Microchip PIC](http://www.xargs.com/pic/c-faq.html)
2. [Библиотека для работы с LCD индикаторами на драйвере HD44780 или KS0066U](http://catcatcat.d-lan.dp.ua/skachat/biblioteki/biblioteka-dlya-rabotyi-s-lcd-indikatorami-na-drayvere-hd44780-ili-ks0066u/)
