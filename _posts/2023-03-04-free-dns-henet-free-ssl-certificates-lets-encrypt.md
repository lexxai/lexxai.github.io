---
layout: post
title: "Free DNS he.net + Free SSL Certificates Let's Encrypt"
date: 2023-03-04 20:02:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/03/free-dns-henet-free-ssl-certificates.html
---

### dns.he.net

Якщо у Вас є домен в DNS службі <https://dns.he.net>, то можна оновлювати TXT записи через динамічний ключ, а це надає можливість отримати безкоштовні сертифікати через [Let's Encrypt](https://letsencrypt.org/) у випадку коли у Вас наприклад поштовий сервер.

Рішення що є pligin до [certbot](https://certbot.eff.org/) - [he.net DNS Authenticator plugin for Certbot](https://github.com/gentoo-root/certbot-dns-henet) потребує повний доступ через login/password до Вашого he.net акаунта, як на мене це жирно :)

Тому було знайдено рішення оновлення через динамічний ключ для оновлення тільки одного TXT запису.

[![](/assets/images/blog/f70764fe23836d9d-ed3019972ef36b1e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_1nJaTzD5zpRUFsbYZGnZefr0kegM0VCGHlPx24hNNRxcFGtD9N80yQVpjVqSNGhWS4VcmHCMjr8YipaEoMGvMhYjxcB-CMdUsghZsbD-A2YMRua5clU96ipENvJx9dafNLpbuox7QqIYtLMMLXslUHhUnCHSn5jQHzsXeCEEjW6Y9K37oRUpIKnMSg/s1062/Screenshot%202023-03-04%20213237.png)  
*he.net TXT*

Для отримання ключа його можна згенерувати, наприклад: aJoSWOFyLD1A3iDG

[![](/assets/images/blog/4eb502d835ee95b8-92dc57175bb2ba3e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQa-3IH2PngXK5BpLg7e1gQPSmDjWatU_zRZ8FBegtke106qwAEvRvgmQussDfHErHnQNfuhyHoRw6xibVYUeQ5XR2FUHDIG-Ct8ee2A8RVhRGdSqqhA4T6JqJkHnV3BD6AKCYNZSxXQCS_4llTIbNGRResuDKaFIUq_XwQhiAExp8ci9LjDUeN_Z18g/s795/Screenshot%202023-03-04%20213446.png)  
*he.net dynamic key*

#### 

### Оновлення he.net

Далі створюю скрипт оновлення або очищення запису.

he-dns-update.sh

```
#!/bin/sh

# Do we have everything we need?

if [ -z "$CERTBOT_DOMAIN" ] || [ -z "$CERTBOT_VALIDATION" ]; then
    echo '$CERTBOT_DOMAIN and $CERTBOT_VALIDATION environment variables required.'
    exit 1
fi

# Add all HE TXT record DDNS keys to the txt_key object
# Remember to protect this script file - chmod 700!
case $CERTBOT_DOMAIN in
 domain)
  txt_key='aJoSWOFyLD1A3iDG'
 ;;
esac

if [  "$1" == "clean" ];then
 CERTBOT_VALIDATION=' '
fi


# Create a FQDN based on $CERTBOT_DOMAIN
HE_DOMAIN="_acme-challenge.$CERTBOT_DOMAIN"

# Update HE DNS record
curl -s -X POST "https://dyn.dns.he.net/nic/update" -d "hostname=$HE_DOMAIN" -d "password=${txt_key}" -d "txt=$CERTBOT_VALIDATION"

# Sleep to make sure the change has time to propagate over to DNS
sleep 30
```

Для захисту ключа змінено права доступу: chmod 700 he-dns-update.sh

Ваш ключ для оновляння домену: domain, фіксований і є  txt\_key='aJoSWOFyLD1A3iDG'. Змініть на свої значення.

### Cертифікат Lets Encrypt

Для генерації сертифікату за допомогою пакету certbot:

```
#!/bin/sh

certbot certonly \
  --preferred-challenges dns \
  --manual \
  --manual-auth-hook "/root/script/certs/he.net/he-dns-update.sh"  \
  --manual-cleanup-hook "/root/script/certs/he.net/he-dns-update.sh clean"  \
  --manual-public-ip-logging-ok \
   -d 'domain' \
  --server https://acme-v02.api.letsencrypt.org/directory --agree-tos \
  --post-hook /root/script/certs/he.net/reloadservices.sh \
  -m postmaster@domain
```

Для оновлення роботи сертифікатів скрипт перезавантаження служб post-hook - reloadservices.sh. Шляхи до розміщення скриптів : /root/script/certs/he.net/. Ваш 'domen' домен замінити на власний.

```
#!/bin/sh

service postfix restart
service dovecot restart
```

### Додатки

У додатках змінюємо шляхи до сертифікатів:

Postfix:

```
smtpd_tls_CAfile = /usr/local/etc/letsencrypt/live/domain/fullchain.pem  
smtpd_tls_cert_file = /usr/local/etc/letsencrypt/live/domain/cert.pem  
smtpd_tls_key_file = /usr/local/etc/letsencrypt/live/domain/privkey.pem
```

Dovecot:

```
ssl_cert = </usr/local/etc/letsencrypt/live/domain/fullchain.pem  
ssl_key = </usr/local/etc/letsencrypt/live/domain/privkey.pem
```

### CAA записи DNS

Для застосування [CAA](https://en.wikipedia.org/wiki/DNS_Certification_Authority_Authorization) записів треба використати домен "letsencrypt.org"

[![](/assets/images/blog/c1a7349343a4a268-aa910ffe0f3a4cdf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRdYAvf-JfnRfLzg4a-vdVweMaWTB42tjgUuVob_G1PMkgR3Wul_4tpLIrkNmwLAA2etQik0LgylZiu661Bl4ql9ASdQhRActtnRjsFQ9mxfpGYtXNSeiTXB447UtjBfOfvb57LCY83QU36xGStv_GMAe4R78lmw4LvUipeyZIiJEMPvghnfHSp2xtpg/s1097/Screenshot%202023-03-04%20214955.png)  
*CAA letsencrypt.org*
