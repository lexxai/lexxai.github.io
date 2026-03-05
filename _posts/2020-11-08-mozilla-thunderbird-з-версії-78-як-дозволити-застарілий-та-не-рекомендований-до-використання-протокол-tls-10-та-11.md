---
layout: post
title: "Mozilla Thunderbird з версії 78 : Як дозволити застарілий та не рекомендований до використання протокол TLS 1.0 та 1.1"
date: 2020-11-08 21:03:00 +0000
tags: ["administration", "email", "mail", "Mozilla Thunderbird", "security", "server", "TLS"]
blogger_orig_link: https://lexxai.blogspot.com/2020/11/mozilla-thunderbird-78-tls-10-11.html
---

З нової 78 версії поштової програми "[Mozilla Thunderbird](https://www.thunderbird.net/uk/)" введено обмеження на використання застарілих і небезпечних протоколів безпеки.  
Якщо у Вас застарілий поштовий сервер то головна проблема використання є те що програма про це ніяк не повідомляє - просто пошта не приходить, але з'єднання є.  
За логами поштового сервера можна з'ясувати це:

```
Nov  8 22:01:00 dovecot: imap-login: Disconnected (no auth attempts): rip=XX.XX.XX.XX, lip=YY.YY.YY.YY, TLS handshaking: SSL_accept() failed: error:1409442E:SSL routines:SSL3_READ_BYTES:tlsv1 alert protocol version 
```

Щоб ТИМЧАСОВО дозволити роботу поштового клієнта є рішення як дозволити підключення з TLS 1.0.

Основний матеріал:   

[Thunderbird 78 FAQ | Thunderbird Help : How to enable outdated security protocols TLS 1.0 and 1.1](https://support.mozilla.org/en-US/kb/thunderbird-78-faq#w_how-to-enable-outdated-security-protocols-tls-1-0-and-1-1)

Додаю покрокові зображення:

[![](/assets/images/blog/bd660868db5db283-9ca353c8a0bc4970.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIwryUGWdLAV1wVRrLpGG-huzYgtG3GE1Bzm5w66tHdwgb81_eF95g3St8Qfu7vlwcusbO3fqsiyykOPOmKYWJmdDQ1OykzB_DayM3Bb5zkIJqKVI6hYhpgPD7kwywF4cLQnBgdpxdLMKl/s826/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-36-42.png)  
*Іконка налаштувань*

[![](/assets/images/blog/d4502f4ecd5abb81-fa90e8817e79a4bf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLszSULmKBlxHf-uY_KsNoqpmglVrY5G3wbT9087rAfotxrCZ5tVVctM0GjrJw6zvyS4Cau2E-Grfpkk7lGCsgXC_7TlESqOGU67aAkG7mnY1O2oTZ5SNtF6a_bOpjnqhGD6aODn_Bje1l/s600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-38-34.png)  
*Налаштування*

[![](/assets/images/blog/b92bab1339c34da8-660f4daaca96b0f1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMGzC5m-zR3mLUVjz1vZjkzofKLbAGGgM1-1Usp_sX2sw1bP8w3inLRANbqlOLpvEzr-9SkI3U0vGmEGFRpxaazxfDEdHXJyuVcE4Q3BgcZY3uIiYukeUlDXtcTAZMeklUU_sh6ow0D1b0/s1153/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-40-24.png)  
*Пошук в налаштуваннях*

[![](/assets/images/blog/0c917b281a9a6c5f-f150b9a93a8ed470.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjE1DWVJ04pEXswLW2_rD44YBTWy_maneMcw1pwwU24t3aCy3w4VevOFZusQqpiNj_gz79crSzDHfNZtNzNsJJgEFAmiWK_nvjknmC6RQ2dNXzlrFZkzITLVNfz_pybk1O0dX1Ts9t0lnjM/s1195/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-28-35.png)  
*Пошук - about:config*

  

[![](/assets/images/blog/4e053ba29e3d298e-193c9d1002166f00.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCU3DxeNMPi5cf4XQTr6qxhL2PXL-loQsW3XY_EyEIdAKzX3RlPbIebQSZlebj7OsIOsgaNT8eL0rAIhNz-wjVuc1GNbd6bf77FhjgRPmmoBNqngc6BcX5YsYX19Df-V6uv3n4BuTTN2F6/)  
*Або пошук "Редактор налаштувань" для версії 91*

  
  

[![](/assets/images/blog/72d578b671849d2d-c3bdfadc351b8c09.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhydPPCiICP4RiqqK1BKGjqsfgZz7SytkPqzDgPrKDnTfyl_ClU9gjLDWZPIaxkNymLdsCa-Gss79K6EZjjq3fP_glyPDWrnJCEi9FRHKcqkHeMyWKQ5tYC968tU5HmE-NzeQuP0a7LMHTI/s1184/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-28-58.png)  
*Погодитись на ризик*

  

[![](/assets/images/blog/4cad7c7ed8bae749-8b68eb6b30851bf6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjddNudTkmPgwlm1hBYa7zCfSu6oB2yHnApokjxb8AOikVMxmTzz7FJJIDpiki5O-ias8kE_1dA6xkjhHy4O4hiJx9trBFlK60IpXp6I1PRVga_JplMPr3ePieGmUs52AroNRvhca1K2dzk/s1191/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-29-31.png)  
*Пошук параметра - security.tls.version.min*

  

[![](/assets/images/blog/d04111ccda504c22-0820a6e207c02991.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKir5xRxQBPFfUL5QtZQbh4niJMXSg8VJl2ztMr5UB01XI28pzGTU8dHVGOFmUd6Bf38gECLlwBHMbpdgd1OJ0S2TrQHVBusEHNDOiQHp8Qk0fGm9CkYXBz1sGX_Bax2i_oS5a4ykfQKRg/s1179/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-08+22-29-46.png)  
*Змінити мінімальну версію з "3" до "1"*
