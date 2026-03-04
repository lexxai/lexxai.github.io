---
layout: post
title: "FreeBSD. Mosquitto. Mosquitto-Auth-Plugin. LDAP"
date: 2019-08-01 00:44:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/08/freebsd-mosquitto-mosquitto-auth-plugin.html
---

Mosquitto ([http://mosquitto.org](http://mosquitto.org/))   
Mosquitto-Auth-Plugin (<https://github.com/jpmens/mosquitto-auth-plug#ldap-auth>)  
  
1. Install Mosquitto from ports  

```
$cd /usr/ports/net/mosquitto/
$make config
$make install
```

```
$ls -d /usr/ports/net/mosquitto/work/mosquitto-*
/usr/ports/net/mosquitto/work/mosquitto-1.5.8 (remember it)
```

2. Install openldap client from ports  

```
$cd /usr/ports/net/openldap24-client
$make config
$make install
$make clean
```

3. Check openssl lib  

```
$openssl version
OpenSSL 1.0.2o-freebsd  27 Mar 2018
```

4. Compile Mosquitto from git  

```
$pkg install git
$cd ~ 
$git clone git://github.com/jpmens/mosquitto-auth-plug.git
$cd mosquitto-auth-plug/
```

4.1. edit config.mk  

```
cp config.mk.in config.mk
```

```
MOSQUITTO_SRC = /usr/ports/net/mosquitto/work/mosquitto-1.5.8
OPENSSLDIR = /usr/bin
BACKEND_MYSQL ?= no
BACKEND_LDAP ?= yes
```

Copmile path on config.mk or Makefile   

```
CFG_LDFLAGS =-L/usr/local/lib
CFG_CFLAGS =-I/usr/local/include
```

or edit Makefile   

```
BE_CFLAGS = -I/usr/local/include
BE_LDFLAGS = -L/usr/local/lib/
```

or run comadns for replace  

```
sed 's#MOSQUITTO_SRC =#MOSQUITTO_SRC = /usr/ports/net/mosquitto/work/mosquitto-1.5.8#' config.mk.in > config.mk 
sed -i "" 's#OPENSSLDIR = /usr#OPENSSLDIR = /usr/bin#' config.mk
sed -i "" 's#BACKEND_MYSQL ?= yes#BACKEND_MYSQL ?= no#' config.mk 
sed -i "" 's#BACKEND_LDAP ?= no#BACKEND_LDAP ?= yes#' config.mk 
sed -i "" 's#CFG_LDFLAGS =#CFG_LDFLAGS =-L/usr/local/lib#' config.mk  
sed -i "" 's#CFG_CFLAGS =#CFG_CFLAGS =-I/usr/local/include#' config.mk
sed -i "" 's#BE_CFLAGS =#BE_CFLAGS = -I/usr/local/include#' Makefile     
sed -i "" 's#BE_LDFLAGS =#BE_LDFLAGS = -L/usr/local/lib/#' Makefile
```

4.2 Compile  

```
$pkg install gmake 
$qmake
```

[![](/assets/images/blog/c1ac361464a05b9d-7d402cf110b61167.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5w_DDB_1cTs0AUeEw4VVG6xnBk34RR4BK10b-XHKiCDxIz6V9D0wWYJh63O5gmF5dI3aopst-1PeSoJx789T9RVjJeMWMkc5v_Dopg40_8TKfXtxX6FHtL4cFwYCdOKUwQt5LD7riRQfX/s1600/mosq.PNG)  
*FreeBSD compile Mosquitto-Auth-Plugin*

  
4.3 Copy lib  

```
$cp auth-plug.so /usr/local/lib
```

  
5. Test LDAP request witj openldap tools ldapsearch  
*RFC 4516. LDAP URLs look like this: ldap://hostport/dn[?attrs[?scope[?filter[?exts]]]]  
where:  
  hostport is a host name with an optional ":portnumber"  
  dn is the search base  
  attrs is a comma separated list of attributes to request  
  scope is one of these three strings:  
    base one sub (default=base)  
  filter is filter  
  exts are recognized set of LDAP and/or API extensions.  
  
Example:  
  ldap://ldap.example.net/dc=example,dc=net?cn,sn?sub?(cn=\*)*   
  

```
$ldapsearch -LLL -h 127.0.0.1 -D "uid=someuser,ou=users,dc=lexxai,dc=pp,dc=ua" -w userPassword -b "dc=lexxai,dc=pp,dc=ua" "(uid=someuser)"

dn: uid=someuser,ou=users,dc=lexxai,dc=pp,dc=ua
uid: someuser
cn: Some
sn: User
userPassword:: ewe334X==
objectClass: uidobject
```

6. Use LDAP URI
  

```
auth_opt_ldap_uri ldap://127.0.0.1/ou=users,dc=lexxai,dc=pp,dc=ua?cn?sub?(uid=@)
```

7. Use auth-plug on Mosquitto  

```
/usr/local/etc/mosquitto/mosquitto.conf
auth_plugin /usr/local/lib/auth-plug.so
auth_opt_backends ldap
auth_opt_binddn cn=manager,dc=lexxai,dc=pp,dc=ua
auth_opt_bindpw s3crits33as
auth_opt_ldap_uri ldap://127.0.0.1/ou=users,dc=lexxai,dc=pp,dc=ua?cn?sub?(uid=@)
auth_opt_ldap_acl_deny false
```

8. ACL  
Since ACL not supported by LDAP auth plugin, try to use fixed ACL from file. ACL will work as backends in case option auth\_opt\_ldap\_acl\_deny = false in mosquitto.conf.  

```
/usr/local/etc/mosquitto/mosquitto.conf
acl_file /usr/local/etc/mosquitto/aclfile
```

```
/usr/local/etc/mosquitto/aclfile
user superAdmin
topic write users/#

pattern read users/%u
```

By this ACL one user superAdmin can write to all topics of users, and user can read only own topic like 'users/someuser' if username is 'someuser'.  
  
  
За матеріалами:  
[FreeBSD Mosquitto i Mosquitto-Auth-Plugin (Grzegorz Rybicki)](https://os.info.pl/freebsd-mosquitto-i-mosquitto-auth-plugin/)
