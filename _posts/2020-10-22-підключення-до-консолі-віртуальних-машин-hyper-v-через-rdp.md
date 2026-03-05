---
layout: post
title: "Підключення до консолі віртуальних машин Hyper-V  через RDP"
date: 2020-10-22 16:31:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2020/10/hyper-v-rdp.html
---

#### .rdp

Для цього треба ввімкнути розширений режим (Enhanced Session Mode (ESM)),  у налаштуваннях Hyper-V

[![](/assets/images/blog/1e65380902afbdd0-1ee2038eb398ec0a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhL72JTWXOn3WBuAwgF1eKjrGVpv3_PuqhtgIv3FxBNucgoD72HbMWQddLceAs0cswa3ohyhKmKhg_qEJ8zmZe2Y8Bbdth4R7FB-dDce82_x0QvT3sVYSJaGY9Wd2qjsZO6xtOwkQF94PaR/s1196/remmina_SYS01_sys01.eseh.local_20201022-14486%252C164856.png)  
*налаштування Hyper-V*

Локальне підключення до консолі віртуальної машини виглядає так:

[![](/assets/images/blog/5327ba372711afa9-8f354cce48dda44c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5qa1b587loKzLusz672ACUgNA7UAGBaA0RfH40_lPa8HOQ2ppbUvZ6HqlnwMfczB7_t_HM3q2TY9lmbbU5oehVzcPHhzUboIy3YH3qfEk4ObFXkJRkfCWRjHBorLGN7u1bYsLjuBgysb6/s1028/remmina_SYS01_sys01.eseh.local_20201022-162812%252C942289.png)  
*Консоль  VM "MEDOC"*

Для підключення треба створити файл підключення .rdp. Для цього треба знати VMId для певної віртуальної машини.  

[![](/assets/images/blog/e0fd0d28f68651f2-b7950d472c261ddc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibxyEjFG-Z7_wtbRmyILkMmvFGgbOgdUJ7zVF3TamlaYHISdGJcaD66STRBRyl4o_VApTvlFmePFdIUxcJblpUOH3LIf_8Fc6iuNrkURi1na2QrnIZDb-eWMeEIFYYu7eXvZ8WUiM5eofS/s382/remmina_SYS01_sys01.eseh.local_20201022-144440%252C374972.png)  
*Отримання VMId для VM "MEDOC"*

  

console-medoc.rdp

```
full address:s:hypervserver.domain.lan
pcb:s:2d0c8466-5a9d-4a22-aae8-1282f9802d5f
server port:i:2179
negotiate security layer:i:0
```

Після створення цього файлу можна за ним підключатися, буде запит на ім'я адміністратора і його пароль.

[![](/assets/images/blog/5edadaed9c401ea4-70ac7f25a1c76ac8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitZMr6DMlRuiZ5dhzpeWGYyp7Bgtt3oqcZ7v6uYwxOEj7MWVQcJZ_jhDXkuCVe1Zh4mk_eTP1df4_1GCrUq-vxB4HqMLIryEZEWergjktSB5Mwwi1wRPP4Ea9gN4Rg3GPxJYurDEemJIa1/s1220/remmina_SYS01_sys01.eseh.local_20201022-143241%252C266574.png)  
*Результат віддаленого підключення (RDP) до консолі VM "MEDOC"*

#### vmconnect.exe

Інший шлях використати програму "vmconnect.exe"

[![](/assets/images/blog/05a949b3e2b1d7d9-8b0f93b867ec0902.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjN5jSMgYje5-HBgfFPEVBYF_iznO1t4crKk422v6oGkwFrbu-JcWDBlVevFpTWJ4r7RuBmzzvpniJ1QOe0zKOMB3eb5NJhrP9RNj7xN6OFUJooj3OhyHbp0mC0ywPEMRG9zzHZx4pSbBEX/s446/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-10-22+18-09-49.png)  
*vmconnect.exe*

Але так як hyper-v server не є членом домену, тому без додаткових налаштувань, на кшталт CredSSP, не вийшло.
