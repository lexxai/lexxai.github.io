---
layout: post
title: "Dovecot, PostfixAdmin mysql alias domain"
date: 2014-03-05 09:15:00 +0000
tags: ["alias", "domain", "dovecot", "mysql", "postfix", "postfixadmin"]
blogger_orig_link: https://lexxai.blogspot.com/2014/03/dovecot-postfixadmin-mysql-alias-domain.html
---

[![](/assets/images/blog/78844090e683bd35-e41db6b719284073.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2CRAy3RU_sm4Rx_hyphenhyphentQ87G71aChX_x-WBOhvVssu_n3xtO1lTQ5ms3w1E6qxEUl_OrYDwSY330t-eR2aL196IUL7KlYn4C7yQxVZhMTL4XzmzVi0TaVOx8AX4gsR70BOcCa-eFpI8axP_/s1600/dovecot-postfix.png)

Завдання, до поштового домену додався додатковий домен - alias  
У поштовій системі використовується зв'язка  postfix+dovecot+mysql для зручності адміністрування PosfixAdmin.  
  

### Dovecot

  

```
password_query = 
SELECT username as user, password FROM mailbox AS t1 LEFT JOIN \
 alias_domain AS t2 ON alias_domain='%d' AND t2.active='1' \
 WHERE local_part = '%n' AND (t1.domain="%d" OR t1.domain=t2.target_domain) \
 AND t1.active='1'
```

  
  

```
user_query =
SELECT   concat('/home/vmail/',maildir) AS home, \
   concat('/home/vmail/',maildir,'dovecot.sieve') AS sieve, \
   5000 AS uid, 5000 AS gid, concat('*:bytes=',floor(quota)) AS quota_rule \
   FROM mailbox AS t1 LEFT JOIN alias_domain AS t2 ON alias_domain='%d' AND t2.active='1' \
   WHERE local_part = '%n' AND (t1.domain="%d" OR t1.domain=t2.target_domain) \
   AND t1.active='1'
```

  

### MySQL

  
--  
-- Структура таблиці `mailbox`  
--  

```
CREATE TABLE `mailbox` (
  `username` varchar(255) NOT NULL,
  `password` varchar(255) NOT NULL,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_unicode_ci NOT NULL,
  `maildir` varchar(255) NOT NULL,
  `quota` bigint(20) NOT NULL DEFAULT '0',
  `local_part` varchar(255) NOT NULL,
  `domain` varchar(255) NOT NULL,
  `created` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
  `modified` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
  `expire` date NOT NULL DEFAULT '0000-00-00',
  `active` tinyint(1) NOT NULL DEFAULT '1',
  `ext` tinyint(1) NOT NULL DEFAULT '0',
  `expire_ext` date NOT NULL DEFAULT '0000-00-00',
  `glob-maillist` tinyint(1) NOT NULL DEFAULT '0',
  PRIMARY KEY (`username`),
  KEY `domain` (`domain`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1 COMMENT='Postfix Admin - Virtual Mailboxes';
```

  
--  
-- Структура таблиці `alias\_domain`  
--  

```
CREATE TABLE `alias_domain` (
  `domain` varchar(255) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `description` varchar(255) CHARACTER SET utf8 NOT NULL DEFAULT 'Postfix virtual alias domain',
  `maxaliases` int(10) NOT NULL DEFAULT '-1',
  `server` varchar(255) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `created` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
  `modified` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
  `modified_by` varchar(255) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `active` tinyint(1) NOT NULL DEFAULT '1',
  `alias_domain` varchar(255) NOT NULL,
  `target_domain` varchar(255) NOT NULL,
  PRIMARY KEY (`domain`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1 COMMENT='Postfix Virtual Alias Domains';
```

  
  
  

### Postfix

mysql\_virtual\_alias\_domain\_mailbox\_maps.cf :  

```
user =dbuser
password = dbpassw
hosts = 127.0.0.1
dbname = mailusers
query = SELECT maildir FROM mailbox,alias_domain WHERE alias_domain.alias_domain = '%d' \
 AND mailbox.username = CONCAT('%u', '@', alias_domain.target_domain) AND mailbox.active = '1' \
 AND alias_domain.active='1'
```

  
  
  
mysql\_virtual\_alias\_domain\_maps.cf :  

```
user =dbuser
password = dbpassw
hosts = 127.0.0.1
dbname = mailusers
query = SELECT goto FROM alias,alias_domain WHERE alias_domain.alias_domain = '%d' \
 AND alias.address = CONCAT('%u', '@', alias_domain.target_domain) AND alias.active = 1 \
 AND alias_domain.active='1'
```

  
main.cf:  

```
virtual_mailbox_maps=mysql:$pfbase/sql/mysql-virtual-mailbox-maps.cf,
                     mysql:$pfbase/sql/mysql_virtual_alias_domain_mailbox_maps.cf
```
