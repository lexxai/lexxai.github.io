---
layout: post
title: "Пульт керування на 433МГц два канали для керування пилосмоком THOMAS TWIN TT AQUAFILTER"
date: 2020-03-19 22:40:00 +0000
tags: ["433МГц", "Thomas", "Пульт керування на", "пилосмок"]
blogger_orig_link: https://lexxai.blogspot.com/2020/03/433-thomas-twin-tt-aquafilter.html
---

Маю старенький пилосмок фірми [THOMAS TWIN TT AQUAFILTER](https://www.thomas.ua/cgi-bin/p.cgi?a=tov&t=1172&to=tvr) з функцією вологого прибирання.  

[![](/assets/images/blog/1d487eb45f4cf150-f25b016f472c9f59.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguf_HEeK096xaSR4c8gQXp6AEU-3WsnKG9I905s8MPIzpud_Azzt6wPR_iNEUXIvLpOuHcPlggtOyPIdoPKLEApq8-6HnJ7quYm0kgiDKZuoR6FtO25j1s6oGsrRodpjzcpqL7dWLSmFYt/s1600/thomas2b.jpg)  
*THOMAS TWIN TT AQUAFILTER*

До нього був пилосмок з функцією керування на ручці, це було дуже зручно. Хоча, тому хто його не мав, важко пояснити як це зручно.  

[![](/assets/images/blog/8b636fd183667484-7c3abb9edc7b9077.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQbLGooKDVPYER8QqgSQkh2B_2GxodVfa4XPKfuCNYIlVAdZOZpUsswHNEN6lmT2fovTiPvIv2oWi13bSRLPtW5bsHcLd-_3-rZFMW40aD6RVdBzS6-cyj7GiPKLUZSSoQee44tdOT5Hh8/s1600/lg-hand.PNG)  
*Керування пилосмоком на ручці*

І стало бажання чому б не доопрацювати функціонал, і зробити псевдо керування на ручці завдяки бездротового пульту з двома каналами.   
  
Для керування пилосмоком фірми [THOMAS TWIN TT AQUAFILTER](https://www.thomas.ua/cgi-bin/p.cgi?a=tov&t=1172&to=tvr) використовується дві кнопки: "Variable Power" і "Pump".  
Кнопка "Variable Power" керує електронним блоком керування двигуном, по принципу:  

* натискання клавіші пилосмоку вмикає/вимикає двигун
* поки натиснута клавіша потужність пилосмоку циклічно плавно збільшується, а потім зменшується, а у максимальних режимах ECO і  MAX блимає світлодіод і змінює колір.

Кнопка "Pump" має режим перемикання вмикає/вимикає водяний компресор.  

#### Дистанційне керування

Я [придбав пульт керування з двома каналами](/2020-03-04-огляд-готовгої-продукції-пульт-на-433мгц-два-канали.md) які можуть замикати і розмикати електронне реле натисканням клавішам "А" і "B" на пульті керування.  

[![](/assets/images/blog/0b88a74b565863e7-521077b86694cbf0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdur5_VTuS_123axeroL4FmhPJV_n3Pp4R-ATlnz5x5e6Z_QloaKLMvYdIKXZ43wvrnajA1ytfE4Z1cQzKGC6RnZbyMQg_FtO6qIIu6bobYC2ATMy1Bw0zDDvyOjHgr1fhprR2Ef88gBAq/s1600/photo_2020-03-04_02-16-40.jpg)  
*пульт на 433МГц два канали*

Пульт керування працює на основі мікросхеми eV 1527,а блок керування на основі мікросхеми qiachip KR2202.  
  

[![](/assets/images/blog/ca26ad12358a42b6-884c463ae5fb4911.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFoYtZCK-PbdayVZkdCldbogG_IDHeW77G24c06rrwaX4sHqOCFOVZUvibGP7bCd9lYi264AZYG-s24axOwMe1LS0v0K_hAlaVTo9uzKaFZ4jYvb_ROveDATEcXSJQ9yEyD9qGQD6mxr9j/s1600/photo_2020-03-04_02-17-22.jpg)  
*Пульт керування*

[![](/assets/images/blog/5bd89aaebb23d596-5866c4e3ad402aeb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDyUoQe155xJo5xvzBpw5kUn8MpEXQSuymPd-baEaDLA8a6pbUrfAuQD0A2VTLCtEGpoPUlyzXyzXYRAeIhnPjFwQVTnb4AdLV9m52Jklnc6jRMQyhi-D4KAHDaRPgUCFbKKAL0ibNvbQv/s1600/photo_2020-03-04_02-17-03.jpg)  
*Блок керування*

Двоканальний пульт керування має декілька програмованих режимів від 1
до 7, у поширених відеоблогах описуються в основному режими 1-3. Для себе я вибрав 4 режим.  

1. моментальний (M) поки тримаєш ввімкнуто, відпустив вимкнуто
2. перемикач - тригер (T) одна і та сама клавіша послідовно вмикає/вимикає.
3. Lock (L) одна кнопка на вмикання інша на вимикання
4. перший канал моментальний (M), другий канал перемикач - тригер (T)

Перший канал (А) буде для кнопки "Variable Power", другий канал (B) буде для кнопки "Pump"  

#### Процедура програмування пульта:

|  |
| --- |
|  |
| Відео: Програмування двоканального бездротового реле на 433MHz у різних режимах для кожного каналу |

#### Додавання електроніки до конструкції пилосмоку

[![](/assets/images/blog/fc582e01159b8635-d02c0bf09c723978.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBs2sIIgffzLu0a6H7LnaLb6WPTtGHdSe6MHFB1qUilyphKygohuQOwfeTp3joFSJ8HIOG4Gtz5OatfHiEOnzRO2Vz6-hgkIHobU6PqGWu_HnXp-3S-fG9UAHqvwKjRRvEHEgSpYRvY5s6/s1600/photo_2020-03-10_00-57-22.jpg)  
*Розбирання корпусу пилосмоку*

[![](/assets/images/blog/9289df67cb57a130-5ddbe7a65a6a73f4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8ZRe_FAAj63dk3nKFmoVA3Ayhu8sq9Bu0jsCVaiMXdq6LL0YpTiSQCy0-h95Q2HPULf0yuPyBiDGqnqdV39VlrNKPgFXayuOprGnL_4EgR5pU67hPr1E6713uJHRtTS_RHLFNO-jykwSP/s1600/photo_2020-03-10_00-57-22+%25282%2529.jpg)  
*Розбирання корпусу пилосмоку, фільтр*

[![](/assets/images/blog/d91f5f2b58fba88a-881d0d7796ee8fa1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgru-HLWfcMNCURXkQ3_RXXyJEdnpLkOev-sw8PnOGc7n_CrZTNPWdSiddPH9tdeBuZDJP5DSgskEKpoibpNcFCDC5n2l41PToFpYzQfunq7RV8-rMab8tk2Mu4H9Oj3_Q4u3eJV8Zh1nc/s1600/photo_2020-03-10_00-57-22+%25283%2529.jpg)  
*Знята кришка пилосмоку*

[![](/assets/images/blog/132566128ee6f6e1-fea3ec8a7307abdc.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjx25IcXyc6lmPGV7jG_rI5Hdt4aBBZSXJlYofnZLARC9NffZTDWhkQoR4JB4pnEgHmmmlvaydjvzNbf-kK8dgbejep7YmnP_OJSiNWPMVbFVPv5As9E2LsqJCXNP4NEw7yCZf4gIinskWT/s1600/photo_2020-03-10_00-57-21.jpg)  
*Плата керування двигуна пилосмоку*

[![](/assets/images/blog/9e8929c98c2245ea-5bd336f0beed0b51.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDx52MYPl3w0GzCfW7pJIIQcS0bjxvKmPhjOMHi79ImrN8Q3yLuqT5vo7Vr9LA1Ql0Us7bBH7qlNyH75cWfwwLDAVgTRKlO8XTS-yw_CI0tjRUI3ki3PVa5I7KFamve4ERTvg7jS4qQkr2/s1600/photo_2020-03-10_00-57-21+%25283%2529.jpg)  
*TriacBTA212-600B, для керування двигуном*

[![](/assets/images/blog/05c4cb9fad3a8dff-53b7ae5e175aeab7.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZspRcllGm1AcIxwTfCUFwXXc6JzgyeCqtsxDt3BeE1dJBlJM1NgyE2UYH9TqlCekWOur08ZAzEHlAH5K_d4KF7pYTbegfWQ1qLUw6IIfTlpDS5mb1F5narwGkvsNM-Wv3XgJXbrbxfjx-/s1600/photo_2020-03-10_00-57-21+%25282%2529.jpg)  
*Плата керування двигуна пилосмоку, контакти підключення реле першого каналу на замикання*

[![](/assets/images/blog/06539258f7435c80-8710d93a3fa8bc94.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGnM4yGLcAf0RnN6GClQu27NnWbZ4Ct6I-Jv0MZDgD85ByuHedfCIB0fbR3TPO9Tmu8sxovoLqFYZTVa8DKRAjcpbJr_Jnrm4tjsAENGLIGCv-e8wNuzRMzxql7p3Q6l8hQiB77ViLdlL0/s1600/photo_2020-03-10_00-57-20+%25282%2529.jpg)  
*Плата керування двигуна пилосмоку, сині проводи для живлення блоку дистанційного керування*

[![](/assets/images/blog/0c78cfa6cc8f05d2-2b1ec6bcee272e74.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmrx88Kwozd6jireikk1bpxfLJ91kitJVgHr8P05NqLDILZ5nwZEPRZgj41K6mdZ6I3AoXirr42S87Ek8pf9kPPTF0RmCR3QdjmewJA5_p-IRyngEhUiR1JT-op1VSESBPdA3LYO5yL0O9/s1600/photo_2020-03-07_20-04-35.jpg)  
*Блок дистанційного керування, підключення*

[![](/assets/images/blog/e7eab4594808d166-f0af9407f9e22fe4.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPvRouyhFBhHaX7ws3THnMHZbB2GIr_24qAxDC1fx-1dkSpsC93fMJP3ZX3PUfwZWjaVP0Mm2tYzRU46DMyuauHEOnWzgm_46k-CADp-o3ePpn0abeTRZEP_LpvV_jlKpGxCCFUdMsjkT6/s1600/photo_2020-03-10_00-57-20.jpg)  
*Блок дистанційного керування, підключення компресора другий канал реле на розмикання*

[![](/assets/images/blog/419ed0d09d6e0f32-41162528a5df971b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCGFmhYcyihxpAAi-vafquYAAhi6nY8eKMqgY0_3DhALnLmMxUtRXgCe-yQjSFnFgiYVlSoxTA-laVDjAnGmOdVYW5dqiLnVro06DTSj8VmHgO4PpXrfPXGJsnsJQ1Ekx6VH2LtyhD0B5k/s1600/photo_2020-03-10_00-57-18.jpg)  
*Блок дистанційного керування, у зборі*

[![](/assets/images/blog/487f70227acb0142-a0c01d3bc7a1ffbd.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhle_uF_EkiDo1NLyn4Ms7QfxjP5oe-vi2RKt2iqYbEoaaQ7GiDpKOlHgct7pt-t0teUhyMvnmBZQFQsRgnL5mFyOw8k7mOJaMPWoin3LBWtxs2eySRxQv-cPiVGokjO9qh_gf0l14FA2yu/s1600/photo_2020-03-10_00-57-18+%25282%2529.jpg)  
*Результат доопрацювання пилосмоку*

|  |
| --- |
|  |
| Відео: доопрацювання пилосмоку THOMAS TWIN TT AQUAFILTER на основі двоканального дистанційного пульту на 433MHz |
