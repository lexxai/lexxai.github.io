---
layout: post
title: "Optimizing Performance: Python Speed Test for Digit Search in Strings. Shot, Middle, Long ..."
date: 2024-11-24 23:07:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2024/11/optimizing-performance-python-speed_25.html
---

### Оптимізація продуктивності: Тест швидкості Python для пошуку цифр у рядках

⛓️ У відповідь на оригінальний пост: [читайте обговорення тут](https://lnkd.in/e56KKsMp) я раніше [провів заміри тільки для однієї категорії - короткий текст](/2024/11/optimizing-performance-python-speed.html), тепер продовження ...

🪜 Я провів експеримент для оцінки ефективності різних методів Python для пошуку цифр у рядках, з урахуванням різних довжин рядків від коротких до довгих. Нижче я порівнюю шість різних технік і їхні переваги при витягуванні цифр.

А також додано вимірювання продуктивності в різних версіях Python: 3.13.0 та 3.12.5

## Категорії для тестування

Рядки були поділені на чотири окремі групи за їхньою довжиною:

* **Short**: Короткий рядок, що містить лише кілька символів.
* **Middle**: Рядок середнього розміру, приблизно 256 символів.
* **Long (1KB)**: Довший рядок, приблизно 1 кілобайт, що імітує параграфи тексту.
* **Long (2KB)**: Значно більший рядок, приблизно 2 кілобайти, підходить для даних, схожих на документи.

## Методи порівняння

Нижче наведено шість версій методів витягування цифр, кожен з яких було проаналізовано за показниками продуктивності:

[![](/assets/images/blog/1d201cacc5165f43-bea6595ab5e01542.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3hbGH7TnnDzHD70izsqosTyDki7MjtOdk6tNNuw-pYPRLXZyN-VjlZJMXrzc6VkRev53oIOhkbvkp9CsNcf03Rs8N-9KCIzisy3cfXn8a-pIQKwIicUWmP-S2kuHv1AcUzF5ypoTOHoaBdtFXciGnMlgCuK0_xmzqwAtDQx02sS_Z1V0aYpF1hvwzcerM/s1238/code.png)  
*Шість версій для порівняння*

### 1. **Version 1: Manual Iteration with List and `isdigit()`**

* **Опис**: Ітерує через кожен символ рядка, перевіряє, чи є це цифрою за допомогою `char.isdigit()` і додає його до списку.
* **Продуктивність** **(3.13.0)**:
  + **Short**: 3.37 секунд
  + **Middle**: 1.12 хвилин
  + **Long (1KB)**: 4.13 хвилин
  + **Long (2KB)**: 9.71 хвилин

### 2. **Version 2: List Comprehension with `isdigit()`**

* **Опис**: Використовує list comprehension для фільтрації цифр, створюючи список цифр за одну лінію з використанням `char.isdigit()`.
* **Продуктивність (3.13.0)**:
  + **Short**: 3.23 секунд
  + **Middle**: 1.04 хвилин
  + **Long (1KB)**: 3.89 хвилин
  + **Long (2KB)**: 9.24 хвилин

### 3. **Version 3: `filter` with `str.isdigit`**

* **Опис**: Використовує функцію `filter` з `str.isdigit`, яка безпосередньо фільтрує нецифрові символи і повертає відфільтрований ітератор.
* **Продуктивність** **(3.13.0)**:
  + **Short**: **2.86 секунд** (Найшвидший)
  + **Middle**: **36.56 секунд** (Найшвидший)
  + **Long (1KB)**: **2.31 хвилин** (Найшвидший)
  + **Long (2KB)**: **5.39 хвилин** (Найшвидший)

### 4. **Version 4: Regular Expression with `re.findall`**

* **Опис**: Використовує регулярний вираз (`\d+`) для пошуку всіх послідовностей цифр у рядку, повертаючи список знайдених збігів.
* **Продуктивність** **(3.13.0)**:
  + **Short**: 4.42 секунд
  + **Middle**: 47.71 секунд
  + **Long (1KB)**: 2.83 хвилин
  + **Long (2KB)**: 6.49 хвилин

### 5. **Version 5: Precompiled Regular Expression with `re.findall`**

* **Опис**: Використовує попередньо скомпільований шаблон регулярного виразу (`re.compile`) для повторних пошуків цифр з `re.findall`, орієнтуючись на швидше виконання з компільованими патернами.
* **Продуктивність** **(3.13.0)**:
  + **Short**: 7.13 секунд
  + **Middle**: 50.33 секунд
  + **Long (1KB)**: 2.87 хвилин
  + **Long (2KB)**: 6.53 хвилин

### 6. **Version 6: Precompiled Regex with `.findall` Method**

* **Опис**: Використовує попередньо скомпільований шаблон регулярного виразу (`re.compile`) і метод `.findall()` безпосередньо на рядку для витягування всіх послідовностей цифр.
* **Продуктивність** **(3.13.0)**:
  + **Short**: 2.93 секунд
  + **Middle**: 45.87 секунд
  + **Long (1KB)**: 2.79 хвилин
  + **Long (2KB)**: 6.45 хвилин

## Фінальна рекомендація

* **Найкращий метод**: **Version 3** (`filter` з `str.isdigit`) — безсумнівний лідер серед усіх методів.
* **Чому**: Він простий, читабельний і показує відмінні результати як для малих, так і для великих наборів даних.

## Результати

```
% python main.py
Darwin_3.13.0, times=5000000, repeat=3

Category: 'short'
 - Version 1. Time: ['3.4180', '3.3468', '3.3461'] avg: 3.3703 seconds. Result: 2024
 - Version 2. Time: ['3.2680', '3.1936', '3.2276'] avg: 3.2297 seconds. Result: 2024
 - Version 3. Time: ['2.8397', '2.8754', '2.8645'] avg: 2.8599 seconds. Result: 2024
 - Version 4. Time: ['4.3639', '4.4486', '4.4417'] avg: 4.4181 seconds. Result: 2024
 - Version 5. Time: ['7.0495', '7.1414', '7.1971'] avg: 7.1293 seconds. Result: 2024
 - Version 6. Time: ['2.9280', '2.9187', '2.9319'] avg: 2.9262 seconds. Result: 2024

Category: 'middle-256'
 - Version 1. Time: ['67.3783', '67.6979', '67.0671'] avg: 67.3811 seconds. Result: 202420240220240001202406151372024091043215672024122233
 - Version 2. Time: ['62.9530', '62.5314', '62.0876'] avg: 62.5240 seconds. Result: 202420240220240001202406151372024091043215672024122233
 - Version 3. Time: ['36.7012', '36.5057', '36.4587'] avg: 36.5552 seconds. Result: 202420240220240001202406151372024091043215672024122233
 - Version 4. Time: ['47.7360', '47.7376', '47.6681'] avg: 47.7139 seconds. Result: 202420240220240001202406151372024091043215672024122233
 - Version 5. Time: ['50.3733', '50.3510', '50.2714'] avg: 50.3319 seconds. Result: 202420240220240001202406151372024091043215672024122233
 - Version 6. Time: ['45.8516', '45.9222', '45.8223'] avg: 45.8654 seconds. Result: 202420240220240001202406151372024091043215672024122233

Category: 'long-1k'
 - Version 1. Time: ['247.6524', '247.7653', '247.7344'] avg: 247.7174 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251
 - Version 2. Time: ['232.9952', '233.0520', '233.8286'] avg: 233.2919 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251
 - Version 3. Time: ['138.3953', '138.7282', '138.7134'] avg: 138.6123 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251
 - Version 4. Time: ['170.0998', '170.4189', '169.5901'] avg: 170.0362 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251
 - Version 5. Time: ['172.2324', '172.1944', '172.1929'] avg: 172.2066 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251
 - Version 6. Time: ['167.4107', '167.2363', '167.1903'] avg: 167.2791 seconds. Result: 202420240101202412312024021520240310110012024202401202024202406302565678202420240705303506020240915789001232024080120241031202411052024121541252024998020242024123020251

Category: 'long-2k'
 - Version 1. Time: ['582.2415', '582.9876', '582.5370'] avg: 582.5887 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
 - Version 2. Time: ['553.1481', '554.6678', '554.9689'] avg: 554.2616 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
 - Version 3. Time: ['323.2028', '323.5094', '323.4390'] avg: 323.3837 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
 - Version 4. Time: ['390.0178', '388.7353', '389.5589'] avg: 389.4373 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
 - Version 5. Time: ['391.7042', '391.4870', '392.1666'] avg: 391.7859 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
 - Version 6. Time: ['386.7376', '387.3923', '387.0871'] avg: 387.0723 seconds. Result: 202420242024010510012024020120240331202404151002244202405202024061015334562024070178912345567899882024080120240915202430352024100578902002024092520241015202410202108765202411012024112099013456202420241125654343212024121030020241220202501312025021020252024
```

### Diagrams **(3.13.0)**

Категорії: 

[![](/assets/images/blog/05d5eea00f7260b0-50df6587b072a00c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPywHcltjrMDrKe5Tjn9Guvdoqhno4g3OejxiJr9IB_IafgrNUWhCwNHwv1zFU2u644hPP2DK_4cfjirbprKyl-j1EamHvfVvpMxe9JkgZXya1gzHWgQW-2GWXqn15g9un1O6BGqtMVQA9CbphUdQNVxhZLyAZ0rs86Fx994iciukL-HqJ9-aHAt1b4lim/s3600/performance_comparison_short.png)  
*Short*

[![](/assets/images/blog/5dc9990a955987af-5eb7ecbaebe80dbd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7aL3oJ4vFJczgFVmnTd0eBRST_IR3Dlw8sse8nMxgH-oe8lf_u5A1aDjTYP5ZZr6ecAgGB0Oakpw9unIlpwfdkgYT58Q_PWE7rc46QUMHayP1I8I0bPS0RF5kN0HgXqTlbUpFA4w96nsyoHNtsaKX-Ts2BMeaLPlISl6gODYIEGIPa7OfwN23IY0oCmCo/s3600/performance_comparison_middle-256.png)  
*Middle 256*

[![](/assets/images/blog/3827a8e8a1b5148d-9ae611a8b8f716aa.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcWK3YxhMnS2kyyOPem4PhUysr1y2PXa1LEC_EdYznd8VHbNrKVd8lsg2k-r5zjPP_cRty3iCpmyCHaYaoGj6t2CqGSCJoIVvEleH-Fr54_rW9jkl7pJm984rSUuXC0CdoHcc5SIGETIvDy_WFBh3SZaQW4sA7NpMb8p-jmFPUxjcWuAvPERs2zQT9zs6R/s3600/performance_comparison_long-1k.png)  
*Long 1K*

[![](/assets/images/blog/d5a644e9ee7faef8-304bccb9dd1c3d52.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIiybEAcqYECPEC86quC5QF4hJvumAt_tiatJkGdgaQjGR5eyR-A3Ja1bTRTHaF3HjY5onU0_0ymD2QDlK3-I4ERs7votR2FRyr6sXG9gOM6wxF9g3qzOL_m9kwAxi-wd0EQH7YH8ezJ_304twJUdWhiMYIOSUXYI_sZPPfFhjB3K2qTo3fzTagQJtKLuQ/s3600/performance_comparison_long-2k.png)  
*Long 2K*

  

[![](/assets/images/blog/6ebc126f32088605-946bd7cd45c2c458.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAoCLqPfjYKTxFqW09nm6y8AyFxuGWE17xQZDni9DBEAbbL7TLUqgpx71_buJy-jOVcJtTB1E2X2JJ5i_cilJNUDHpXDNHuivdjdRs00_CA2t2DtTBXuVYaUp55OHahtybR42uGoPuzfIHy0UDF0Qatvujxb3Ru-dl22m2vKr1V4zlt-PRoy89eAXmr8fN/s3600/performance_comparison.png)  
*Всі категорії*

  

### Diagrams **(3.12.5)**

### *Python 3.12.5*

### Function Duration Metrics in Python 3.13.0

| Function | short | middle-256 | long-1k | long-2k |
| --- | --- | --- | --- | --- |
| Version 1 | 3.37 s | 1.12 m | 4.13 m | 9.71 m |
| Version 2 | 3.23 s | 1.04 m | 3.89 m | 9.24 m |
| Version 3 | **2.86 s** | **36.56 s** | **2.31 m** | **5.39 m** |
| Version 4 | 4.42 s | 47.71 s | 2.83 m | 6.49 m |
| Version 5 | 7.13 s | 50.33 s | 2.87 m | 6.53 m |
| Version 6 | 2.93 s | 45.87 s | 2.79 m | 6.45 m |

### 

### Function Duration Metrics in Python 3.12.5

| Function | short | middle-256 | long-1k | long-2k |
| --- | --- | --- | --- | --- |
| Version 1 | 3.01 s | 51.65 s | 3.22 m | 7.54 m |
| Version 2 | **2.93 s** | 48.15 s | 3.08 m | 7.33 m |
| Version 3 | 3.10 s | **37.44 s** | **2.40 m** | **5.66 m** |
| Version 4 | 4.96 s | 52.88 s | 3.08 m | 7.05 m |
| Version 5 | 7.78 s | 55.65 s | 3.13 m | 7.17 m |
| Version 6 | 3.19 s | 50.61 s | 3.04 m | 7.04 m |

### 

### CSV of duration differences (%) for Python 3.13.0 and 3.12.5

| Function | short | middle-256 | long-1k | long-2k |
| --- | --- | --- | --- | --- |
| Version 1 | 11.96% | 29.44% | 28.21% | 28.78% |
| Version 2 | 9.27% | 30.79% | 26.60% | 26.46% |
| Version 3 | **-7.74%** | 2.36% | **-3.75%** | **-4.77%** |
| Version 4 | **-10.89%** | 9.78% | 8.12% | 7.95% |
| Version 5 | **-8.36%** | 9.56% | 8.30% | 8.93% |
| Version 6 | **-8.15%** | 9.33% | 8.22% | 8.39% |

### 

### 

### Посилання на проєкт:

* <https://github.com/lexxai/compare_perfomance_isdigit>
