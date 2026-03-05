---
layout: post
title: "Настирливих спамерів в black list IP і блокуємо їх в IPFW"
date: 2012-02-03 11:34:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/02/black-list-ip-ipfw.html
---

Є:   

* FreeBSD,
* Postfix,
* Swatch - log analyze.
* MySQL - database
* IPFW - firewall

  
Отже **завдання** як тільки в logfile pistfix's з'явився запис на подобі:   

|  |
| --- |
| **Code:** |
| Jul 10 15:43:52 mail postfix/smtpd[89997]: warning: Connection rate limit exceeded: 10 from unknown[62.213.112.69] for service smtp   Jul 10 15:43:52 mail postfix/smtpd[89997]: disconnect from unknown[62.213.112.69] |


  
Те заносимо IP адресу у тимчасовий чорний список "IP block list", викорисувуючи правила для firewall - IPWF і дані про адреси що записані у таблиці № 99, а правило з цієї таблиці всіх не пускає.   
  
**Реалізація**:Обмеження не більше 4 підключень в 2 хвилини, не вважаючи локальні адреси.   
postfix, main.cf:   

|  |
| --- |
| **Code**: |
| anvil\_rate\_time\_unit = 240s   smtpd\_client\_connection\_rate\_limit = 4   smtpd\_client\_connection\_limit\_exceptions = 127.0.0.1 172.16.1.0/24   smtpd\_client\_event\_limit\_exceptions = 127.0.0.1 172.16.1.0/24 |

  
Дивимося лог, якщо перевищення 10 раз ліміт підключень, блокуємо адресу IP на 12 годин.   
Swatch-postfix-lim:  
  

|  |
| --- |
| **Code**: |
| watchfor /warning: Connection rate limit exceeded: 10/i    echo    exec /usr/local/etc/manage\_scripts/ipfw-blockip-0.sh 12 '$\_' |

Авто запуск swatchrc.conf   

|  |
| --- |
| **Code:** |
| swatch\_enable="YES"   swatch\_rules="7"   swatch\_7\_flags="--tail-file=/var/log/maillog --config-file=/usr/local/etc/swatch/.swatch-postfix-lim --daemon --pid-file=/var/run/swatch\_7.pid --restart-time=00:00"   swatch\_7\_pidfile="/var/run/swatch\_7.pid" |

Правило фаєрвола:   
ipfw.rules:   

|  |
| --- |
| **Code:** |
| deny log logamount 5 ip from table(99) to any |

Додавання в базу даних, і в таблицю файервола. Виключаючи
IP 111.11.11.111 (Свій, а то при тестірваніі можна гілку обрізати на
якій сидиш Smile) І протоколюючи поштою, причину занесення адреси.   
ipfw-blockip-0.sh:   
**Code:**  

#!/bin/sh

# Block postfix spammers by ip, $1 - hours block duration,  $2 - log line

# ipfw table 99 - list of ips. ipfw deny from table(99) any

# Oleksiy Petrov, 2009, http://lexxai.co.cc

ip=`echo $2 | /usr/bin/sed -n 's#.\*\[\([0-9.]\*\)\].\*#\1#p'`

ips=`echo $ip | /usr/bin/grep -v '^111\.11\.11\.111$'`

if [ -n "$ips" ]

then

/sbin/ipfw table 99 add $ips $1

/sbin/ipfw table 99 list > /var/tmp/~blockip

/usr/bin/tail -n 50 /var/log/maillog | /usr/bin/grep -e "RCPT from.\*\[$ips\]"  >> /var/tmp/~blockip

cat /var/tmp/~blockip | mail -s "ipfw block [$ips] `host $ips`" root

rm /var/tmp/~blockip

export HHH=$HOME

export HOME=/root

echo "replace into blackip VALUES('$ips',DATE\_ADD(NOW(),
INTERVAL $1 HOUR));" | /usr/local/bin/mysql --defaults-file=~/.my.cnf 
mailusers

export HOME=$HHH

            fi  
mysql: table mailusers.blackip   

|  |
| --- |
| **Code:** |
| CREATE TABLE IF NOT EXISTS `blackip` (     `ip` char(15) CHARACTER SET ascii NOT NULL,     `time` timestamp NOT NULL DEFAULT CURRENT\_TIMESTAMP,     PRIMARY KEY (`ip`)   ) ENGINE=MyISAM DEFAULT CHARSET=utf8; |

  
~ /. My.cnf:  
  

|  |
| --- |
| **Code:** |
| [client]   user = mailusers   password = fdfjuu3\*\*\*def |

Очищаємо застарілі IP раз на годину.  
 ipfw-blockip-release.sh:   

|  |
| --- |
| **Code:** |
| #!/bin/sh   # Block postfix spammers by ip, $1 - hours block duration,  $2 - log line   # ipfw table 99 - list of ips. ipfw deny from table(99) any   # Oleksiy Petrov, 2009 http://lexxai.co.cc           export HHH=$HOME           export HOME=/root           echo "SELECT ip FROM blackip WHERE time < now( ) " | /usr/local/bin/mysql  --defaults-file=~/.my.cnf --skip-column-name  mailusers |  while read str; do \               /sbin/ipfw table 99 delete "$str" | echo "DELETE FROM blackip WHERE (ip='$str');" | /usr/local/bin/mysql --defaults-file=~/.my.cnf mailuser           done           export HOME=$HHH |

crontab:   

|  |
| --- |
| **Code:** |
| @hourly                                 root    /usr/local/etc/manage\_scripts/ipfw-blockip-release.sh |

При старті системи, таблиця файрвола порожня, заповнимо її з бази даних.  
 ipfw-blockip-init.sh:   

|  |
| --- |
| **Code:** |
| #!/bin/sh   # Block postfix spammers by ip, $1 - hours block duration,  $2 - log line   # ipfw table 99 - list of ips. ipfw deny from table(99) any   # Oleksiy Petrov, 2009 http://lexxai.co.cc           export HHH=$HOME           export HOME=/root           echo "SELECT ip FROM blackip WHERE time > now( ) " | /usr/local/bin/mysql  --defaults-file=~/.my.cnf --skip-column-name  mailusers |  while read str; do \               /sbin/ipfw table 99 flush               /sbin/ipfw table 99 add "$str" 999           done           export HOME=$HHH |


Обговорювання теми [на форумі](http://translate.google.com.ua/translate?sl=ru&tl=uk&js=n&prev=_t&hl=uk&ie=UTF-8&layout=2&eotf=1&u=http%3A%2F%2Fwww.postfix.ru%2Fviewtopic.php%3Ft%3D23483%20) , Posted: Fri Jul 10, 2009 8:32 pm  
  
Як на додаток, можна ознайомитися з наступними проектами:  
Аналізатор лог файлів так блокування за правилами, [Fail2ban](http://www.fail2ban.org/) - FreeBSD: /usr/ports/security/py-fail2ban
