---
layout: post
title: "Програмування мікроконтролерів. PIC12F675. Timer1,Sleep,Clock,RS232"
date: 2014-01-12 23:41:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/01/pic12f675-timer1-sleep.html
---

[![](/assets/images/blog/ea84ef518b78d304-9c9484d6294094c0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhF4pChvQeiG7Dy4xiqxhC7qgiYv5iYAQ4Iuh-hzfWYFeKGdTEd2pVLMzqWhT70j2BknKJ7_uE2tPfy49R5JSdBMn2TUtddS7e0NFtmrRPojvwzD-dVjyO7az6F-2Dv20aJyYGTQMYwTT8b/s1600/28080-img_3541.JPG)

Вивчаю програмування мікроконтролерів PIC. Маю програматор PICkit 2 та макетну плату.  
Вивчаю крок за корком. Почав від класичних завдань з світлодіодом.  
Мигання світлодіоду класично робиться за допомогою внутрішньої затримки пустими циклами NOP.  
Пройшовши ці завдання, я став оптимізувати і заодно вивчати роботу з таймерами та перериваннями.  
  
Так у моєму розпорядку є мікроконтролер [PIC12F675](http://www.microchip.com/wwwproducts/Devices.aspx?dDocName=en010114).   
Його технічні дані:  
  
CMOS Flash-based 8-bit microcontroller packs Microchip’s powerful PIC® MCU architecture into an 8-pin package and features 4 channels for the10-bit Analog-to-Digital (A/D) converter, 1 channel comparator and 128 bytes of EEPROM data memory.  
Program Memory Type : Flash   
Program Memory (KB) : 1.75   
CPU Speed (MIPS)  : 5   
RAM Bytes : 64   
Data EEPROM (bytes) :128   
Timers : 1 x 8-bit, 1 x 16-bit   
ADC : 4 ch, 10-bit   
Comparators : 1   
Operating Voltage Range (V) : 2 to 5.5   
Pin Count : 8   
Cap Touch Channels : 4   
  
Спочатку я вивчав роботу з 8 бітним таймером Timer0. Надалі стало цікаво використання режиму мікроконтролера "сон" замість пустих циклів очікування. Але Timer0 не може виводити контролер з режиму "сон". А ось Timer1 може виводити контролер зі сну якщо використовується Timer1 у спеціальному асинхронному режимі ([DS41190G](http://ww1.microchip.com/downloads/en/DeviceDoc/41190G.pdf)-стор. 32).  
Ось моя тестова схема:  

[![](/assets/images/blog/835e959afedb713a-b7f9e281457ec652.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgURhFEAOZGXwtd9s5zyR1Kr2TJy61nhXWd0fShhiQh5Js6WWY-NnBwM5xuWrCTTLpG1cb4i4Lzsg1HDl0FSeXrYnBgp2ejz_IbxZ6cT4wazNaiz6k1U_hYxSYm-StuOn6-kUKlF3dMBFdq/s1600/timer1-sleep.png)  
*LED controlled by PIC12F675, Timer1, External Clock, Wake-up.*

 У цій схемі використовується зовнішній кварцовий резонатор на основі "часового" резонатору на частоту 32768 Hz. Він потрібен тільки для роботи таймера Timer1 у асинхронному режимі. Сам мікроконтролер працює на внутрішньому генераторі з частотою 4MHz , режим INTOSC w/o CLKOUT (FOSC: 100). Для керування світлодіоду використовується порт GP2 у режимі відкритого колектору (output swing).  Для аварійного живлення контролера у режимі "сон", використано літієву батарею на 3V CR2032 котра відокремлена від усієї схеми двома діодами, так у аварійному режимі світлодіод не спалахує, але таймер працює.  
Для розуміння  роботи таймера Timer1 у асинхронному режимі дуже згодитися наведена діаграма.  

[![](/assets/images/blog/b2c0913f61097ede-41d3b71e990640b9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinAyxf_g6h-Rh4esB3zH4lZ154uAVubk7jX7z6nuf4SOhR4MSz5Uj5tz4KiBo40vBEPXIsy8OZMHDGVh7SokFG1xw3Df-LeN2lUrk3R0cl0MMlNhzQj6Iwo81XEyTLGueWORC6gRVu5ne5/s1600/timer1-sleep-01.png)  
*TIMER1 BLOCK DIAGRAM*

Так таймер Timer1, це 16 розрядний таймер зі збільшенням свого значення кожного вхідного такту таймера. Максимальне його зазначення є 0xFFFF (65536), після переходу значення таймера (TMR1 [TMR1L,TMR1H]) з 0xFFFF до 0х0000, може бути знервовано апаратне переривання і виставлений біт (PIR1bits.TMR1IF).  
Вхідний такт таймера визначається внутрішню програмного логікою.  
У даному застосуванні використовується шлях логіки виділений жовтим кольором на діаграмі.    
Основою є зовнішній LP (Low Power Crystal) генератор що генерує частоту 32768 Hz, на основі кварцового резонатора підключеного до входів OSC1 та OSC2. Для дозволу його використовувати треба встановити біт T1CONbits.T1OSCEN=1. Далі частота генератора може бути поділена за допомогою модуля Prescaler на 1,2,4 або 8 (OPTION\_REG.PS).  
Обов'язковою умовою є виключення блоку синхронізації з основним тактовим генератором, так як у режимі сну він не працює, (T1CONbits.nT1SYNC=1). І для того щоб працював таймер потрібно його роботу дозволити  за допомогою T1CONbits.TMR1ON=1.  
Після цього таймер буде справно генерувати переривання з періодом визначений формулою:    

Tint=1/(LP\_FREQ/TMR1\_PRESCALER/65536) c.

Де LP\_FREQ=32768 Hz, TMR1\_PRESCALER- коефіцієнт модуля Prescaler.  
Як що prescaler буде 1, то максимальний період переривання буде 2с. При максимальному значенні prescale=8, період переривання відповідно буде 16с.  
  
Так якщо будувати схему часового годинника, то за такою реалізацією часи будуть від ображати час не кожну секунду, як за зазвичай, а кожні 2сек або 4,8,16сек відповідно від значення коефіцієнту prescale.   
Для того щоб зменшити мінімальний період генерування переривання, наприклад до класичної однієї секунди, треба коригувати початкові значення таймера, після кожного спрацювання переривання від таймера, використовуючи формулу:  

TMR1=(0xFFFF-((LP\_FREQ / TMR1\_PRESCALER) \* TMR1\_TIME))

Де LP\_FREQ=32768 Hz, TMR1\_PRESCALER - коефіцієнт модуля Prescaler, TMR1\_TIME - бажаний період у секундах.  
Для значення  TMR1\_TIME=1с., TMR1\_PRESCALER=1. TMR1 повинен бути 0x7FFF. І відповідно старша і молодша частина TMR1 є TMR1H=0x7F, TMR1L=0xFF.  
  
Додатково додано виведення результату через емульований віхід RS232 ([дякуючи іншим авторам](http://www.romanblack.com/bitbangserial.htm))  

```
#define TxPin          GPIObits.GP0   // Output Tx
#define RxPin          GPIObits.GP1   // Input  Rx
```

Результат виводу можна ,бачити через інструмент програматора PICkit 2 - UART Tool.   

[![](/assets/images/blog/110561b435228011-b6672275538ae800.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsxlJqVjujndOsippEyVxmPnPgKerYAHcHwM_C3MUIajLFLCAqldjqODXya9qXq4Js7cJ3NBoYyj1-lfdB7TNZLezjhJv-8BGK1BRrvuY04NuG0OJwVP5UFZsGXds-1yxN-DQ4eGnFlo_e/s1600/timer1-sleep-02.png)  
*PICkit 2 - UART Tool, результат годиника*

Ось програма на мові програмування С  (XC8) що реалізує цей алгоритм:   
Переривання через 4 sec, таймер має повний цикл 65536. Таким чином переповнення таймера проходить без втручання програми, що повинно зменшити похибку роботи таймера.   
Можуть використовуватись інші значення з втручанням у значення таймера, тому можливо необхідно вводити корегувальні коефіцієнти на той час котрий затрачено для запису нових значень таймера (2 по 2 такти).   
MOVLW, k // (1 cycle);  
MOVWF, f // (1 cycle);  
  

```
# lexxai,(2014), http://www.lexxai.pp.ua
```

```
#if defined(__XC)
#include <xc.h>         /* XC8 General Include File */
#pragma config FOSC = INTRCIO   // Oscillator Selection bits (INTOSC oscillator: I/O
#pragma config WDTE = OFF       // Watchdog Timer Enable bit (WDT disabled)
#pragma config PWRTE = OFF      // Power-Up Timer Enable bit (PWRT disabled)
#pragma config MCLRE = OFF      // GP3/MCLR pin function select (GP3/MCLR pin
#pragma config BOREN = OFF      // Brown-out Detect Enable bit (BOD disabled)
#pragma config CP = OFF         // Code Protection bit (disabled)
#pragma config CPD = OFF        // Data Code Protection bit (disabled)
#elif defined(HI_TECH_C)

#include <htc.h>        /* HiTech General Include File */
__CONFIG(FOSC_EXTRCCLK & WDTE_OFF & PWRTE_OFF & MCLRE_ON & BOREN_OFF & CP_OFF & CPD_OFF);
#endif

/* Microcontroller MIPs (FCY) */
#define SYS_FREQ        400000L
#define FCY             SYS_FREQ/4

#define _XTAL_FREQ  4000000   // for delay

/* For RS232 console output*/

#define BAUDRATE    9600 // Desierd BAUD Rate (tested) Rates 9600 and 19200
#define SER_BAUD    ((_XTAL_FREQ / 4) / BAUDRATE - 3)
#define SER_BIT     1    // Signal MODE - 1 = Normal 0 = Inverted (Use Inverted for direct 232)

// I/O Pins
//
#define TxPin          GPIObits.GP0   // Output Tx
#define RxPin          GPIObits.GP1   // Input  Rx
#define LedPin         GPIObits.GP2   // LED

// Precalculate values of Timer 1 by macros
#define _LP_FREQ  32768 // Hz
#define _TMR1_PRESCALER  2  // 1:8
#define _TMR1_TIME  4  // sec
#define _TMR1W  (0xFFFF-((_LP_FREQ / _TMR1_PRESCALER) * _TMR1_TIME))
#define _TMR1H  _TMR1W >> 8
#define _TMR1L  _TMR1W & 0x00FF

#include <stdint.h>        /* For uint8_t definition */
/******************************************************************************/
/* Main Program                                                               */
/******************************************************************************/
//
//    User Functions
//

/* <Initialize variables in user.h and insert code for user algorithms.> */
void InitApp(void) {
    //   Setup - Run once
    //   Default I/O Setup
    ANSEL = 0; // Disable Analog
    CMCON = 0b00000111; // Comparator OFF
    GPIO = 0; // Turn all Outputs OFF
    TMR1CS = 1; // off internal FOSC/4
    __delay_ms(1000); // delay for correct starting LP
    T1CONbits.T1OSCEN = 1;   //LP use
    T1CONbits.T1CKPS = 0b01; //prescaler TIMER1 1:2
    T1CONbits.TMR1ON = 0;    //disable Timer1
    T1CONbits.nT1SYNC = 1;
    PIE1bits.TMR1IE = 1;
    INTCONbits.PEIE = 1;
    // TxPin          GPIObits.GP0   // Output Tx
    // RxPin          GPIObits.GP1   // Input  Rx
    // LedPin         GPIObits.GP2   // Output LED
    TRISIO = 0b000010; // INPUTS: GP1 = RxPin
    LedPin = 1; // Initial value of LED
    ei(); // Enable Interrupts
    T1CONbits.TMR1ON = 1; // Enable Timer1
}

//change LED state

void outLED() {
    LedPin = !LedPin;
}

//parse hex byte value to decimal view

unsigned char parseHex(unsigned char i) {
    unsigned char buf, digit1, digit10;
    buf = 0;
    digit10 = i / 10;
    digit1 = i - digit10 * 10;
    buf = digit10 << 4;
    buf |= digit1;
    return buf;
}

// out byte via RS232 simulated interface

void send_serial_byte(unsigned char data) {
    unsigned char i;
    i = 8; // 8 data bits to send
    TxPin = !SER_BIT; // make start bit
    TMR0 = (256 - SER_BAUD); // load TMR0 value for first baud;
    while (TMR0 & (1 << 7)); // wait for baud
    while (i) // send 8 serial bits, LSB first
    {
        if (data & 1 << 0)TxPin = SER_BIT; // send data bit
        else TxPin = !SER_BIT;
        data = (data >> 1); // rotate right to get next bit
        i--;
        TMR0 -= SER_BAUD; // load corrected baud value
        while (TMR0 & 1 << 7); // wait for baud
    }
    TxPin = SER_BIT; // make stop bit
    TMR0 -= SER_BAUD; // wait a couple of baud for safety
    while (TMR0 & 1 << 7);
    TMR0 -= SER_BAUD;
    while (TMR0 & 1 << 7);
}

//initial value of shared values of clock
static uint8_t sec = 0;
static uint8_t min = 30;
static uint8_t hours = 1;


//interrupt

void interrupt isr(void) {
    if (PIR1bits.TMR1IF) {
        //only if pre calculated value of Timer1 need to correct, then define next
#if _TMR1W>0
        TMR1L = _TMR1L;
        TMR1H = _TMR1H;
#endif
        PIR1bits.TMR1IF = 0;
        // CLOCK LOGIC
        if ((sec += _TMR1_TIME) >= 60) {
            sec = 0;
            if (++min == 60) {
                min = 0;
                if (++hours == 24) {
                    hours = 0;
                    min = 0;
                }
            }
        }
        outLED(); //short blink start
        //out value of clock via RS232
        send_serial_byte(parseHex(hours));
        send_serial_byte(parseHex(min));
        send_serial_byte(parseHex(sec));
        outLED(); //short blink stop
    }
}

/* MAIN */

void main(void) {
    /* Initialize I/O and Peripherals for application */
    InitApp();
    while (1) {
        SLEEP(); //almost all time we sleeping
    }
}
```

```
 
```

```
 
```

```
P.S.
```

```
 
```

[![](/assets/images/blog/ec7abbf02082ca74-7f77c19e63e0e3dc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEheZO1ad27YF8zE3VOTftSXIAEmRMa2r6Ny8963q9q6iJB-tS1qd8HYgAL-GfaDh9UIrgLFGXkxPJK9X2t0iYHTlhbWxqI59q6TzPqgohM-X24sUcnaLYZ_kLOM9EOTi-AW2QJmXt_0sFfr/s1600/pic-timer-soft-uart-01.PNG)  
*Керування значень годинка через UART.*

```
 
```

```
 
```

lexxai,(2014), http://www.lexxai.pp.ua
