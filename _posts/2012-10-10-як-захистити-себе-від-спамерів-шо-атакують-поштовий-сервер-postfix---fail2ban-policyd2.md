---
layout: post
title: "Як захистити себе від спамерів шо атакують поштовий сервер Postfix - Fail2ban, Policyd2"
date: 2012-10-10 20:05:00 +0000
tags: ["fail2ban", "pfctl", "policyd2", "postfix", "postgesql", "tcpdrop"]
blogger_orig_link: https://lexxai.blogspot.com/2012/10/postfix-fail2ban.html
---

## Як захистити себе від спамерів що атакують поштовий сервер Postfix.

Визначаємо активних  користувачів що відсилають пошту понад дозволенного ліміту за певний час, блокуємо доступ з IP адреси до сервера на певний час. Також блокуємо користувачів що ввели забагато неправильних паролів: *authentication failed* та супер активних користувачів що хочуть надіслати дуже швидко і багато : *Connection rate limit exceeded*.  
  
  
  
Компоненти:  

### Policyd2

/usr/ports/mail/policyd2  
policyd2-2.0.12   -  Policyd v2 is a multi-platform policy server for popular MTA ...  
  
  
*/usr/local/etc/cluebringer.conf:*  
[server]  
  
# Protocols to load  
protocols=<<EOT  
Postfix  
#Bizanga  
EOT  
  
# Modules to load  
modules=<<EOT  
Core  
AccessControl  
#CheckHelo  
#CheckSPF  
#Greylisting  
Quotas  
EOT  
  
[database]  
DSN=DBI:Pg:database=policyd;host=localhost  
Username=somedbuser  
Password=somedbuserpwd  
  
# Access Control module  
[AccessControl]  
enable=1  
  
# Greylisting module  
[Greylisting]  
enable=0  
  
# CheckHelo module  
[CheckHelo]  
enable=0  
  
# CheckSPF module  
[CheckSPF]  
enable=0  
  
# Quotas module  
[Quotas]  
enable=1  
  

### Postfix & Policyd2

  
*postfix/main.cf:*  
smtpd\_recipient\_restrictions =  
   check\_policy\_service inet:127.0.0.1:10031,  
   permit\_mynetworks,  
   reject\_non\_fqdn\_sender,  
   reject\_sender\_login\_mismatch,  
   permit\_sasl\_authenticated,  
   reject\_unauth\_destination  
   
smtpd\_end\_of\_data\_restrictions = check\_policy\_service inet:127.0.0.1:10031  
  
anvil\_rate\_time\_unit = 240s  
smtpd\_client\_connection\_rate\_limit = 10  
smtpd\_client\_event\_limit\_exceptions = $mynetworks  
  
  
далі налаштування policyd2 тільки через Web interface.  
*cluebringer-2.0.12/INSTALL:*   
5. Install the webui/\*  into your apache directory, check out   includes/config.php  and adjust the MySQL server details.  

