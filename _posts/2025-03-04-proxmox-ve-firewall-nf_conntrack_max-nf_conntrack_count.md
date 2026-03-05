---
layout: post
title: "Proxmox VE, firewall, nf_conntrack_max, nf_conntrack_count"
date: 2025-03-04 16:57:00 +0000
tags: ["firewall", "linux", "nf_conntrack_count", "nf_conntrack_max", "Proxmox", "proxy", "security", "squid", "ubuntu"]
blogger_orig_link: https://lexxai.blogspot.com/2025/03/proxmox-ve-firewall-nfconntrackmax.html
---

Є Hypervisor, Proxmox VE з ввімкненим firewall на рівні Proxmox, через те, що запущені VM з Proxy сервером "Squid", і бажано обмежити доступ до потенційного доступу до локальної мережі.

Але за великої кількості підключень спрацьовують  обмеження на кількість одночасних сесій підключень.

[![](/assets/images/blog/f0aa1c2ca1f3e971-4dd7f1cc82710c29.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMAEa2FILx_CFNDCsnUtf-v0gMdXn-Kwtc3mm4uKcsJT1FvJgGhEHkX-TwwRafi9hs0kcC_qS8MQNN5DPyqRKuqWyGb9_TD6TdS_wyv-7oKYCqv00VQ0ccYy4WcrMkzC646ZUDzihE93hI_SAMqhoYkJ5-caoeS9rK7liR-i9eyzmrYQCcPDBGPZ5DLlNY/s803/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-03-04%20183943.png)  
*Proxmos VE, Shell, dmesg*

Для стандартних рішень з Ubuntu є змінна файлу з додавань певних рядків у  /etc/sysctl.conf, але усі значення перевизначаються і бачимо постійно значення за замовчуванням.

[![](/assets/images/blog/13732f7092240021-d9ee90bf80276ab0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjX0nr3oaOTYeYhwyzxzXhEVFhvqYXjafEDARSeG8Y6jE5jptMU6osTjqNAO75YHoM271YrmgZKGT3gN4JoSdz73ok7jFdzc4ViLK19ZxRiAcpnUSbWngUaZ8Vx1olztrtsceGKyCILawWMHiFw_1SUuG-Wguu7KRRkVeSBRwQxQ_dcrC7O08PTOqzCgO3x/s571/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-03-04%20184202.png)  
*Not help, /etc/sysctl.conf*

Але знайшов давній пост де є пропозиція використати налаштування GUI Proxmox. І це допомогло.

[![](/assets/images/blog/ffd0be78f3d90398-8f16e21286d6ad05.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWTINrgtxfIA01IWZtNOvaqjM0v7GgXciPtE2cMI3fhQle65yHtTzcaQFhHuUucDb09Gn7TX_5Kr2PTonlXnjiZg_p8hZUNeXaNBhXodxrnsG1Rv7MiOAKqHIW2r2__AC27u8oldK34WNzoIlF0zSmZSF9Wtl4md5ccUJ2KozJlbIvNiOsH01DEIetAzZx/s1183/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-03-04%20184611.png)  
*Proxmox VE, GUI, Node, Firewall. Options. nf\_conntrack\_max*

Моніторинг значень - nf\_conntrack\_count: "watch -n 1 "cat /proc/sys/net/netfilter/nf\_conntrack\_count"

[![](/assets/images/blog/9c47acdb434bd311-f427aa229f8ea54b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqlTFtqCgtB9yE4HKbQwD4eqLf6Tv3qQfc7VvxVddQOF7_UBAMcfeKByMrFTF7fgBBsBOy5ZQMwI6V8iP2lRXIt25DjMBvwzkI4Bop_h4n6cYcbCWiEkXh9hGWjZXDF8oSiSyUDRdK0tYJRN67qCTNh7dQ1pcf0EOjIFDG2xoJFMSV9DKOy4SJHxMRpsuN/s1027/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-03-04%20184417.png)  
*watch -n 1 "cat /proc/sys/net/netfilter/nf\_conntrack\_count"*
