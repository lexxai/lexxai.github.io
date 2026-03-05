---
layout: post
title: "pfSense remove empty password from certificates pkcs12"
date: 2019-02-28 00:08:00 +0000
tags: ["certificate", "FreeBSD", "openssl", "pfsence", "pkcs12"]
blogger_orig_link: https://lexxai.blogspot.com/2019/02/pfsense-remove-empty-password-from.html
---

Коли у [pfSense](https://www.pfsense.org/) [експортуєш сертифікат](https://docs.netgate.com/pfsense/en/latest/certificates/certificate-management.html) користувача у форматі [pkcs12](https://en.wikipedia.org/wiki/PKCS_12), наприклад для використання авторизації [WiFi EAP](https://en.wikipedia.org/wiki/Extensible_Authentication_Protocol) то у він експортується захищеним паролем котрий є пустим.  

[![](/assets/images/blog/085acbda75180154-509e822a1443add6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjehG1JqZxnroSoJUp0Prkqzg89z38eyGttXrEfBKjXl4-NGfIpru4mBHPqQQXTI1LtuyQPQ4UlIVkvZyeJA_lgf1MDbbTRnwDvQVw5765j61z8LB01-0rTAL8DNPIvwQxSf_cWriglvVy-/s1600/conp121.PNG)

  
Але деякі мобільні пристрої не можуть імпортувати сертифікати з таким пустим паролем, він мусить бути.  

[![](/assets/images/blog/fdce3d226e0c4cd8-8eed6b1019e33b94.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgELODwCx0QYuNJ6Ddk-rni3vBNylThS2PtT0PnR9yS_R05irDW-don9vIIYH3TENwos3Cp7NIBFB0K-6idOrHeXn5GA-CrWJ2YBkuHi3JZ9bSOafe5FTQDYGDVP9Qyb_IsP6mBpW9B5UYt/s1600/photo_2019-02-28_02-01-53.jpg)  
*Пароль мусить бути*

  
Тому можна перекодувати .p12 такими командами  
  

```
openssl pkcs12 -in userz.p12 -nodes -out temp.pem
openssl pkcs12 -export  -in temp.pem  -out userz-p.p12
rm temp.pem
```

  
Де:  userz.p12 - вхідний файл, userz-p.p12 - вихідний файл з паролем  
На перший запит пароля вводимо Enter, на інші необхідний новий відомий Вам пароль.   
  

[![](/assets/images/blog/1edd5df400bb3bd4-bf2928e50ee1815f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEBa_HvGrC_bjFe7FETPjW1s1s-i2nYzIBwMu4l4HWU1H56-U_1bufsOyH5ucV1dMAG62J5Z8nQA8OCTQ_MOCLmpCEFCIuC2ijozuOMjGwTDwDi45i5K0RjiQsWYOQ_6ZBLroRZP5EYnIT/s1600/conp12.PNG)  
*Запити паролів*
