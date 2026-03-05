---
layout: post
title: "Could not chdir to home directory /home/nsc: Permission denied"
date: 2013-12-12 22:19:00 +0000
tags: ["adimistration", "unix"]
blogger_orig_link: https://lexxai.blogspot.com/2013/12/could-not-chdir-to-home-directory.html
---

Could not chdir to home directory /home/nsc: Permission denied /bin/bash: Permission denied Connection to 192.168.1.13 closed. After creating lxc container, I wasn’t able to connect to it as a simple user. Also couldn’t change to simple user using “su”.  
  
  
All I got was an error:
  

```
Could not chdir to home directory /home/nsc: Permission denied
/bin/bash: Permission denied
Connection to 192.168.1.13 closed.
```

All permissions of home directory were correct, as to be 100% sure, deleted user and created again. Still the same problem.  
  
The problem was / permissions.  
It was 700, so simple user couldn’t change to any directory.  
  
Small fix to make it working (with root user)  

```
chmod 755/
```

And that’s it, user is working!!!  
  
<http://loginroot.com/could-not-chdir-to-home-directory-homensc-permission-denied-binbash-permission-denied-connection-to-192-168-1-13-closed/>
