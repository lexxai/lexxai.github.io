---
layout: post
title: "Нотатка: патч порту сервера NGINX для додавання модуля JWT AUTH. Платформа FreeBSD."
date: 2023-12-03 08:04:00 +0000
tags: ["authentication", "FreeBSD", "jwt", "nginx", "ports", "security", "server", "unix"]
blogger_orig_link: https://lexxai.blogspot.com/2023/12/nginx-jwt-auth-freebsd.html
---

Постала задача як зробити просту автентифікацію за JWT токеном безпосередньо через NGINX сервер.

За підпискою [NGINX PLUS jwt-auth](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-jwt-authentication/) є у базовому комплекті. У Community verision jwt-auth не має.

Знайшов модуль від автора [nginx-auth-jwt](https://github.com/kjdev/nginx-auth-jwt)  і вирішив додати до FreeBSD порту пакетів, щоб надалі було зручно собі оновлювати.

Результат оформив собі до репозиторію : <https://github.com/lexxai/port_nginx_add_jwt_auth>.

[![](/assets/images/blog/94da8b467717de19-62a587dc13a99abb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdnJ6TOoJllQRxtSq0CQZawH6E2cKcIEx2G5LpK5i61YVVrIBC0BsZBcHaFdd6DWpv54ejAXr0R2h906aW6LI9Z_iACnCCak0IiJcB_uVFPJEH8TqnaYw8z1qy-HhnS2Z7UZNta7SGbX5pSv2eKMAnU8Iu3o2qeglq90V2dVbFCGdqKeOf2JH4lS5eTaRW/s1011/make_menu.png)  
*nginx hwt-auth FreeBSD port 3rd party module*

Приклад використання nginx.conf:

```
load_module /usr/local/libexec/nginx/ngx_http_auth_jwt_module.so;

...
	location /token_protected {
		auth_jwt "closed site";
		auth_jwt_key_file .jwt_keyfile.json keyval;
		auth_jwt_validate_exp on;
		auth_jwt_validate_iat on;
		auth_jwt_validate_sig on;
		auth_jwt_validate_sub on;
		....
```
