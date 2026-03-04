---
layout: post
title: "FreeNAS-11.2-U2, Samba, VFS Object: default_quota."
date: 2019-02-22 19:26:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/02/freenas-112-u2-samba-vfs-object.html
---

Попередньо я вивчав як зробити реальні квоти для користувачів у [FreeNAS](https://freenas.org/):   
[Маємо те, що маємо. Усе що відбувається - на краще: FreeNAS, ZFS, User Quota, Samba.](https://lexxai.github.io/2018/04/freenas-zfs-user-quota-samba.html)  
  
Тепер задачу з реальним визначення квоти для користувача ZFS можемо зробити додавши [VFS Object: default\_quota](https://www.samba.org/samba/docs/current/man-html/vfs_default_quota.8.html) до необхідної спільної теки у налаштуваннях Sharing / SMB .  
[З документації FreeNAS](https://www.ixsystems.com/documentation/freenas/11.2/sharing.html#configuring-authenticated-access-with-local-users): default\_quota - Stores the default quotas that are reported to a windows client in the quota record of a user.   
  

[![](/assets/images/blog/c2116e21eb98f8b7-6a516b488bc5805b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEii7ryslpLeKCQM23JKHZ66e6gnhg6_iM9tBlz-PUmp-MZCZneItKmBWhntNbaLf30_Iv-S-uAOrF9koSu3lIDdj7dNxIidvuSAKj-3gPrhVF8-p4rsHipOs5zp6DgZ1EwHmuLkf6ij9P91/s1600/freenas_default_quota_samba.PNG)  
*Додавання VFS Object: default\_quota*

Перевіряємо поточне значення квоти для змонтованого диску Z: у ОС Windows 10.  

[![](/assets/images/blog/05891cceea06769e-1f76ba1efa39f854.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjYVDU7vG3OeCYm4oT7Mt46YnEMfWI0dnFJQutzJQR-oP7wm-pMACND0o3JG-wCVUw09jtVCB1Uvm4NrrLeM1potodzhJrJnwz5BzrDxrzVY8Kcfej5ISy4PNyuAiwoiipU-5xPxTe8smYh/s1600/freenas_default_quota_samba_users.PNG)  
*Квота 80 GB*

  
Встановлюю персональну квоту для певного користувача домену на zfs dataset.  

```
DEFAULT_QUOTA=65G
USERS_DATASET=poolz2/samba/users
LOGON=${DOMAINNAME}\\${USERNAME}
/sbin/zfs set "userquota@${LOGON}=${DEFAULT_QUOTA}" ${USERS_DATASET}
```

Перевіряємо нове значення квоти для  змонтованого диску Z: у ОС Windows 10.  

[![](/assets/images/blog/8ba8379a9af67e28-059560c4cb7ff731.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-PsjWc9jE5DAko8kqY5Lbu3rYsg3pit-o6ekrOW8kNOXwv-C9E09xr9w4GdGbHSzJzGsXIm63bkA-3qZcVldMHnd6UvVvkjzVGK_mIWoh4KlYQ8-w1MI5_778TkSnzutGrFaKLD66w0ZN/s1600/freenas_default_quota_samba_users_1.PNG)  
*Квота 65 GB*

P.S. Автор розробки доповнень до FreeNAS  коментує питання по цій темі:  
[CIFS/SMB - AD - Quotas and reported share size | iXsystems Community](https://www.ixsystems.com/community/threads/cifs-smb-ad-quotas-and-reported-share-size.46564/#post-514638)  

* What minimum VFS Objects should be used for worked users quota ?  

  only zfs\_space, only ixnas. And default\_quota it not need to use since 11.2-U2 ?

  - Only ixnas.
* You can set a base quota for all authenticated users by adding the following auxiliary parameter for your share: ixnas:base\_user\_quota = 10G. You can of course change this value. What happens is the first time a user connects to the share, samba will automatically set a user quota at 10GB for him.
* You can remove "zfsacl" from your vfs\_objects list. ixnas does the same thing.

[![](/assets/images/blog/2a132e4db831c7e5-dc718ade7dafa1e5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3FNF2UZh4LpC_SiMBo4hchh4PIYSNNG-_gfTVGAOoI3y87pWFq5qg069bgQkqrr5e-iQKoTI3KDxMXV2qMlvJ9rxVta83JqUKEvRJ-nsiHlb07y4rwFyDw9hmblfe_hXXH41uyaNBkNJU/s1600/samba-default-quota.PNG)  
*ixnas:base\_user\_quota*
