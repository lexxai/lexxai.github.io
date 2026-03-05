---
layout: post
title: "Restore a VM from a backup file on a ZFS snapshot over NFS in Proxmox VE by use GUI."
date: 2024-10-12 14:19:00 +0000
tags: ["backup", "cli", "FreeBSD", "linux", "NAS", "nfs", "Proxmox", "restore", "snapshot", "TrueNAS", "ZFS", "zfspool"]
blogger_orig_link: https://lexxai.blogspot.com/2024/10/restore-vm-from-backup-file-on-zfs.html
---

### Умови:

Proxmox VE server  періодично робить резервні копії до NFS спільної теки котра розміщена на NAS сервері.

NAS це TrueNAS сервер що зберігає дані у ZFS файловій системі. TrueNAS автоматично налаштований робити періодичні знімки (snapshots) ZFS dataset де розміщенні дані для NFS теки.

У VM сервера знайдено підозрілі файли за назвою "." розміром 1024 bytes з бінарним вмістом. Необхідно провести аналіз в "offline copy" сервера.

### Задача:

Відновити віртуальну машину з попередньої резервної копії.

### Рішення:

Під'єднати попередні періодичні знімки (snapshots) ZFS dataset, що створені на стороні NAS сервера, для відновлення віртуальної машини з її резервної копії.

## Підключення до консолі Proxmox Node.

* Proxmox node: ns21
* NFS share: nfs-ns58-10g
* Target data: 2024-10-11
* Target VM ID: 150

[![](/assets/images/blog/ea93d44887910d8a-b7faa839d14e1c63.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKK2wiAlCBgNNrLf4-bslPWGCGmYgvInN5Ca4LuN31EfEM3GHXGUXoOxqWgfRjjUcadCVl9ypLg9vX7Id375OphIChw2nEOm7n8UfDZAUz1bRb1Cki-DlUInmCG0IUuEBB1z993g7fJlxnEaRCXE8eOGg7I40gka-k8gl51OF4ehSeMY7TGu_oLbHMxnLQ/s1184/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20163403.png)  
*Proxmox console.*

ZFS snapshots зберігаються за замовчуванням у прихованій теці ".zfs".   

Знайдено цільовий шлях де зберігаються потрібні резервні копії: /mnt/pve/nfs-ns58-10g/.zfs/snapshot/auto-2024-10-11\_00-00

## Створення Datacenter STORAGE (Directory).

[![](/assets/images/blog/2f09e7161c7d8e2b-7441f211a6e79b5f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1IJEiuXwT2iz-e3I7LVkpVLd49wteQVMLPi_3lqwoaR5G3XpTDSXMGGIbmQUScbTZOvIoADSZFcAf6RIQA7ecS8ti-d5351X4nNb9JkhYUbh64B0vr3XmBKZjBdp6w-jjfTODe5aey3fbjkWrvzBaXMztfRvZxFtcgq_hrBE34Mv1quc-qXyH429hME2j/s778/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20163555.png)  
*Directory 'nfssnap' from premounted NFS folder on ns21.*

  

[![](/assets/images/blog/45f9b8276071ece9-b08abb75498bc5c1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEOE0tGQWV2PUkOE64X0fd0SkPRJ9n4eMx7zyN9JtZUXY8o2bHo5BDrvy9SH7v5aWoxVP8TN-ixKQp-Qj5h23J0Te4wldnr032rMYaztKLt2c3dhuxCKdccRUhCS3LOkxBfLH_-1DTI5_kgQaFjroT_NnCRv9qMnFxRMleXODZrp3yVp8LsjoxtnBGLD-B/s1627/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20163145.png)  
*Directory 'nfssnap' on Datacenter of cluster*

## Пошук резервної копії у 'nfssnap'.

[![](/assets/images/blog/e4d1031e7d8f675c-a36c34ade92fe327.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEisuTU6TaVQBDlBMlXhos_y9ivCSHVSkcVM5koS7r7O6N7s3dw7zG271ElgXI0ljXUJ6CeJKJw3Xg7ZJRXyo3cnQGib7B3JVVSy822dWTPxKDTINZTmvY3hoW9hyHWOmjhNgQV90MlC895KBOco3DuYx7rAvHB5ffaQ3skwmKO3zqPbc_JLUu9r1VT_I8Sd/s1428/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20163035.png)  
*Backup file for VM ID 150.*

## Відновлення VM 150.

[![](/assets/images/blog/67e336897c5d8c9b-1b9a796b0c31eeba.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiebFDJUiUSZAK1rSFBSkOmF56fIwEiSliQhbfN3ZkwYonP2nKcfx4uP7d_RUK0SuziPBYbWKUNsRUwEuFwEielXVOHzG3Hy-Dl91OYD3OiHwgUGPyuaHycKXcmCKosubNg3z1Y1HQXAf2LnLJeVlM7MMk_b66NYz6aZAF0-iLi_2NFHj3hYa-Pi46cL8c2/s995/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20162802.png)  
*Start of restore*

  

[![](/assets/images/blog/28e51a5f39221e3f-b4b15c4816029068.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsLnjZGvYCtA3huuo6kStnCVzogSl8DaXpVqGDBU8RC-VN9u-6Ih3MESjHJvmLRES5C3NRSbzA8qsWLgpS4x8kSTZfiHKBnihitibi-NqebRi7vZquYRORqJrjLbfEpJ3j3jv9KKqp-LxBZLCvOgJT_-cvpzijNWyicZPRrqWk7wAsNnKeJ-l8el7gUNrE/s1010/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20165810.png)  
*Finish of restore*

  

## Аналіз VM.

[![](/assets/images/blog/70f413bb75823492-d81fc6697230c2a9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4R_-6F5fyzJbkXkZn8ukI_BHBVA9pFd6MMKKSiDPorfe-G0E3NlZJyKg_JbWmULXr7pSp_6Dz8253V0Y9LUaS65YpRlZwTT2cHKjk8FNwf018JsnSmNePWZ9ydpKgesZF-uh2-KjbkJ9CIYkN-7oVsg7K89mlVo2DThp-Q__11W-RtgmCbPmpfguYtTt5/s372/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-12%20171542.png)

Не забуваємо видалити тимчасове storage 'nfssnap'
