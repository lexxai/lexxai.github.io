---
layout: post
title: "Аналог команди tail та інших у MS Windows."
date: 2014-10-31 01:54:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/10/tail-ms-windows.html
---

Для того щоб у Windows можна було використовувати у консолі команду tail,  
треба встановити [Windows Server 2003 Resource Kit Tools](http://www.microsoft.com/en-us/download/confirmation.aspx?id=17657).  
Приклад використання:
  

```
cd "%ProgramFiles(x86)%\Windows Resource Kits\Tools\"
tail -f "c:/somedir/file.txt"  |  find "some grep text"
```

Перелік \*.exe  

```
adlb.exe
atmarp.exe
atmlane.exe
autoexnt.exe
cdburn.exe
cepsetup.exe
chklnks.exe
chknic.exe
cleanspl.exe
clearmem.exe
clusterrecovery.exe
compress.exe
confdisk.exe
consume.exe
creatfil.exe
csccmd.exe
custreasonedit.exe
delprof.exe
dh.exe
diskraid.exe
diskuse.exe
dnsdiag.exe
dvdburn.exe
empty.exe
eventcombmt.exe
fcsetup.exe
getcm.exe
gpmonitor.exe
gpotool.exe
hlscan.exe
ifilttst.exe
ifmember.exe
iniman.exe
instcm.exe
instexnt.exe
instsrv.exe
intfiltr.exe
kerbtray.exe
kernrate.exe
klist.exe
krt.exe
linkd.exe
linkspeed.exe
list.exe
lockoutstatus.exe
logtime.exe
lsreport.exe
lsview.exe
mcast.exe
memmonitor.exe
memtriage.exe
mibcc.exe
moveuser.exe
mqcast.exe
mqcatch.exe
nlsinfo.exe
now.exe
ntimer.exe
ntrights.exe
oh.exe
oleview.exe
pathman.exe
permcopy.exe
perms.exe
pfmon.exe
pmon.exe
printdriverinfo.exe
qgrep.exe
qtcp.exe
rassrvmon.exe
rcontrolad.exe
regini.exe
regview.exe
remapkey.exe
reportgen.exe
robocopy.exe
rpccfg.exe
rpcdump.exe
rpcping.exe
rpingc.exe
rpings.exe
rqc.exe
rqs.exe
setprinter.exe
showacls.exe
showperf.exe
showpriv.exe
sleep.exe
sonar.exe
splinfo.exe
srvany.exe
srvcheck.exe
srvinfo.exe
srvmgr.exe
ssdformat.exe
subinacl.exe
tail.exe
tccom.exe
tcmon.exe
timeit.exe
timezone.exe
tsctst.exe
tsscalling.exe
uddicatschemeeditor.exe
uddiconfig.exe
uddidataexport.exe
usrmgr.exe
vadump.exe
vfi.exe
volperf.exe
volrest.exe
vrfydsk.exe
winhttpcertcfg.exe
winhttptracecfg.exe
winpolicies.exe
```
