---
layout: post
title: "Ввімкнення та аналіз з Алі: RF 433 MHz Remote Control Micro transmitter Module Mini (EV1527)"
date: 2020-10-26 20:00:00 +0000
tags: ["433MHz", "433МГц", "EV1527", "RTL"]
blogger_orig_link: https://lexxai.blogspot.com/2020/10/rf-433-mhz-remote-control-micro.html
---

Придбав на Алі, [модуль трансмітер RF 433 MHz (EV1527)](https://s.click.aliexpress.com/e/_AtrRJX)

[![](/assets/images/blog/76ec6859e99699d3-ee53f9568dc25268.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQOBFgqWDtyfB479xGW0QU-KaYeDeNv2RnJN-DLH5Il_70WMV1ZoXVLMPcqfqluVIVNci39q8NAplbC_AYRcNxHGHhYtNgD5qpViNm8btz-1e05YLljojLO4MBwt7tExN2ykHIXig9RzI9/s4608/IMG_20201026_215155.jpg)  
*RF 433 MHz Remote Control Micro transmitter*

#### Підключення та споживання

[![](/assets/images/blog/08ed3fc2f02c35e4-030468498abfb3cc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEig9kraQyAPUjtNp5jlWfdCIFn_JMmQGVWQvDStLKzCTCBXDsmqSE82e-TIJ3AQxIQCMi17oJI7HVdqugKUvcVBXAtS33EKljJCC_GH1qv5_YUhRyjmoagk5vRm8GZC6ERIKMGLa4VIy6JT/s804/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-10-26+21-53-22.png)  
*Підключення*

[![](/assets/images/blog/33513284d60b7853-d5ce7f8b7cccc0c9.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjNB4vJPqXKv6WjPR91Ll3m3p8nbv0kecrAF4GlsnljZk3_r8lOwL32nFGUE3f_PjFnhEyJfJphjinCjkQWvTFnOYvl_keAPK64Vgt2Dr-x8sfdPUE8TbwQd0-izItqmTRzOwc49St4QRdH/s750/HTB1_f8KT4TpK1RjSZR0q6zEwXXa2.jpg)  
*Споживання за документацією*

  

[![](/assets/images/blog/c21c8c55181c30b0-9e80bee2bf88934b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5AgbP_ZXrXDyXkYetdX-T-5-UcdBfYeKR4KGqDjoENmyE2Ch8JiVlv35iqnnCBC8xNIWITshlwPAjpSpQc5SjfRBfWxTumYRaux3URIsFcd_LTlUrFcOI8dZely2DqWlJ01FHwwiLc8SB/s4608/IMG_20201026_200525.jpg)  
*Споживання 3.7V, 9mA*

#### Аналіз даних через RTL-SDR

[![](/assets/images/blog/768563f56fe6f327-8720b9850b970de8.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFZpehCEf4ubQ0eTX1E1CAnmHrmPFsaZId5GRZpAr7AHVrrPz3Jk7WMNEwNExn9sJDfE_rwO9Rz2AazNB2kQ0WNtWwYg_h30Kq-Jej5tpAbPKEEUaFjjQ7T2j9rCirriYmHW2ETecq89PF/s4161/original_97e2dc96-90c5-420f-8f0f-346087482686_IMG_20201026_200657.jpg)  
*Аналіз даних через RTL-SDR*

rtl\_433  -R30 -vv

```
time      : 2020-10-26 21:45:23  
model     : Generic-Remote                         House Code: 17123  
Command   : 164          Tri-State : Z00X1X01XXZ0  
pulse_demod_pwm(): Generic Remote SC226x EV1527  
bitbuffer:: Number of rows: 1   
[00] {25} 42 e3 a4 80 : 01000010 11100011 10100100 1
```

rtl\_433  -R30 -vvv

```
Pulse data: 25 pulses  
[  0] Pulse:  101, Gap:  275, Period:  376  
[  1] Pulse:  282, Gap:   89, Period:  371  
[  2] Pulse:   98, Gap:  274, Period:  372  
[  3] Pulse:   98, Gap:  273, Period:  371  
[  4] Pulse:   99, Gap:  274, Period:  373  
[  5] Pulse:   98, Gap:  274, Period:  372  
[  6] Pulse:  284, Gap:   88, Period:  372  
[  7] Pulse:   98, Gap:  274, Period:  372  
[  8] Pulse:  285, Gap:   89, Period:  374  
[  9] Pulse:  283, Gap:   88, Period:  371  
[ 10] Pulse:  284, Gap:   90, Period:  374  
[ 11] Pulse:   98, Gap:  273, Period:  371  
[ 12] Pulse:   98, Gap:  273, Period:  371  
[ 13] Pulse:   98, Gap:  276, Period:  374  
[ 14] Pulse:  283, Gap:   88, Period:  371  
[ 15] Pulse:  284, Gap:   88, Period:  372  
[ 16] Pulse:  286, Gap:   89, Period:  375  
[ 17] Pulse:   97, Gap:  272, Period:  369  
[ 18] Pulse:  285, Gap:   89, Period:  374  
[ 19] Pulse:   98, Gap:  274, Period:  372  
[ 20] Pulse:   98, Gap:  273, Period:  371  
[ 21] Pulse:  284, Gap:   89, Period:  373  
[ 22] Pulse:   98, Gap:  273, Period:  371  
[ 23] Pulse:   98, Gap:  274, Period:  372  
[ 24] Pulse:   98, Gap: 2861, Period: 2959
```

Як з'ясувалося отримав модель не таку як замовляв, прийшла без кварцового резонатора, тому частота пливе в залежності від напруги.

[![](/assets/images/blog/537a287f317bb065-0148da69e55fe383.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgaZRJGOb8sS0OtIXb0-RdF7XwOrH21kS4A2c8MCfOoRp5l0QgrlE4v9V7cxut1ED_KWxxEhnnK4tdVkEAg-nKRKKmfJZ8dUP3Vhmbr4p7tGlwsU1CDujw28QeP4rFzbpxBl8Xx96cnB-r9/s659/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-10-27+02-18-23.png)  
*3V, 433.862MHz*

  

[![](/assets/images/blog/e9dc687c7d415882-c8289f51a5b62f33.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwchrqXI87eyV42P2X-IF8uqh85O_Oi5gX_3lzMN358qLcSrCvNL64r78HaZYz1prnn2feSrfWkTfUqKyfO5EGPKqiFpqvFosqOu9SdfmL_TKsPINhbrlBKrx5Irik-Cw1mTbT_FuwFsHk/s660/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-10-27+02-33-45.png)  
*6V, 433.890MHz*

  
  

Продаж тут:

[![](/assets/images/blog/aacb029de5301855-361bb9584090bbcb.jpg)](https://s.click.aliexpress.com/e/_AtrRJX)
