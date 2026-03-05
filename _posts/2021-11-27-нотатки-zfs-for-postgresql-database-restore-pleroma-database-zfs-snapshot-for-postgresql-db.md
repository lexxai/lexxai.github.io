---
layout: post
title: "Нотатки: ZFS for postgresql database, Restore pleroma database, ZFS Snapshot for postgresql db"
date: 2021-11-27 00:47:00 +0000
tags: ["administration", "FreeBSD", "pleroma", "postgresql", "script"]
blogger_orig_link: https://lexxai.blogspot.com/2021/11/zfs-for-postgresql-database-restore.html
---

FreeBSD, Postgress SQL Database v14.  
Відновлення бази даних Pleroma на сервері з резервної копії.

```
pkg install postgresql14-server
pkg install postgresql14-contrib
zfs create -o mountpoint=none zroot/var/db
zfs create -o mountpoint=/var/db/postgres
zroot/var/db/postgres
zfs list
zfs zfs set atime=off zroot/var/db/postgres
zfs set atime=off zroot/var/db/postgres
zfs set relatime=on zroot/var/db/postgres
zfs set recordsize=8k zroot/var/db/postgres
chown -R postgres:postrgres /var/db/postgres
chmod 750 /var/db/postgres
service postgresql initdb
pkg install sudo
```

restore\_db.sh

```
#!/usr/bin/env sh  
DBSAVE=.
#DBFILE=pleroma_2021-11-09-0000.sql.gz
sudo -u postgres psql < pleroma_init.sql
cd ${DBSAVE}
DBFILE=$( ls -1 *.sql.gz | tail -1 )
echo ${DBFILE}
zcat ${DBSAVE}/${DBFILE} | sudo -u postgres psql -d pleroma
```

pleroma\_init.sql:

```
REVOKE CONNECT ON DATABASE  pleroma FROM public;
SELECT pg_terminate_backend(pg_stat_activity.pid)
 FROM pg_stat_activity
 WHERE pg_stat_activity.datname = 'pleroma';
CREATE USER pleroma WITH ENCRYPTED PASSWORD '.......................................';
DROP DATABASE pleroma;
CREATE DATABASE pleroma OWNER pleroma;
\c pleroma;
--Extensions made by ecto.migrate that need superuser access
CREATE EXTENSION IF NOT EXISTS citext;
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
GRANT CONNECT ON DATABASE pleroma TO public;
```

zfs-snapshot-pgsql.sh

```
#!/usr/bin/env sh
DATASQL=data14
DATABASE=/var/db/postgres/${DATASQL}
DBDATASET=zroot/var/db/postgres
DBDATASET_CLONE=zroot/var/db/postgres-clonedb
DATABASE_CLONE=/var/db/postgres-clonedb
SHAREDSQL=/usr/local/share/postgresql
zfs list -t snapshot
zfs destroy ${DBDATASET}@snap1
touch ${DATABASE}/backup_in_progress
#START BACKUP SQL by SQL SNAPSHOT
sql="SELECT pg_start_backup('snapshot', true);"
sudo -u postgres psql -c "$sql"
#Fix state of DATABSE DIRECTORY to ZFS snapshot
zfs snapshot ${DBDATASET}@snap1
sql="SELECT pg_stop_backup();"
sudo -u postgres psql -c "$sql"
rm ${DATABASE}/backup_in_progress
#STOP BACKUP SQL by SQL SNAPSHOT
zfs list -t snapshot 
#CLONE ZFS SNAPSHOT to new SQL DATABSE DIRECTORY
zfs clone -o mountpoint=${DATABASE_CLONE} ${DBDATASET}@snap1 ${DBDATASET_CLONE}
#Backup by file archive
tar -cf ~/backup.tar ${DATABASE_CLONE}
#Backup by pg_dump to SQL by start tmp instance of pgsql on port 5433
rm ${DATABASE_CLONE}/${DATASQL}/postmaster.*
cp ${SHAREDSQL}/postgresql.conf.sample ${DATABASE_CLONE}/${DATASQL}/postgresql.conf
sudo -u postgres pg_ctl -D ${DATABASE_CLONE}/${DATASQL} -o "-p 5433" start
umask 077
sudo -u postgres pg_dump pleroma -p 5433 --clean --create | gzip >  pleroma_`date +%F-%H%M`.sql.gz
sudo -u postgres pg_ctl -D ${DATABASE_CLONE}/${DATASQL} stop -m immediate
umount  ${DATABASE_CLONE}
zfs destroy ${DBDATASET_CLONE}
```

[![](/assets/images/blog/7f01ce150a50ec41-b3b19c1500f6d1f4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxYqNLcfQbocLkWI7tmZP6E87tSmFvz7fEKCt5P0iyvwmR0Mx_q3FSJogi1m8ptpc9zLXCeFCSnvp32ZYgGVWZhtBtSsRkW_boOS83w5-_KS7TOhig1BljweOHAt4FaVweCxh9Luu5yhnT/s1121/Screenshot+2021-11-30+034147.png)

  

 Ref.:

* [PG Phriday: Postgres on ZFS | Ways ZFS Can Make Postgres an Even Better Database](https://www.2ndquadrant.com/en/blog/pg-phriday-postgres-zfs)
