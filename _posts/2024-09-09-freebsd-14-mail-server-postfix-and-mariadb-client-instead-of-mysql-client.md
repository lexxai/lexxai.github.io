---
layout: post
title: "FreeBSD 14. Mail server POSTFIX and mariadb-client instead of mysql-client"
date: 2024-09-09 14:22:00 +0000
tags: ["administration", "cli", "FreeBSD", "linux", "mariadb", "mysql", "ports", "postfix", "Proxmox", "Virtualization"]
blogger_orig_link: https://lexxai.blogspot.com/2024/09/freebsd-14-mail-server-postfix-and.html
---

Маю операційну систему FreeBSD 14.1-RELEASE у віртуальному середовищі Proxmox VE.

Щойно оновив поштовий сервер з FreeBSD 13.1-RELEASE, і з'ясувалося що я не можу тепер встановити POSTFIX та mariadb-client одночасно, як це було раніше. Тому це нотатка мені як я  розв'язав цю проблему, щоб не наступати на ті самі граблі двічі.

Коли встановлено у Вас *mariadb-server* та *mariadb-client* на одному сервері, то при встановленні поштового сервера *postfix* як пакунок через *pkg install postfix-mysql*, або з портів з опцією *MySQL*.

[![](/assets/images/blog/1d6df7dd4130ead7-d253aa9fe243b1fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxyXaInmG5nZsFZ_ZIrLN7l3OmsC9CCRMO7XRXdpg4osqW880o8Jc2wcTGnrx_-MSSEN62sKx1rus8Hs9qRm4ze_y7Li4UIAhUgejpDI7DwhiRzqE_i547zxWHsgRcJvL6SWxozQvHvAKfJev61BkC8VE4pDZJjc8g_p7PT-4J0oHyly7dbb-8PfSRCxI_/s1489/Screenshot%202024-09-09%20164754.png)  
*postfix freebsd port, mysql option*

  
 Вам буде пропоновано видалити *mariadb-server* та mariadb-client і встановити *mysql-client*.

[![](/assets/images/blog/fdc2abf1e6d3bd94-058d2cde7ef42f88.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEglYygI-02Y60WkXx7G3oVvdJKEd_MrTmBPNU23gfTPJvLu72W7zvLvRV3vLDtp04DIU2kFz8CT3ofAg6ym4IiWc1qQMu5VxQRB8PVwa7oa55ef60PGr1Esyw86dkFgNwrKUpv70gy5WZbBWjHxPy6ulKCAIkvZXlqkHIEKD8pQeQPNRftCVhchxVYKFyVC/s1285/Screenshot%202024-09-09%20165105.png)  
*видалити mariadb-server та mariadb-client і встановити mysql-client*

У такому випадку необхідно зробити dump до sql з mariadb server встановити *mysql-server*, *mysql-client*, та імпортувати базу з dump до нововстановленого *mysql-server* і потім встановити *postfix-mysql*.

Але якщо мені потрібно зберегти *mariadb-server*, то треба використовувати збирання *postfix* з портів, але сталося не все як хотілося.

[![](/assets/images/blog/b8186fb51bbdadc5-d00747827365dd5a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhng8CvfXRy4KrkJVVoqvPkXtiNKRcBQYybzbQUphd-BhziBT1iVz-uuAmXR90mv-9eLEf30vJqCf1Sprw2kfbVdyOWCxbew-_DqmWjQQCGi4simXUMTcPfciNwAHGkUqiST1STRDhXkAzRRY4zOKYloGiji5ueWnfCZFiTIkv6p9rG_4wFmI2lA32n9pX5/s1502/Screenshot%202024-09-09%20164116.png)  
*Збирання postfix з портів з MySQL підтримкою.*

Як видно на зображені, не знайдено бібліотеки *===>   postfix-3.9.0\_1,1 depends on shared library: libmysqlclient.so.21 - not found*, і за допомоги файлу *Makefile* починають збирати відсутній пакунок  */usr/ports/databases/mysql80-client*.  

Для розв'язання проблеми я створив символічне посилання для цього файлу що не існує  *libmysqlclient.so.21*.

[![](/assets/images/blog/d4f95ddf5764b0e9-6f062a2ce4f759f0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1Ty_vOkO1Ed6eVtQK5gdAZ_mEkOa4T7I15ovRZL6Nj5Fbi25nrvqUf2Z6xfQ78_nWtHFxA8ZqOYrnglfKfni3FJ3wLysQCQD61GlkFt_wRFMCkW-Rxfi1pdX54QRlz-trVlsRzoTZbqIfD5yUpbjFM5xUUZVzkWgP5ieJTkoqr16ovIi0I7YLFBGnYJsV/s1247/Screenshot%202024-09-09%20170432.png)  
*ln -s /usr/local/lib/mysql/libmysqlclient.so /usr/local/lib/mysql/libmysqlclient.so.21*

[![](/assets/images/blog/51dffbda25836489-29b509b4b130abf8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjYaBZ3djK2JkjxReSJqYTMkstwdFa3GxBJ8oiguz5HpmpY3MDhrrkqQNcnmbenaUtb83n06uhRx9FP4Q7DPcpxdeiqDUuqXHbmvadVJo7OfOSGFz_xoiD_SIzcYFV3Z1JJ2BMuH-xAXRFXuDaQ7aw_5oeuM4Q5wTNQB6K9R7cHvCw85rOzfKUKuNsroDWd/s1500/Screenshot%202024-09-09%20170655.png)  
*Successful building of postfix 3.9 with mariadb1011-client-10.11.8\_1*