[![](/assets/images/blog/05a911cc5260b3a0-541915107c10b8d3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXwhDi79w7Q5wlFcSMr7ws9hYILnW1fFCOVDxi0EeJft1x3TlEmyjNLqCSCPxTxJ9YKKLPBeo__AhDBbdYgSSjEJG4WGSaLQBukkjIr6EVX9JMYG9opTvwn32TRMVg1PKBrrCYS1fxGwJ8/s1600/fb01.png)

  

[![](/assets/images/blog/8d26a7ee351a8274-43010eb7c79748af.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgj18Y-_TeO5wxrNQDx7TifZfkh3bsO4HAKgLeSva70mKdKw4aqZ5_SuM4zJ1_FP9Jl2old5vETsmpI9H2CAdRCrwHOS7xpHoDZ_qJmVqk2PvQ8wDwb7zeyPcjPOsEKo_KY4V0Z3Z0E4jNY/s1600/fb02.png)

  

[![](/assets/images/blog/fa9eabd7b59ae659-1d51ed8ab4f5346a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtq2CjCHCi-dqMWSexlTCxYVBieOZm6flaBD8vuMXNYPmeYZ6NKWgM542v4diqBMlt83lNTsOI9GlHX-96geNuJ0pxPKnU6jj7ML55IOfmfJEDIO79RiNHY0DkXJYiX7TuxMLGbLETMhjb/s1600/fb03.png)

  

[![](/assets/images/blog/8969f595537944ea-997a10e88b7b1b81.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAxYMudAPQykfTH8PwQtFIgY8WUZYMHrqNrcLJseYKAkYAaiwOd4uhfsF-xczQBS7DEU_MfJvu60J7kzQu5aZPBToX4uH7pofybFnpprDdEuKkH0EfBqVjsNAR_-bG1a7Aq0md4Ke4y1Zn/s1600/fb04.png)

  
Таким чином усі користувачі що знаходяться у таблиці Member "newuser" попадають під правила Policy List: "newusers to not internal"  
А модуль QUOTAS та POLICY: "newusers to not internal" підраховує скільки відіслано повідомлень за кількістю (MessageCount) , або скільки повідомлень за розміром (MessageCumulativeSize). Якщо ліміт перевищено, то виконується дія за POLICY: "newusers to not internal" - REJECT "LIMIT AT QUOTAUSE" - відкинути, і це буде записано до postfix лог файлу.  
  
  

### Fail2ban

/usr/ports/security/py-fail2ban   
py27-fail2ban-0.8.6 - Scans log files and bans IP that makes too many password ...  
  
  
  
*/usr/local/etc/fail2ban/filter.d/postfix-sasl.conf:*  
failregex = (?i): warning: [-.\_\w]+\[<HOST>\]: SASL (?:LOGIN|PLAIN|(?:CRAM|DIGEST)-MD5) authentication failed(: [ A-Za-z0-9+/]\*={0,2})?\s\*$  
  
*/usr/local/etc/fail2ban/filter.d/postfix-conlim.conf:*  
failregex = Connection rate limit exceeded: .\* from (.\*)\[<HOST>\] for service smtp  
  
*/usr/local/etc/fail2ban/filter.d/postfix-overlim.conf:*  
failregex = reject: RCPT from (.\*)\[<HOST>\]: .\*: LIMIT AT QUOTAUSE;  
  
  
*/usr/local/etc/fail2ban/jail.conf:*  
[postfix-sasl]  
enabled  = true  
filter   = postfix-sasl  
action   = pf  
logpath  = /var/log/maillog  
bantime  = 360000  
maxretry = 4  
   
[postfix-conlim]  
enabled  = true  
filter   = postfix-conlim  
action   = pf  
logpath  = /var/log/maillog  
bantime  = 360000  
maxretry = 4  
   
[postfix-overlim]  
enabled  = true  
filter   = postfix-overlim  
action   = pf  
logpath  = /var/log/maillog  
bantime  = 360000  
maxretry = 4  
  
  
Описую як треба передати інформацію до таблиці файровола pf, також завершення усіх поточних з'єднань за визначеною адресою та портами SMTP, SMTPS сервера.  
Додатково для спрощення аналізу - оновлю файл з переліком  блокованих адрес - fail2ban.txt.  
  
*/usr/local/etc/fail2ban/action.d/pf.conf:*  
  
  
actionban = /sbin/pfctl -t fail2ban -T add <ip>/32  
            /usr/sbin/tcpdrop -la | /usr/bin/egrep ' (25|465|110|995) <ip>' |/bin/sh  
           /sbin/pfctl -t fail2ban -T show -q > /var/db/filter/fail2ban.txt  
  
actionunban = /sbin/pfctl -t fail2ban -T delete <ip>/32  
  
*pf.conf:*  
# block banned SMTP's  IP  
table <fail2ban> persist file "/var/db/filter/fail2ban.txt"  
block in on $ext\_if proto tcp from <fail2ban> to any port {25, 465, 110, 995}  
  
  
Надалі до таблиці policyd2 - Member "newuser", додаємо користувачів за котрими треба "наглядати" у ручному або автоматичному режимі.   
  

### Автоматичний режим додавання користувачів до "нагляду"

Базується на аналізі лог файлу на предмет підрахувань спроб авторизації користувачів з різних країн та різних інтернет провайдерів під одним обліковим записом.  
  
У мене в наявності є рішення з описаним класом на PHP, а також спрощену версію з використанням шел скрипту.   
Ось скрипт що додає до бази даних спроби авторизації користувачів та їх адреси та код інтернет провайдеру що видав цю адресу.  
*store-postfix-sasl.sh:*  
#!/bin/sh  
  
echo "DELETE FROM xm\_sasl\_user\_activity;"| psql policyd2 somedbuser   
( zcat /var/log/maillog.0.bz2 ; cat /var/log/maillog ) | grep "sasl\_username=" | cut -d " " -f 1,3,4,10,8  | \  
    while  read str; do \  
    #echo $str;  
    date=`echo $str | sed "s/^\(.\*\) client.\*/\1/g"`  
    ip=`echo $str | sed "s/.\*\[\(.\*\)\].\*/\1/g"`  
    user=`echo $str | sed "s/.\*\=\(.\*\)/\1/g"`  
            #sed "s/.\*\[\(.\*\)\].\*/\1/g"  
            if [ -n "$ip" ]; then  
             isp=`whois $ip | grep netname |head -n 1| /usr/bin/awk '{ FS = ":"; gsub(/^ \*/,"", $2);print $2;}'`  
             if [ -z $isp ]; then  
                 isp=`whois $ip | grep '\-num:' |head -n 1| /usr/bin/awk '{ FS = ":"; gsub(/^ \*/,"", $2);print $2;}'`  
             fi  
             if [ -z $isp ]; then  
                isp=`whois $ip | grep -i 'netname:' |head -n 1| /usr/bin/awk '{ FS = ":"; gsub(/^ \*/,"", $2);print $2;}'`  
             fi  
             if [ "$isp" == "N/A" ]; then  
                isp=`whois $ip | grep -i 'ownerid:' |head -n 1| /usr/bin/awk '{ FS = ":"; gsub(/^ \*/,"", $2);print $2;}'`  
             fi  
             year=`date "+%Y"`  
             echo $year $date  
#            echo $user  
#            echo $ip  
#            echo $isp  
             echo "INSERT INTO sasl\_user\_activity (sasl\_username,ip,netname,tstamp) VALUES ('${user}','${ip}','${isp}',to\_timestamp('${year} ${date  
             psql -q imp nobody  
            fi  
    done  
  
  
Databse policyd2 table sasl\_user\_activity:  
*>psql policyd2 somedbuser   
policyd2=> \d sasl\_user\_activity*                                         Table "public.sasl\_user\_activity"  
    Column     |            Type             |                                Modifiers  
---------------+-----------------------------+-----------------------------  
 user\_id       | integer                     | not null default nextval('sasl\_user\_activity\_user\_id\_seq'::regclass)  
 sasl\_username | character varying(30)       | not null  
 ip            | inet                        | not null  
 netname       | character varying(70)       |  
 tstamp        | timestamp without time zone | not null default now()  
Indexes:  
    "sasl\_user\_activity\_pkey" PRIMARY KEY, btree (user\_id)  
  
  
*Class PHP SASL\_Activity.php:*  
  
<?php  
require\_once 'Net/Whois.php';  
  
class SASL\_Activity {  
    protected $db;  
  
    public function \_\_construct($db,$debug) {  
        $this->db = $db;  
        $this->whois = new Net\_Whois;  
        $this->pattern1 = '/origin:  \*(.\*)/i';  
        $this->pattern3 = '/netname: \*(.\*)/';  
        $this->pattern2 = '/ownerid: \*(.\*)/i';  
        $this->pattern4 = '/netname: \*(.\*)/i';  
        $this->pattern5 = '/(.\*) \(NET-.\*\)/i';  
        $this->debug = $debug;  
    }  
  
    public function clear() {  
        $res=$this->db->query("DELETE FROM sasl\_user\_activity");  
        $result=$res->fetch();  
        return $result[0];  
    }  
  
    public function check($user,$ip) {  
        $res = $this->db->query("SELECT count(\*) FROM sasl\_user\_activity WHERE sasl\_username=? AND ip=?", array($user,$ip));  
        return ('0' == $res->fetchColumn(0));  
    }  
  
    public function get\_isp($ip) {  
        $res = $this->db->query("SELECT netname FROM sasl\_user\_activity WHERE ip=? LIMIT 1", array($ip));  
        $isp = $res->fetchColumn(0);  
            if ( $isp == '') {  
                $data1 = $this->whois->query($ip);  
                if (preg\_match($this->pattern1,$data1,$matches1)) {  
                    $isp=trim($matches1[1]);  
                }else if (preg\_match($this->pattern2,$data1,$matches1)) {  
                    $isp=trim($matches1[1]);  
                }else if (preg\_match($this->pattern3,$data1,$matches1)) {  
                    $isp=trim($matches1[1]);  
                }else if (preg\_match($this->pattern4,$data1,$matches1)) {  
                    $isp=trim($matches1[1]);  
                }else if (preg\_match($this->pattern5,$data1,$matches1)) {  
                    $isp=trim($matches1[1]);  
                }else{  
                    $isp="\*\*\*UNDEFINED";  
                }  
            }else{  
                if ($this->debug) echo "ISP detected early: $isp\t";  
            }  
        return $isp;  
   
    public function store ($date,$user,$ip) {  
        if ($this->check($user,$ip)) {  
            $ispa=$this->get\_isp($ip);  
            if ($this->debug)   echo "ISP: $ispa";  
            if ($this->db->query("INSERT INTO sasl\_user\_activity (sasl\_username,ip,netname,tstamp) VALUES (?,?,?,?)",array(${user},${ip},${ispa},${date}))) {  
                if ($this->debug)       echo "\tInsterted OK\n";  
            }  
        }else{  
            if ($this->db->query("UPDATE sasl\_user\_activity SET (tstamp) = (?) WHERE sasl\_username=? AND ip=?",array(${date},${user},${ip}))) {  
                if ($this->debug) echo "\tUpdated OK\n";  
            }  
        }  
    }  
  
    public function get\_activity(){  
        $res = $this->db->query("SELECT foo.sasl\_username, COUNT(foo.sasl\_username) AS differ\_isp FROM (SELECT Distinct  sasl\_username, netname  FROM  xm\_sasl\_user\_activity GROUP BY netname, sasl\_username) AS foo GROUP BY sasl\_username ORDER by differ\_isp DESC");  
        return $res;  
    }  
  
    public function get\_hyper\_activity($limit=0){  
        $res = $this->db->query("SELECT \* FROM (SELECT foo.sasl\_username, COUNT(foo.sasl\_username) AS differ\_isp FROM (SELECT Distinct  sasl\_username, netname  FROM  xm\_sasl\_user\_activity GROUP BY netname, sasl\_username) AS foo GROUP BY sasl\_username) AS activity  
        return $res->fetchAll(Zend\_Db::FETCH\_ASSOC);  
    }  
  
  
    private function \_\_clone()  
    {}  
}  
  
*sasl-act.php:*  
date\_default\_timezone\_set('UTC');  
set\_time\_limit(60 \* 60 \* 24);  
error\_reporting( E\_ALL ^ E\_NOTICE);  
require\_once 'SASL\_Activity.php';  
$debug=0;  
$dirMailLog = '/var/log/';  
$fileMailLog = 'maillog.0.bz2';  
$log\_file=$dirMailLog . $fileMailLog;  
   
$useractivity = new SASL\_Activity($db,$debug);  
  
//Oct  3 02:10:14 mail postfix/smtpd[49400]: 1B33DB44909: client=unknown[137.243.223.131], sasl\_method=LOGIN, sasl\_username=noman  
//for parse log of postfix  
$pattern = '/(\w{3}[^a-zA-Z]+)+ mail postfix.\* client=.\*?\[([0-9.]+)+\],.\*sasl\_username=(.\*)/';  
  
$fh = bzopen($log\_file,'r') or die($php\_errormsg);  
  
$i = 1;  
$file\_prev\_day=strtotime('-1 day', filemtime($log\_file));  
$file\_year=date ("Y", $file\_prev\_day);  
if ($debug) echo "$log\_file year:$file\_year from:". date('Y-m-d',$file\_prev\_day). PHP\_EOL;  
//echo "Purge table:".$useractivity->clear()."\n";  
  
while (!feof($fh)) {  
  
    // read each line and trim off leading/trailing whitespace  
    if ($s = bzread($fh,16384)) { //fgets  
        // match the line to the pattern  
        if (preg\_match($pattern,$s,$matches)) {  
            list($whole\_match,$date,$remote\_host,$user) = $matches;  
            if ($debug) echo "\n##:".$i."  $file\_year $date  $remote\_host $user";  
            if ($debug)  echo "\nUser: $user\tIP: $remote\_host";  
            $date="$file\_year ".$date;  
            if ($debug) echo "\nStore: ";  
            $useractivity->store($date,$user,$remote\_host);  
       } else {  
            // complain if the line didn't match the pattern  
            error\_log("Can't parse line $i: $s");  
      }  
    }  
    $i++;  
}  
bzclose($fh) or die($php\_errormsg);  
  
add\_newusers\_activity($members,$useractivity);  
    
//add hyper acivity users to tables newusers poilicy  
    $hyperact=$useractivity->get\_hyper\_activity(10);  
    foreach($hyperact as &$val) {  
        if ($debug)  echo $val[sasl\_username].PHP\_EOL;  
        $user=$val[sasl\_username];  
        // here some methods for addMember($user.') to policyd2 tables with members of newusers poilicy  
    }
