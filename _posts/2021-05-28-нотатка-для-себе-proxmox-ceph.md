---
layout: post
title: "Нотатка для себе.  Proxmox Ceph"
date: 2021-05-28 22:39:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/05/proxmox-ceph.html
---

### Ceph destroy

```
#rm -rf /etc/systemd/system/ceph*  
#killall -9 ceph-mon ceph-mgr ceph-mds  
#rm -rf /var/lib/ceph/mon/  /var/lib/ceph/mgr/  /var/lib/ceph/mds/  
#pveceph purge  
#apt purge ceph-mon ceph-osd ceph-mgr ceph-mds  
#rm /etc/init.d/ceph
```

### LVM

```
#pvdisplay  
File descriptor 7 (pipe:[1486785]) leaked on pvdisplay
  invocation. Parent PID 382397: bash  
  --- Physical volume ---  
 
  PV
  Name              
  /dev/sdc  
  VG
  Name              
  ceph-adc898bc-44ea-43a1-8279-964e06acc8d5  
  PV
  Size              
  931.51 GiB / not usable 1.71 MiB  
 
  Allocatable           yes
  (but full)  
  PE
  Size              
  4.00 MiB  
  Total
  PE             
  238467  
  Free
  PE              
  0  
  Allocated
  PE          238467  
  PV
  UUID              
  3LBrIs-Eh8t-wljP-WDMv-DxiX-CKAd-vXCyHf
```

```
#vgremove ceph-adc898bc-44ea-43a1-8279-964e06acc8d5  
File descriptor 7
  (pipe:[1486785]) leaked on vgremove invocation. Parent PID 382397: bash  
Do
  you really want to remove volume group
  "ceph-adc898bc-44ea-43a1-8279-964e06acc8d5" containing 1 logical volumes?
  [y/n]: y  
Do you really want to remove and DISCARD active logical volume
  ceph-adc898bc-44ea-43a1-8279-964e06acc8d5/osd-block-ca1b00e7-bafc-4c7a-97aa-a83bcd784173?
  [y/n]: y  
  Logical volume
  "osd-block-ca1b00e7-bafc-4c7a-97aa-a83bcd784173" successfully removed  
 
  Volume group "ceph-adc898bc-44ea-43a1-8279-964e06acc8d5" successfully removed
```

```
#pvremove   /dev/sdc  
File descriptor 7 (pipe:[1486785]) leaked
  on pvremove invocation. Parent PID 382397: bash  
  Labels on physical
  volume "/dev/sdc" successfully wiped.
```

### OSD

```
#pveceph osd create /dev/sdc  
unable to open file
  '/var/lib/ceph/bootstrap-osd/ceph.keyring.tmp.191200' - No such file or
  directory
#mkdir /var/lib/ceph/bootstrap-osd
```

