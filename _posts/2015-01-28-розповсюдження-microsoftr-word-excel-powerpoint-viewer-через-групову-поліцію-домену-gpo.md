---
layout: post
title: "Розповсюдження Microsoftr Word, Excel, PowerPoint Viewer через групову поліцію домену GPO"
date: 2015-01-28 13:29:00 +0000
tags: ["adimistration", "deploy GPO", "LibreOffice", "Microsoft office", "MSI", "Viewer"]
blogger_orig_link: https://lexxai.blogspot.com/2015/01/microsoftr-word-excel-powerpoint-viewer.html
---

[![](/assets/images/blog/00958ae8db705110-ecdeda2cbcbf4c01.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiY1eH5yYAAiqosugk63yzkWz6FymA1jKBdIY_TeAPHnRiJP_tblbKd_o3zj0dK7Q89JoCcehiihXLO2SmeEDJd-C-XVj9ezBA_3XQUZXz3soO8vkh9_7BC7uWIvMMgmDpXULlLv6Rsa1MZ/s1600/msicon.PNG)

Якщо необхідно встановити на багатьох комп'ютерах програми для офісної роботи з безкоштовною ліцензією, то потрібно встановлювати через групову поліцію домену GPO. Для цього встановлюємо LibreOffice після певної модифікації msi з скороченням довжини поступних мов.  
І наступною чергою для сумісності з файлами Microsoft Office встановлюємо Microsoftr Word, Excel, PowerPoint Viewer що дозволяють тільки переглядати файли Microsoft Office.  
  
Але якщо завантажити з сайту Microsoft переглядачі:  

* Засіб перегляду [Word Viewer](http://download.microsoft.com/download/2/b/d/2bd08f27-822b-4c20-936a-0f1d91cb9e69/wordview_uk-ua.exe)
* Засіб перегляду [Excel Viewer](http://download.microsoft.com/download/7/4/9/749A8D59-A2D5-4A64-99CF-5FF4DD7C5AC6/ExcelViewer.exe)
* Засіб перегляду [PowerPoint Viewer](http://download.microsoft.com/download/C/E/8/CE879E6E-D3F3-4234-A968-9651A3A85D98/PowerPointViewer.exe)
* Пакет сумісності з форматами файлів [Microsoft Office Word, Excel і PowerPoint](http://download.microsoft.com/download/b/d/4/bd48a7f7-f16a-4d04-9518-5a99062c803b/FileFormatConverters.exe)

То ми отримаємо файли для встановлення .exe, а для розповсюдження через GPO потрібні .msi.  
  
Тому розпакуємо отрманні файли для отримання .msi за допомогою параметра  /extract.  

```
wordview_uk-ua.exe /extract:EXTRACT_WORDVIEW
ExcelViewer.exe /extract:EXTRACT_EXCELVIEW
PowerPointViewer.exe /extract:EXTRACT_POWERPOINTVIEWER
FileFormatConverters.exe /extract:EXTRACT_FFCONV
```

Надалі у отриманих теках    
EXTRACT\_WORDVIEW, EXTRACT\_EXCELVIEW, EXTRACT\_POWERPOINTVIEWER, EXTRACT\_FFCONV отримаємо необхідні нам файли .msi котрі можемо використовувати при розповсюдженні програм через групову поліцію домену GPO.  
  

[![](/assets/images/blog/d52a41617cce5779-ffddd14b5f20046f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizUbG0dJM8ua8RQygfpI0X88nhP_ounEJz22iVn-i_2Oc9PuqrQ757rvqvB9Wx5EFCOLXPdYWs1UZIF-UWuJEmkV0EDIsEKA8f0lSIW2iyZjmkVCr3481NL1o0ygreKytsFY2dc-JkYcCi/s1600/msi-wv.PNG)  
*EXTRACT\_WORDVIEW*

[![](/assets/images/blog/787d50aebf2491fb-b6a95e22e29efda8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDayZMepCBGaxv11gF7EpoQ2ZRN7awke4Ujrck9shbzUNRZmyYMJHxrn7Sv29FlMQdLfN3yEZA5Te_gvJg4HYjE2fY8xfi-62S5nDN7b3enL3y22PsqQl2KX88vUZwuRiKLmrHd8__e1p0/s1600/msi-ev.PNG)  
*EXTRACT\_EXCELVIEW*

[![](/assets/images/blog/b75a60a726ef252c-969e2552d2176155.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0ZNAwMOHp9dyac5fYjeK_NC6D062VfKoO1Dq6mQkPRkwWz1qApUEoK6WAhtDoOSg311JvnadUXy8a2aLhrJS1Qjm1wrWzc-Mx6AWEqlYOTjbYzABQDEQg-v9D56DRpjiavrq1OL16O89y/s1600/msi-pv.PNG)  
*EXTRACT\_POWERPOINTVIEWER*

[![](/assets/images/blog/9e533975f271c7ec-8d7d68f2b7be766d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgepiRThUCIrJwz6WdyQwZfaZ5BKHRiMNRFS8SOxwBraZ3J_5mow7ji4rLasBCiLYfbYXGsWUdogo9PnSJu17sKTgtePXp0S0BMgIglE8jrPQzRG9UZQb0XomjseXY94uJBolzDumYNC8Xs/s1600/msi-fc.PNG)  
*EXTRACT\_FFCONV*

  
  
Подяка: [Deploy Word Viewer with GPO](https://social.technet.microsoft.com/Forums/windowsserver/en-US/8363e8cd-2dad-43a2-8500-442c970926a3/deploy-word-viewer-with-gpo?forum=winserverGP)
