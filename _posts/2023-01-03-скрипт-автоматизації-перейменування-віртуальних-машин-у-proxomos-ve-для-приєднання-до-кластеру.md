---
layout: post
title: "Скрипт автоматизації перейменування віртуальних машин у Proxomos VE для приєднання до кластеру"
date: 2023-01-03 23:08:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/01/proxomos-ve.html
---

[![](/assets/images/blog/46d2ce094af73e0b-8900e8349e2b09b4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoApk02OJO5A5-N9faNK9c22l3Z7Sfwje8arSASKV0X0XKzUslu80qtQxAImqE75sAMxdE0ZsgGYkR4aElICL4QEQwkMNwE_MLjMX5un4y-SM2PxVNmx6ILiu2ZMuqn-h6irinUmw44DVYqzOfnp7JUhpHOrz67mu2zpJb_GeHYkhYyWkWwwCtH4GYUQ/s253/Screenshot%202023-01-04%20002424.png)

  

При переносі віртуальних машин з ноду в кластер необхідно мати пустий нод, і номера віртуальних машин що не конфліктують з іншими.

Для вирішення цієї задачі простіше і надійніше використати метод створення резервної копії і потім її відновлення в кластері.

Але я створив малий скрип що допомагає обійтися без цього методу.

Тому цей простий скрип перейменовує гуртом віртуальні машини з номерами 1ХХ на 6ХХ і де дані зберігаються на ZFS диску з назвою 'mdata'.

  

```
#!/usr/bin/env bash  
  
old=1  
new=6  
zfspool=mdata  
  
#rename zfpool disks of vm  
for i in $( zfs list -o name | grep "${zfspool}/vm-${old}" ); do  
  r=$(echo $i | sed "s/vm-${old}/vm-${new}/")  
  echo " zfs rename $i $r"  
  zfs rename $i $r  
done  
  
#rename disks on vm conf  
sed -i s/${zfspool}:vm-${old}/${zfspool}:vm-${new}/g /etc/pve/qemu-server/${old}*.conf  
  
  
#rename conf of vm  
for i in $( ls -1 /etc/pve/qemu-server/${old}*.conf); do  
  r=$(echo $i | sed -E "s/${old}(.*)\.conf/${new}\1\.conf/" )  
  echo "mv $i $r"  
  mv $i $r  
done  
       
```

Після операцій перейменувань переносимо файли конфігурацій з /etc/pve/qemu-server до резервної теки в домашньому каталозі /root/backup/vmconfigs.

[![](/assets/images/blog/647eb8770ac7b354-afc047f55ad5cb3e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_582sOuA96td8HRVcv2nR9KFQGWkzzkurGaUXuOyXgGYsRgNKKhlD-uhOkSGOp-x36bBPEzCoxycNbOzdiudzNHiGuPniUz4CW2s31eH6-gGFmvwdv2JD-gikCtUsfmrMEKfLSsYofGe2EPg3Y_IH7o5wZk4XkJogNztNZlOPElV7Ix_YSyxhiTYihw/s379/Screenshot_20230104_123303.png)  
*Очищений нод ns26 у Proxmox VE*

Далі створюється кластер  на базі іншого чистого ноду  ns25. Де використовується два мережеві інтерфейси один для керування VM (10.5.0.25) інший для операцій в кластері (10.10.10.25).

P.S. Так як мережева карта з чотирьох портів, то окремий інтерфейс також використовується для самих віртуальних машин, що дає ізоляцію панелі керування від адресного простору віртуальних машин.

[![](/assets/images/blog/5503ba81b749e526-a3aff2d90eee8fed.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXSpZBHLVEDDBWlCTusVrEhhsON7zxipMpCTGMXoVKls7jhlnG3eS2O-p2Qe0JKYv8AThgditROZFHAr0OpkFYJDyU-HYYCxbcAHbBbGnA7rsbH-tutrWRWWaLUJxx1LHE1xI4QuwPwrL44AN9lXZCtAwlgC8w8WlQkirFlZd2olZ55J0jgW4VcCU3GQ/s1913/Screenshot%202023-01-04%20003446.png)  
*ns25 у ново створеному кластері*

Додається до новоствореного кластеру новий нод ns26.

[![](/assets/images/blog/91125aef09390805-f5b7b0bb8c577df1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-KWwNaRezAQ7dI0q8shm7C56t1jU3HPGdZ9pv_PyXt4YEVq8sBvmdTPWK3QQXuixKTeRyHsHCjdS41ruKtgBScXNSNW4Ig4zwLRb16dw5EiKRNOPHTjnNnco5IxKdPgT_Ua9osnC2WMtXghJ9NJsKwvT1dPndZq0eJX5dIgNvZrRy7ayEJUqvAP7UIg/s1912/Screenshot%202023-01-04%20003749.png)  
*ns26 додається до ново створеного кластеру*

  
 Кластер з двох нодів.  

[![](/assets/images/blog/f3c60fc9843ee33c-9bc465b6507d089e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTJ3OTgkmXPKf8UaJEP-YRu8wKGCUcb0gNFZ2y_tqs947-ORia-39T0TGRibvgJVhcY_Cv2aE1YTNLt365bpgu55snZfztPi34ywHYKf4fpq0CYt1xa2j_t0v0cLRxvou3C_aoMb36JEiKONHxXcUO6GJ8Ebj8BL1vf0cLuiQ6pe8IhIHDR5udmU85ew/s1915/Screenshot%202023-01-04%20003922.png)  
*Кластер з ns25 та ns26*

  

Далі переписую назад файли конфігурацій віртуальних машин в ноді ns26 з /root/backup/vmconfigs до /etc/pve/qemu-server. Все - віртуальні машини відновлено у кластері без використання методу backup - restore.

[![](/assets/images/blog/299340f18ce1a318-8ede62181d642086.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSTgg8HDqqDc62896RzogLOb8WDD_ZYCuM8grj4yj2laIbIBRvcPRpQvrjjj_OJNZtHZwOSk9hXAuo4wtyIfx89txq2FbCai9Pkw-LH8Gmf_jDnaq7u-DaAhWh7zNcozxIpooKxXLjGRdiRLp81Wy3a88E5m10BsxJtFDgO5tvBpDQNgdZBQ1MZ2G-UQ/s460/Screenshot%202023-01-04%20004907.png)  
*Кластер з відновленими ВМ.*

  

[![](/assets/images/blog/d377ce75dfdc0560-6b5005add6b542b3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0mDcEGAx1XsZxjNyIa2uS-Un9eYSJHgQM-zMxu7ytvVCHpJN_nVewM6oMfdX5OCMF6_oXRiD4XBgBkyM_9E8xi-T4kMFJ5VOIINMYyJuODvPUfhrSezDO9RT9D1j1fIx1lfwjWwACxoCBpGXrbrlwGFhCXVp28X6P4ai97yOAgR9LplHwwaMFmt6s7g/s1908/Screenshot%202023-01-04%20010048.png)  
*Приклад запущеної ВМ з ноду ns26 в кластері*

  

Послідовно додаю інші ноди до кластеру, так як мінімум бажано мати 3 ноди у кластері для створення робочого кворуму.

P.S. Допомога з видаленням ноду з кластеру:

```
systemctl stop pve-cluster  
systemctl stop corosync  
  
pmxcfs -l  
  
rm /etc/pve/corosync.conf  
rm -r /etc/corosync/*  
  
killall pmxcfs  
  
systemctl start pve-cluster  
  
pvecm delnode <node to delete>
```

```
# ^this^ failed which the doc states is expected... so I ran  
  
pvecm expected 1  
  
pvecm delnode <node2>
```

https://forum.proxmox.com/threads/proxmox-ve-6-removing-cluster-configuration.56259/