```
#pveceph osd create /dev/sdc  
create
  OSD on /dev/sdc (bluestore)  
wipe disk/partition: /dev/sdc  
200+0
  records in  
200+0 records out  
209715200 bytes (210 MB, 200 MiB)
  copied, 1.54936 s, 135 MB/s  
Running command: /bin/ceph-authtool
  --gen-print-key  
Running command: /bin/ceph --cluster ceph --name
  client.bootstrap-osd --keyring /var/lib/ceph/bootstrap-osd/ceph.keyring -i -
  osd new 815084bd-ee2c-4b7e-beab-39c76ca8e95f  
Running command:
  /sbin/vgcreate --force --yes ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493
  /dev/sdc  
 stdout: Physical volume "/dev/sdc" successfully
  created.  
 stdout: Volume group
  "ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493" successfully created  
Running
  command: /sbin/lvcreate --yes -l 238467 -n
  osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f
  ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493  
 stdout: Logical volume
  "osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f" created.  
Running
  command: /bin/ceph-authtool --gen-print-key  
Running command: /bin/mount
  -t tmpfs tmpfs /var/lib/ceph/osd/ceph-1  
--> Executable selinuxenabled
  not in PATH: /sbin:/bin:/usr/sbin:/usr/bin  
Running command: /bin/chown -h
  ceph:ceph
  /dev/ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493/osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f  
Running
  command: /bin/chown -R ceph:ceph /dev/dm-5  
Running command: /bin/ln -s
  /dev/ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493/osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f
  /var/lib/ceph/osd/ceph-1/block  
Running command: /bin/ceph --cluster ceph
  --name client.bootstrap-osd --keyring /var/lib/ceph/bootstrap-osd/ceph.keyring
  mon getmap -o /var/lib/ceph/osd/ceph-1/activate.monmap  
 stderr:
  2021-05-28T14:36:51.481-0700 7f3cc29d7700 -1 auth: unable to find a keyring on
  /etc/pve/priv/ceph.client.bootstrap-osd.keyring: (2) No such file or
  directory  
2021-05-28T14:36:51.481-0700 7f3cc29d7700 -1
  AuthRegistry(0x7f3cbc0596e0) no keyring found at
  /etc/pve/priv/ceph.client.bootstrap-osd.keyring, disabling cephx  
 stderr:
  got monmap epoch 2  
Running command: /bin/ceph-authtool
  /var/lib/ceph/osd/ceph-1/keyring --create-keyring --name osd.1 --add-key
  AQByYrFgYXVgEhAAPGxq/77NPiOynKFYsVdtqQ==  
 stdout: creating
  /var/lib/ceph/osd/ceph-1/keyring  
added entity osd.1
  auth(key=AQByYrFgYXVgEhAAPGxq/77NPiOynKFYsVdtqQ==)  
Running command:
  /bin/chown -R ceph:ceph /var/lib/ceph/osd/ceph-1/keyring  
Running command:
  /bin/chown -R ceph:ceph /var/lib/ceph/osd/ceph-1/  
Running command:
  /bin/ceph-osd --cluster ceph --osd-objectstore bluestore --mkfs -i 1 --monmap
  /var/lib/ceph/osd/ceph-1/activate.monmap --keyfile - --osd-data
  /var/lib/ceph/osd/ceph-1/ --osd-uuid 815084bd-ee2c-4b7e-beab-39c76ca8e95f
  --setuser ceph --setgroup ceph  
 stderr: 2021-05-28T14:36:51.874-0700
  7fdeded2de00 -1 bluestore(/var/lib/ceph/osd/ceph-1/) _read_fsid unparsable
  uuid  
 stderr: 2021-05-28T14:36:52.158-0700 7fdeded2de00 -1 freelist
  read_size_meta_from_db missing size meta in DB  
--> ceph-volume lvm
  prepare successful for: /dev/sdc  
Running command: /bin/chown -R ceph:ceph
  /var/lib/ceph/osd/ceph-1  
Running command: /bin/ceph-bluestore-tool
  --cluster=ceph prime-osd-dir --dev
  /dev/ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493/osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f
  --path /var/lib/ceph/osd/ceph-1 --no-mon-config  
Running command: /bin/ln
  -snf
  /dev/ceph-4f3d41bc-22ec-42bb-95fa-ca4c9d278493/osd-block-815084bd-ee2c-4b7e-beab-39c76ca8e95f
  /var/lib/ceph/osd/ceph-1/block  
Running command: /bin/chown -h ceph:ceph
  /var/lib/ceph/osd/ceph-1/block  
Running command: /bin/chown -R ceph:ceph
  /dev/dm-5  
Running command: /bin/chown -R ceph:ceph
  /var/lib/ceph/osd/ceph-1  
Running command: /bin/systemctl enable
  ceph-volume@lvm-1-815084bd-ee2c-4b7e-beab-39c76ca8e95f  
 stderr:
  Created symlink
  /etc/systemd/system/multi-user.target.wants/ceph-volume@lvm-1-815084bd-ee2c-4b7e-beab-39c76ca8e95f.service
  -> /lib/systemd/system/ceph-volume@.service.  
Running command:
  /bin/systemctl enable --runtime ceph-osd@1  
 stderr: Created symlink
  /run/systemd/system/ceph-osd.target.wants/ceph-osd@1.service ->
  /lib/systemd/system/ceph-osd@.service.  
Running command: /bin/systemctl
  start ceph-osd@1  
--> ceph-volume lvm activate successful for osd ID:
  1  
--> ceph-volume lvm create successful for: /dev/sdc
```

### MDS

Create MDS  

```
#pveceph mds create  
creating MDS directory
  '/var/lib/ceph/mds/ceph-ns231'  
creating keys for 'mds.ns231'  
setting
  ceph as owner for service directory  
enabling service
  'ceph-mds@ns231.service'  
Created symlink
  /etc/systemd/system/ceph-mds.target.wants/ceph-mds@ns231.service ->
  /lib/systemd/system/ceph-mds@.service.  
starting service
  'ceph-mds@ns231.service'
```

Destroy MDS  

```
#pveceph mds destroy ns231  
disabling service
  'ceph-mds@ns231.service'  
Removed
  /etc/systemd/system/ceph-mds.target.wants/ceph-mds@ns231.service.  
stopping
  service 'ceph-mds@ns231.service'  
removing ceph-mds directory
  '/var/lib/ceph/mds/ceph-ns231'  
removing ceph auth for 'mds.ns231'
```

### ceph-volume

```
ceph-volume lvm activate --all
```

Додавти попердні OSD до монітору.

```
#ceph auth add osd.0 osd 'allow *' mon 'allow rwx' -i /var/lib/ceph/osd/ceph-0/keyring
added key for osd.1
#ceph-volume lvm activate --all
```

Export keyring for osd on cluster member. If error is on create new OSD:
stderr: [errno 13] RADOS permission denied (error connecting to the cluster)
--> RuntimeError: Unable to create a new OSD id

```
#ceph auth get client.bootstrap-osd > /var/lib/ceph/bootstrap-osd/ceph.keyring
exported keyring for client.bootstrap-osd
```

....
