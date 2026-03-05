---
layout: post
title: "Нотатка для себе: FreeBSD, Postfix, SASL, LDAP"
date: 2020-09-04 01:40:00 +0000
tags: ["FreeBSD", "LDAP", "OpenLDAP", "posfix", "sasl", "security"]
blogger_orig_link: https://lexxai.blogspot.com/2020/09/freebsd-postfix-sasl-ldap.html
---

### Postfix auth by SASL LDAP

```
cd /usr/ports/net/openldap24-client
make install clean
```

```
cd /usr/ports/security/cyrus-sasl2-saslauthd
make config
```

```
[x] OPENLDAP     Use OpenLDAP make install clean
```

/usr/local/etc/saslauthd.conf

```
  ldap_servers: ldap://LDAP_IP:389/  
  
ldap_bind_dn:
  cn=admin,o=l-mail,c=CA  
ldap_bind_pw: XPASSRDsLDAP  
  
ldap_search_base:
  o=l-mail,c=CA  
ldap_auth_method: custom  
  
ldap_port: 389  
#ldap_uidattr:
  uid  
#ldap_filter: &(uid=%u)(objectClass=xuidobject)  
#ldap_filter:(&(uid=%u)(premium=0))  
ldap_filter:
  uid=%u  
  
ldap_use_sasl: no  
ldap_timeout: 10  
ldap_scope:
  sub  
  
ldap_version: 2
```

```
cd /usr/local/lib/sasl2
```

```
echo "pwcheck_method: saslauthd" > smtpd.conf
echo "mech_list: plain login" >> smtpd.conf
```

```
cd /usr/ports/mail/postfix
```

```
make config
```

```
[x] LDAP        LDAP maps (uses WITH_OPENLDAP_VER)
[x] SASL        Cyrus SASL support (Dovecot SASL is always built in)
[x] TLS         Secure network connection support via TLS
```

```
make install clean
```
