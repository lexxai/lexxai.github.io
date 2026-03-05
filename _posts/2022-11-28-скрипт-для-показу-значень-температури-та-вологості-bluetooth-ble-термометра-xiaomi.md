---
layout: post
title: "Скрипт для показу значень температури та вологості Bluetooth BLE термометра Xiaomi"
date: 2022-11-28 01:01:00 +0000
tags: ["Bluetooth", "firmware", "humidity", "python", "script", "Temperature", "Xiaomi"]
blogger_orig_link: https://lexxai.blogspot.com/2022/11/bluetooth-ble-xiaomi.html
---

Є Bluetooth BLE термометр Xiaomi Mijia (LYWSD03MMC) з альтернативною прошивкою [PVVX](https://github.com/pvvx/ATC_MiThermometer).

[![](/assets/images/blog/8991d1fcc2ff818e-27c3575298ea7b28.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgX0jImi11ZePa1tdBiGtNZmNlEbjFdrZdhBIm8rV-1nNspbg-pZsWKMrA2WLonxTowseR5mj6DEEQmBwrlMHGgNdg3XL58dtiqxVivvSe6awiHSbB3lPO1J--rqYAbV5g2752TGZzEuASVCUeVVQ7b6iUzLSDIf9UjFXI_04C09Ey3Act4TXd_sMk-Zw/s550/68747470733a2f2f7461736d6f74612e6769746875622e696f2f646f63732f5f6d656469612f626c7565746f6f74682f4c5957534430334d4d432e706e67.png)  
*Xiaomi Mijia (LYWSD03MMC)*

Написаний простий скрипт (Python) що сканує мережу [Bluetooth BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) на наявність broadcasting advertising packets.

Якщо назва пристрою починається з 'ATC' то виводяться декодовані данні телеметрії в консоль.

#### Код

```
import asyncio
import datetime
from bleak import BleakScanner

#MiTermometerPVVX"

async def main():
    print("Scanning BLE devices of type 'ATC_MiThermometer (PVVX)', please wait...")
    stop_event = asyncio.Event()
    ATC_COUNTERS={}
    ATC_DATE={}

    def callback(_device, advertising_data):
        name=advertising_data.local_name
        if name and name[0:3]=="ATC" :
            advatc=advertising_data.service_data['0000181a-0000-1000-8000-00805f9b34fb']
            count=int.from_bytes(advatc[13:14], byteorder='little', signed=False) 
            if ATC_COUNTERS.get(name) != count :
                ATC_COUNTERS.update({name: count}) 
                datenow=datetime.datetime.now()
                dateprev=ATC_DATE.get(name)
                if dateprev:
                    datediff=datenow-dateprev
                    datediff=datetime.timedelta(seconds=round(datediff.total_seconds()))
                else:
                    datediff=0
                ATC_DATE.update({name: datenow}) 
                temp=int.from_bytes(advatc[6:8], byteorder='little', signed=True)/100.0
                humidity=int.from_bytes(advatc[8:10], byteorder='little', signed=True)/100.0
                batteryv=int.from_bytes(advatc[10:12], byteorder='little', signed=False)
                battery=int.from_bytes(advatc[12:13], byteorder='little', signed=False)    
                flag=int.from_bytes(advatc[14:15], byteorder='little', signed=False) 
                print()
                print("device:\t ", name)
                namelen=len(str(name))+10
                print("-" * namelen)
                print(f"temp:\t  {temp}\xB0C")
                print(f'humidity: {humidity}%')
                print(f'batteryv: {batteryv} mV')
                print(f'battery:  {battery}%')
                print(f'count:\t  {count}')
                if datediff:
                    datedifftext=', duration: ' + str(datediff)
                else:
                    datedifftext=""
                print(f'time now: {datenow.strftime("%H:%M:%S")}{datedifftext}')

    try:
        async with BleakScanner(callback) as scanner:
            await stop_event.wait()
    except asyncio.CancelledError as ex:
        print('**** task scanner cancelled')
        stop_event.set()


if __name__ == '__main__':
    try:
        asyncio.run(main())
    except Exception as e:
        print(str(e))
```

<https://github.com/lexxai/Show_temperature_from_BLE_ADV_ATC_MiThermometer>

[![](/assets/images/blog/1dfe767cc5b35517-a7aebe05012b65e7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0sqjrpD60B3LCO7bj_Jp3bCYyhLk5GSVTcj6KElsVBVJRZ7awtrgAngcSpZ4ZeXEyRZN1gHHSrPOW7Zt63NnV2c-RbT5mUDeiOeQXhH2GEHIEsOVEg12XIDQb61yi0OOCJEeqXX5ru6zQs6xSNgMbz56-T3etRujYXHY82-UOLLxNiHVbdOl-CoIPqw/s897/204151276-c43508b4-945a-4859-8740-efbf5d425674.png)  
*Результат роботи MiTermometerPVVX*

#### Custom format (all data little-endian):

UUID 0x181A - size 19: Custom extended format in 0.01 units (all data little-endian):

```
uint8_t     MAC[6]; // [0] - lo, .. [6] - hi digits
int16_t     temperature;    // x 0.01 degree
uint16_t    humidity;       // x 0.01 %
uint16_t    battery_mv;     // mV
uint8_t     battery_level;  // 0..100 %
uint8_t     counter;        // measurement count
uint8_t     flags;  // GPIO_TRG pin (marking "reset" on circuit board) flags: 
                    // bit0: Reed Switch, input
                    // bit1: GPIO_TRG pin output value (pull Up/Down)
                    // bit2: Output GPIO_TRG pin is controlled according to the set parameters
                    // bit3: Temperature trigger event
                    // bit4: Humidity trigger event
```
