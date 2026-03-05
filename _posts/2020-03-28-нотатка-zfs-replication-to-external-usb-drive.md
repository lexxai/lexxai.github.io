---
layout: post
title: "Нотатка: zfs replication to external usb drive"
date: 2020-03-28 22:39:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2020/03/zfs-replication-to-external-usb-drive.html
---

[![](/assets/images/blog/c75b247ee95b0105-8f49c72fdef53ad3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCPKiVjeA8-_Zo0oJ4kXx7zk-1ZTx-EWdIGx9ciL5AB1XFGLIBr4kRBKjvH8yaGxBQyMKbwufLIhXMzzy5PLxLLb6X9cgfddGC5TZR62y3maGMY_OtkYlIv83acOnxVPi1n2Xpj7gLBbIH/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-03-28+22-56-06.png)

Нотатка для себе, так я роблю реплікацію з зовнішнім USB накопичувачем у [FreeNAS](http://www.freenas.org/).  
Зовнішній USB накопичувач монтується безпосередньо перед реплікацією, і розмонтовується після реплікації.  
Зовнішній диск монтується за GPT ідентифікацією диску - GPTID (zpool list -v extusb-01), та ідентифікацією zfs pool за POOL\_GUID (zpool list -o guid extusb-01).  
Зовнішній диск був створений майстром створення zfs pool у FreeNAS.  
Скрипт /root/script/backup-2-extusb-01-next.sh  запускається за розкладом через cron.  
  
  

#### backup-2-extusb-01-next.sh

```
#!/bin/sh

GPTID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
POOL_GUID=yyyyyyyyyyyyyyyyyyy
EXTPOOL="extusb-01"
MNTPOINT="/mnt"
SRCPOOL="pool"
SRCDATASETS=""  
#use skipdatasets via | separator  
SRCDATASETSSKIP="^pool/SOME|^pool/SOMEOTHER"  
DSTDATASETROOT=${EXTPOOL}/backup
SAVEOLDSNAPHOTS=10
   
/root/script/mount_ext.sh
 
zpool status -x ${EXTPOOL}
if [ $? -gt 0 ];then
 echo "Try import ${EXTPOOL}"
 zpool import ${EXTPOOL}  -R ${MNTPOINT}
 echo "result:" $?
 zpool status -x ${EXTPOOL}
 if [ $? -gt 0 ];then
  echo "can't import pool ${EXTPOOL}"
  exit
 fi
fi

echo backup start... $(date)

SRCDATASETS=$(zfs list -H -o name | egrep "^${SRCPOOL}" | egrep -v "${SRCDATASETSSKIP}")

SNAPDATE=$(date +%Y%m%d%H%M%S)
SNAPNAME1=snap_${EXTPOOL}_
SNAPNAME=${SNAPNAME1}${SNAPDATE}

echo LIST of ${SRCPOOL}
SRCPOOLLIST=$(zfs list -H  -o name |egrep "^${SRCPOOL}/")

for sppool in ${SRCPOOLLIST}; do
echo ------
echo sppool ${sppool}
datasetpath=$(echo ${sppool} | sed -e "s/^${SRCPOOL}\///g")
echo datasetpath ${datasetpath}
SRCSNAPLIST=$(zfs list -H -s creation -o name -t snapshot | egrep "^${SRCPOOL}/" | grep ${sppool}@${SNAPNAME1} | tail -1)
SRCSNAPNAME=$(echo ${SRCSNAPLIST} | awk -F@ '{print $2}')
echo SSNAP ${SRCSNAPLIST} @ ${SRCSNAPNAME}
DSTSNAPLIST=$(zfs list -H  -s creation  -o name -t snapshot | egrep "^${EXTPOOL}/" | grep "${datasetpath}@${SNAPNAME1}" | tail -1)
DSTSNAPNAME=$(echo ${DSTSNAPLIST} | awk -F@ '{print $2}')
echo DSNAP  ${DSTSNAPLIST} @ ${DSTSNAPNAME}

if [ "${SRCSNAPNAME}" == "${DSTSNAPNAME}" ];then
 zfs snapshot ${sppool}@${SNAPNAME}
 zfs hold keep ${sppool}@${SNAPNAME}
 if [ "$?" -eq 0 ];then
  echo incremental send  -i ${SRCSNAPNAME}  ${sppool}@${SNAPNAME} ,  receive -F ${DSTDATASETROOT}/${datasetpath}
  zfs send -i ${SRCSNAPNAME}  ${sppool}@${SNAPNAME} | zfs receive -F ${DSTDATASETROOT}/${datasetpath}
  if [ "$?" -eq 0 ];then
   echo destroy old snap "${sppool}@${SRCSNAPNAME}"
   zfs release keep "${sppool}@${SRCSNAPNAME}"
   zfs destroy "${sppool}@${SRCSNAPNAME}"
   echo result $?
  fi
 fi
else
 echo "***************************************************"
 echo  src pool: ${sppool} SNAP  NOT EQ "${SRCSNAPNAME}" and "${DSTSNAPNAME}"
 echo "***************************************************"
 sleep 15
 echo "** DO FIRST COPY **********************************************"
 echo "SNAP zfs snapshot ${sppool}@${SNAPNAME}"
 zfs snapshot ${sppool}@${SNAPNAME}
 echo "HOLD zfs hold keep ${sppool}@${SNAPNAME}"
 zfs hold keep ${sppool}@${SNAPNAME}
 echo "DESTROY DEST zfs  destroy - ${DSTDATASETROOT}/${datasetpath} "
 zfs destroy -r ${DSTDATASETROOT}/${datasetpath}
 echo "SEND zf send  ${sppool}@${SNAPNAME} | zfs receive  ${DSTDATASETROOT}/${datasetpath}"
 zfs send  ${sppool}@${SNAPNAME} | zfs receive  ${DSTDATASETROOT}/${datasetpath}
 echo "***************************************************"
 sleep 15
fi

#destroy old snapshots
DATASETOLDSNAP=${DSTDATASETROOT}/${datasetpath}
echo try remove old snapshots on: ${DATASETOLDSNAP}  saving: ${SAVEOLDSNAPHOTS}
zfs list -H -t snapshot -o name -S creation -r ${DATASETOLDSNAP} | grep ^${DATASETOLDSNAP}@   | tail +${SAVEOLDSNAPHOTS} | xargs -n 1 zfs destroy

done

SRCSNAPLIST=$(zfs list -H  -s creation -o name -t snapshot |egrep "^${SRCPOOL}/" |grep ${SNAPNAME1})

echo LIST of $EXTPOOL
zfs list -H -o name -t snapshot |egrep "^${EXTPOOL}/" |grep ${SNAPNAME1}

/root/script/unmount_ext.sh

echo backup done... $(date)
exit
```

#### /root/script/mount\_ext.sh

Скрипт виконує монтування зашифрованого тому через GELI.

Ключ диску зберігається у теці /root/.key з іменем geli-${EXTPOOL}.key, де EXTPOOL ім'я зовнішнього zfs pool.

```
#!/bin/sh

GPTID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
POOL_GUID=yyyyyyyyyyyyyyyyyyy
EXTPOOL="extusb-01"

geli_device="gptid/${GPTID}"
geli_flags="-k /root/.key/geli-${EXTPOOL}.key -p"

MNTPOINT="/mnt"
SRCPOOL="pool"
echo GELI ATTACH
geli attach ${geli_flags} ${geli_device}
echo "result:" $?

zpool status -x ${EXTPOOL}
if [ $? -gt 0 ];then
 echo "Try import ${EXTPOOL}"
 zpool import ${EXTPOOL} -R ${MNTPOINT}
 echo "result:" $?
 zpool status -x ${EXTPOOL}
 if [ $? -gt 0 ];then
  echo "can't import pool ${EXTPOOL}"
  exit
 fi
fi

exit
```

#### /root/script/unmount\_ext.sh

```
#!/bin/sh

GPTID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
POOL_GUID=yyyyyyyyyyyyyyyyyyy
EXTPOOL="extusb-01"

geli_device="gptid/${GPTID}"

MNTPOINT="/mnt"
SRCPOOL="poolz2"

snapshots=$(zfs list -t snapshot | grep ^${EXTPOOL}/backup/BACKUP@snap_${EXTPOOL} | wc -l)
zpool list ${EXTPOOL} | mail -s "Ext USB ZFS pool "${EXTPOOL}" - used space, snaphots: ${snapshots}" postmaster@email

echo "Try export ${EXTPOOL}"
zpool export ${EXTPOOL}
echo "result:" $?

echo GELI DETTACH
geli detach ${geli_device}
echo "result:" $?
exit
```

#### Засипання зовнішнього диску

Я використовую Seagate Expansion 10TB для зовнішнього диску. І стала потреба щоб він вимикався через певний час після використання.   

[![](/assets/images/blog/8c424cb517915fb0-ef64115b1cc4642d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiewDHbXFCf1nfMbhsh6urTLO2D0Yx_XAzHLuYKFxmTNheQzAGZH6raR7E8KA_fAAbdFTANe7vqtdObcIQnn6EOdeZSrnRSbh052xXx_htDdIA6jEpgME5jxCv9nPdhS6m8A39bktJeFTCq/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-03-28+23-19-57.png)  
*Seagate Expansion 10TB*

Стандартні рішення від опреаційної системи не дають результату, томущо виробник забокував цю можливість. І рішення тільки використтаи спеціальну программу від Seagate для налаштування параметрів зовнішніх дисків, на зараз не помню вже назви, але з під Windows прийшлось це робити.  
І тількт так зараз цей накопичувач засипає після завершення реплікації автоматично.  

#### Зачистка

Всяк буває і інколи треба зачисити snapshots @snap\_${EXTPOOL}  котрі "залипли".  
Цей скрипт залишає тільку одну останню версію snapshots @snap\_${EXTPOOL} , і якщо вона ще є зблокованна від видалення через zfs hold, тоді snapshot розблокується перед видаленням.  
  
/root/script/clear\_holded\_snapshots.sh
  

```
#!/bin/sh

SRCPOOL="pool"
EXTPOOL="extusb-01"
HOLDTAG="keep"

datas=$(zfs list -H  -o name -r ${SRCPOOL}  )

for data in ${datas}; do
 echo *${data}
 snapshots=$(zfs list -H  -S creation -o name -t snapshot -r ${data} | grep ^${data}@snap_${EXTPOOL} | tail -n +2)
 for sn in ${snapshots}; do
#   echo ${sn}
   holded=$(zfs holds -H ${sn} | grep ${HOLDTAG})
   if [ ! -z "${holded}" ];then
      echo HOLDED ${sn}, releasing
      zfs release ${HOLDTAG} ${sn}
   fi
   echo zfs destroy ${sn}
   zfs destroy ${sn}
 done
done
```

#### S.M.A.R.T. test

Так як диск майже завжи вимкенно, то можна після кожної реплікації виконати перевірку диску через наступний скрипт.  

```
#!/bin/sh

GPTID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
disk=$(glabel status | grep ${GPTID}| awk '{print $3}' | awk -Fp '{print $1}')
#echo ${disk}
smartctl -q errorsonly -t short /dev/${disk}
```
