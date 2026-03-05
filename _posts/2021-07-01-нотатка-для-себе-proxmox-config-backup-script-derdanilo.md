---
layout: post
title: "Нотатка для себе. Proxmox config backup script  DerDanilo"
date: 2021-07-01 20:40:00 +0000
tags: ["backup", "Proxmox", "Virtualization"]
blogger_orig_link: https://lexxai.blogspot.com/2021/07/proxmox-config-backup-script-derdanilo.html
---

Proxmox  має вбудований механізм створення  резервних копій образів
віртуальних машин, але копія налаштувань самого гіпервізору відсутня в
community version.

Тому спільнота створює власні механізми, один з них проєкт від
DerDanilo: 
<https://github.com/DerDanilo/proxmox-stuff>

[![](/assets/images/blog/a061ec5b7b1168cb-1ecdf06c34515174.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjFtKZiZoEQ6J1kkbJ3ixSYT0sY_RLTrr5IynKb14A7Pa687h6Dt8lFO6mZskO251ucqj9I3w4ZD4Smw2g9ZAU4V4FhMcqjmd3jxKos54MX99-TWgoxzArvLUk2shJFn9eHu8eRmremSYC/s561/prxmox-backup.PNG)

  

Де є скрипт резервного копіювання основних файлів до архівної теки,  і
опис як відновити.

Ось його копія:

```
#!/bin/bash
# Version	      0.2.2 - BETA ! !
# Date		      02.20.2020
# Author 	      DerDanilo 
# Contributors    aboutte, xmirakulix, bootsie123

# set vars

# always exit on error
set -e

# permanent backups directory
# default value can be overridden by setting environment variable before running prox_config_backup.sh
# example: export BACK_DIR="/mnt/pve/media/backup
_bdir=${BACK_DIR:-/mnt/backups/proxmox}

# number of backups to keep before overriding the oldest one
MAX_BACKUPS=5

# temporary storage directory
_tdir=${TMP_DIR:-/var/tmp}

_tdir=$(mktemp -d $_tdir/proxmox-XXXXXXXX)

function clean_up {
    echo "Cleaning up"
    rm -rf $_tdir
}

# register the cleanup function to be called on the EXIT signal
trap clean_up EXIT

# Don't change if not required
_now=$(date +%Y-%m-%d.%H.%M.%S)
_HOSTNAME=$(hostname -f)
_filename1="$_tdir/proxmoxetc.$_now.tar"
_filename2="$_tdir/proxmoxpve.$_now.tar"
_filename3="$_tdir/proxmoxroot.$_now.tar"
_filename4="$_tdir/proxmoxcron.$_now.tar"
_filename5="$_tdir/proxmoxvbios.$_now.tar"
_filename6="$_tdir/proxmoxpackages.$_now.list"
_filename_final="$_tdir/proxmox_backup_"$_HOSTNAME"_"$_now".tar.gz"

##########

function description {
    clear
    cat </dev/null)" != "" ] ; then
	echo backing up custom video bios...
	tar --warning='no-file-ignored' -cvPf "$_filename5" /usr/share/kvm/*.vbios
    fi
    # copy installed packages list
    echo "Copying installed packages list from APT"
    apt-mark showmanual | tee "$_filename6"
}

function compressandarchive {
    echo "Compressing files"
    # archive the copied system files
    tar -cvzPf "$_filename_final" $_tdir/*.{tar,list}

    # copy config archive to backup folder
    # this may be replaced by scp command to place in remote location
    cp $_filename_final $_bdir/
}

function stopservices {
    # stop host services
    for i in pve-cluster pvedaemon vz qemu-server; do systemctl stop $i ; done
    # give them a moment to finish
    sleep 10s
}

function startservices {
    # restart services
    for i in qemu-server vz pvedaemon pve-cluster; do systemctl start $i ; done
    # Make sure that all VMs + LXC containers are running
    qm startall
}

##########


description
are-we-root-abort-if-not
check-num-backups

# We don't need to stop services, but you can do that if you wish
#stopservices

copyfilesystem

# We don't need to start services if we did not stop them
#startservices

compressandarchive
```
