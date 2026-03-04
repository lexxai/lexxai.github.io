---
layout: post
title: "Нотатки. Тестування iSCSI Mutltipath. Windows Server 2019, FreeNAS"
date: 2019-10-08 21:32:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/10/iscsi-mutltipath-windows-server-2019.html
---

Маю Windows Server 2019 для Hyper-V.  
Вирішив протестувати  iSCSI Mutltipath на двох 1Gb мережевих адаптерах  що під'єднанні до сервера FreeNAS з iSCSI службою.  
Результат, завантажуються два інтерфейси з загальним коефіцієнтом десь 1.4.  
FreeNAS служба iSCSI з використанням ZVOL на пулі ZFS Z2, з шести дисків SATA 3.  
Приклади налаштування iSCSI Mutltipath [Using MPIO with the Windows Server iSCSI Initiator - Petri](https://www.petri.com/using-mpio-windows-server-iscsi-initiator), [FreeNAS iSCSI Configuration for MPIO - Virtualization Howto](https://www.virtualizationhowto.com/2018/08/freenas-iscsi-configuration-for-mpio/)  
  
  

[![](/assets/images/blog/64008c3a96d93b00-d3ba2fe631aa6fe6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUJZ3cIRj9LknK_7rtCAEHT4wzRvimny7UEFG_xG1H8igMMAEk8eAAta3d3Td1sj25s60ijenmtioxKcghUu1jybBcF10q2DFAjzADnT8WRObvWDJD4_whu-So7Zh1C5c4LL_bfevuLadw/s1600/hyper-freenas-iscsi-multipoint-02.PNG)

  
  

[![](/assets/images/blog/bdcb380d58037f1f-ec67e0c41b987ea1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBXwZWCbuqC-ORmakWL8Q6Vcvye_BNVs0J47sD9x4c19yEModkYtcpXZLYcEpUgOSctthKMjYHMRDtdnau1VLPczAcKVLfoNuUsd902T8GgcNBx1kuEvNv_yA5CpwzCqNiuCXymUG895KK/s1600/hyper-freenas-iscsi-multipoint-03.PNG)

  

[![](/assets/images/blog/95e3aabf05e56192-fdf47e239c602192.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNJ1ByOhHZx4Ka_dX9NyBMtmqnX2n06aoBOv2CJp2oLQmFTCq6iwogSiJ5LpADSrtVq8lDX5E6pARDTfAJylqH_LhbIuk7YxxQlq2Kzb-7Tq2PijTckcZ7d_M51GrTNCPyCKx0H2hglEKT/s1600/hyper-freenas-iscsi-multipoint-04.PNG)

  

[![](/assets/images/blog/9ece2bb7d49e0b0a-2eaf7ca110f8ba43.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjF8l2l8RwM4XIYxJPX07muYjazmmAS9XBe2gX3YgRocXi_ZKo3IutnC-CRSOYSkN02iYJgdg_4TueSKGXTPlSH54ksmnrb5HpOqwBf9bjV2wbHiOU6nU4B2gLf0WKVVAyU2Cq3_eUwzPWa/s1600/hyper-freenas-iscsi-multipoint-05.PNG)

  

[![](/assets/images/blog/40672617510d5125-eb86b523b5c0aa97.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmBhCsD6HsjJhsyNchaapN-73IK61zswARO8onb41Ts_Xbk6EdgMFY99ntCbKIgXTlp7KNe1o8KytcaxJ0vUE6UVQOuxkaJ2LYXEYHE-wp5_nuskwD_dFL1WhDWGvGWwcxANQAITAvvVNg/s1600/hyper-freenas-iscsi-multipoint-06.PNG)

  
  

[![](/assets/images/blog/8fe805569ed73ac4-dc080ff835ad3adc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1Z6vXL5gXfqG57SZh4xH_-Ug0uScUf2K4bZsuqT6xCegSsjNmQNhwdw-gkUMPFNVmfFy9cQCD0XKuzoQ8pSvKjb_knKkUsDYuxUR3hBabKh9D7SrJDixL8ZVp_Ae1ukhbSCJ_PpC9xV51/s1600/hyper-freenas-iscsi-multipoint.PNG)  
*Завантаженні два мережеві інтерфейси*

  

[![](/assets/images/blog/5c0fcb3bd38fb720-60bb1cbd93d896e8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgu4ZWgQZEEGQfiMrzfMaVBXHdrFkDJQlrB-bL2pnKbHVc45O3ZwGCkPFWPxKHQEXw3V1ypglPMgHY-k9KSNI6IJk4qQbJWnlPNFuI6_mabaHLGjW_N51xV99RFDapQJgYSBXkDm9hlRkCn/s1600/hyper-freenas-iscsi-multipoint-01.PNG)  
*Результати тестування NTFS диск на iSCSI multipath device*

[![](/assets/images/blog/fc10162e72ab3cb2-f9886149a0f45ae1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqsSMWtfd6sM5B4_m8nkH2f966IBdJrUv_FSFifHO1Xu0B-dbKLLDzcFTyqyTAkVzKaqCFlcl9oFrwUBwgS6u6oW_lTsHmBR2XSSqJ36myOjydmGkQ57FgwJj5Ip7rNch24QVUla74cxKO/s1600/hyper-freenas-iscsi-multipoint-07.PNG)  
*Результати тестування NTFS диск на iSCSI без multipath device*

  
  
[Налаштування iSCSI у Windows 2012](https://windowsnotes.ru/windows-server-2012/nastrojka-mpio-dlya-iscsi-v-windows-server-2012/)
