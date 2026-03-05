---
layout: post
title: "FreeBSD  ntpd starting up after upgrade. pwd_mkdb"
date: 2019-02-18 23:57:00 +0000
tags: ["FreeBSD", "ntp", "password"]
blogger_orig_link: https://lexxai.blogspot.com/2019/02/freebsd-ntpd-starting-up-after-upgrade.html
---

Here, mergemaster added the ntpd user to /etc/passwd, but for some reason spoiled the password database. I simply ran  `/usr/sbin/pwd_mkdb -p /etc/master.passwd` and this resolved the issue.  
  
After updating another system, the old ntpd.pid file owened by the 11.2 root user was for some reason left in place and prevented the new ntpd starting up, because the user ntpd
had no write permissions to the old .pid file (owned by root). I needed
to delete said old .pid file manually and that resolved that issue.  
  
  
<https://forums.freebsd.org/threads/ntpd-not-starting-after-upgrade-to-12-0-release.68758>
