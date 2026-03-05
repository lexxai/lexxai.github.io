---
layout: post
title: "Нотатка для себе,  Proxmox QEMU agent для VM - запуск команд"
date: 2021-12-10 22:29:00 +0000
tags: ["administration", "Proxmox", "root", "script", "security"]
blogger_orig_link: https://lexxai.blogspot.com/2021/12/proxmox-qemu-agent-vm.html
---

Як взаємодіє гіпервізор з віртуальними машинами ?   
Один з варіантів використати агент для віртуальної машини.

У Proxmox це [qemu-agent](https://pve.proxmox.com/wiki/Qemu-guest-agent) [1],  від існує для різних операційних систем.  
Так для FreeBSD існує версія пакунку "[qemu-guest-agent-5.0.1](https://www.freshports.org/emulators/qemu-guest-agent)  QEMU guest-agent utilities", з функціоналом але дещо обмеженим.

Для роботи з агентом є команда  qm guest.  
  
 qm guest cmd <vmid> <command>  
 qm guest exec-status <vmid> <pid>  
 qm guest passwd <vmid> <username> [OPTIONS]  
 qm guest exec <vmid> [<extra-args>] [OPTIONS]

[Manual proxmox qm](https://pve.proxmox.com/pve-docs/qm.1.html) :  
**qm guest cmd <vmid> <command>**  
Execute Qemu Guest Agent commands.  
<vmid>: <integer> (1 - N) -  The (unique) ID of the VM.  
<command>: <fsfreeze-freeze | fsfreeze-status | fsfreeze-thaw | fstrim | get-fsinfo | get-host-name | get-memory-block-info | get-memory-blocks | get-osinfo | get-time | get-timezone | get-users | get-vcpus | info | network-get-interfaces | ping | shutdown | suspend-disk | suspend-hybrid | suspend-ram>  
  
The QGA command.  
  
**qm guest exec <vmid> [<extra-args>] [OPTIONS]**  
Executes the given command via the guest agent  
<vmid>: <integer> (1 - N) - The (unique) ID of the VM.  
<extra-args>: <array>   Extra arguments as array   
--pass-stdin <boolean> (default = 0)  
  
When set, read STDIN until EOF and forward to guest agent via input-data (usually treated as STDIN to process launched by guest agent). Allows maximal 1 MiB.  
--synchronous <boolean> (default = 1)  
  
If set to off, returns the pid immediately instead of waiting for the commmand to finish or the timeout.  
--timeout <integer> (0 - N) (default = 30)  
  
The maximum time to wait synchronously for the command to finish. If reached, the pid gets returned. Set to 0 to deactivate  
  
**qm guest exec-status <vmid> <pid>**  
Gets the status of the given pid started by the guest-agent  
<vmid>: <integer> (1 - N) -   The (unique) ID of the VM.  
<pid>: <integer> -  The PID to query  
  
**qm guest passwd <vmid> <username> [OPTIONS]**  
Sets the password for the given user to the given password  
<vmid>: <integer> (1 - N) -  The (unique) ID of the VM.  
<username>: <string>  -   The user to set the password for.  
--crypted <boolean> (default = 0)  
    set to 1 if the password has already been passed through crypt()

Запитаємо функціонал агента в FreeBSD обмеження мають статус disabled:

```
qm guest cmd 113 info
{
   "supported_commands" : [
      {
         "enabled" : true,
         "name" : "guest-get-osinfo",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-timezone",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-users",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-host-name",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-exec",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-exec-status",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-get-memory-block-info",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-set-memory-blocks",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-get-memory-blocks",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-set-user-password",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-fsinfo",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-set-vcpus",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-vcpus",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-network-get-interfaces",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-suspend-hybrid",
         "success-response" : false
      },
      {
         "enabled" : false,
         "name" : "guest-suspend-ram",
         "success-response" : false
      },
      {
         "enabled" : false,
         "name" : "guest-suspend-disk",
         "success-response" : false
      },
      {
         "enabled" : false,
         "name" : "guest-fstrim",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-fsfreeze-thaw",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-fsfreeze-freeze-list",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-fsfreeze-freeze",
         "success-response" : true
      },
      {
         "enabled" : false,
         "name" : "guest-fsfreeze-status",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-flush",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-seek",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-write",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-read",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-close",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-file-open",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-shutdown",
         "success-response" : false
      },
      {
         "enabled" : true,
         "name" : "guest-info",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-set-time",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-get-time",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-ping",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-sync",
         "success-response" : true
      },
      {
         "enabled" : true,
         "name" : "guest-sync-delimited",
         "success-response" : true
      }
   ],
   "version" : "5.0.1"
}
```

Виконаємо команду для  отримання змісту файлу від імені "root" !!!

[![](/assets/images/blog/3832b00c0cdf269f-a99d84690e020156.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0uZ93lz7hilN6dLyusHRb-OwTfvOJbwRn99PEP1V9CQouLQLwTXeZ82C7rrV8JHyDGrxQs3i3WVZPZAp-6pxtBfrUdSJ6rPAYGLApoUMXg_kgTYvzzxj-epVpNpIVdFVfekspmyzXmmpQ/s1012/Screenshot+2021-12-10+234002.png)  
*qm guest exec 113 -- sh -c "cat /root/test.txt"*

Так, якщо використати при створенні віртуальної машини параметр:  "[--hookscript](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_hook_scripts) <string>    Script that will be executed during various steps in the vms lifetime", то можна додатково контролювати віртуальні машини через агент.

Приклад скрипту: */usr/share/doc/pve-manager/examples/vzdump-hook-script.pl* 

Але використання агента віртуальної машини в неконтрольованому середовищі може бути не зовсім безпечно, коли до гіпервізора має доступ хакер.

 За матеріалами:

1. [Proxmox Server Solutions - qm - Qemu/KVM Virtual Machine Manager](https://pve.proxmox.com/pve-docs/qm.1.html)
2. [[SOLVED] - Run guest agent commands on linux VM | Proxmox Support Forum](https://forum.proxmox.com/threads/run-guest-agent-commands-on-linux-vm.85000/ )
