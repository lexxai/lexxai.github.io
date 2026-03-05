---
layout: post
title: "Підключення даних моніторінгу mbmon у FreeBSD до CACTI."
date: 2012-08-24 11:06:00 +0000
tags: ["CACTI", "Fan", "FreeBSD", "mbmon", "php", "script", "Temperature", "template", "Voltage"]
blogger_orig_link: https://lexxai.blogspot.com/2012/08/mbmon-freebsd-cacti.html
---

Підключення даних  до CACTI даних моніторингу переданих з серверів FreeBSD.  
  
  
Я використовував поточну варстю CACTI v0.8.8a.  
Втсновленну з портів, FreeBSD.  
  
  
Для роботи з "Data Input Methods" створив файл ss\_mbmon\_common.php, що розміщується    
  
  
у теці з cacti/scripts. Цей файл у параметрах отримує параметри snmp сеовера, та режим запиту mode, кортий моде бути temps,fans або volts.  
  
  
  
  
Ось шаблони CACI - mbmon [для завантаження](https://docs.google.com/folder/d/0ByJHIt7DSY3cZzJrdlU4RUlBcGs/edit).  
  
Зображення ще потребують організації, поки не має часу. Виклав як є.  
  
  

[![](/assets/images/blog/ed2875c6fb6352ee-8ad65571e25c0570.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg60IQ_ExLCjsmycB9o4D5GWP8o_6o8xxBFbZ2GZCINooCrEy5D0a6tUrc-D7JeK3heSFBDejGy6YN-zJGKwXOIUX4eOY-EgRb2QRZqt_cQrWD2UArifY4uYGImsj939YvD5vt5g3nGS4na/s1600/cacti-01.png)

  

[![](/assets/images/blog/fb956896c8b8a63e-88582c6e64e9545c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5fqOuflh23KL6TAQPLJlRQ89YlyotJswyjZCZPqNwJhWx-TQjrahZSy29a2DTTL24fFYADRYMVM0MrKdmcAgLRyVWtov5jlZItNf3YsNAXNqhMwJ5RYiLX9-CkhKxP6J3sbtaL8fyrNbD/s1600/cacti-02.png)

  

[![](/assets/images/blog/cbe8a5c98b546c5c-da54457c486e739e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWa1IxMS2sM-69R2igOt8_RSFMyGQTAQNHIHSwb0mGwW5AtUPessd_1qIblYCDAobenXfZPba0_FDdIideWa9y5PWxNdRzpAbSdzekql6WshYQuEsKhI2PzMRPrt-Vn2GMm-Rc5wMFSlOa/s1600/cacti-03.png)

  

[![](/assets/images/blog/13fe41bd581ba33e-28168a36c7a46204.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTFNw1NEzMDOt-D2YKuiqaIAMkNptXvzAmYbZ8FcjxTChLoZPuYMM16WCu2uWl_ssO5mtpl0EPwynLEmOsRemNEh-_wTHYkuhHFlfLbo2JN64uHKv3xraiysarKdpiXwkIvqrKZ_v01xfQ/s1600/cacti-04.png)

  

[![](/assets/images/blog/5f47d326fccafe1a-f302958e24aaf4df.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgoV9ojRo7tddbnstumCMFKRF0LDBIEzE8HEtsCrcqtcn2iZn5wkI9-7uKBNlpPlrtDwvpGFpzpbVeUVYeWciWYy7LbZo7cKETJunkvOX1pyxZ0eTyR19O2HYQx5YjaeGe9Sb-P6ZHceMpo/s1600/cacti-05.png)

  

[![](/assets/images/blog/547b62724455a30f-e17b5d164813ec8e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjxpIH5j1jZMn7X4oGbCWr9rwYOuf-w2o0xmvz3KptYV7jXs_EoD8gvivJh8y6mNrznF-y30S_wZZEFWT2csh2lrRfnavAT7pB8WUieUNQd_Mk8dG1P25aHUj38oExLMq4hUfrjW9NYJnRP/s1600/cacti-06.png)

  

[![](/assets/images/blog/d09a7d671e602b05-6c17a83ce52fd992.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjyJ7CsfVlvkTMASCtY0Iljbtn7z4ZsFO2ziAMDQvpucG0R3Uxa1Oun9GFKF5KPw1ijn1xkOL8CXhiQ3esSNFw8zAEHa9cLurALHt-5e834VLE6ZDCyVCG5vxMNgoEQ7JL3_ymgvgr-stq/s1600/cacti-07.png)

  

[![](/assets/images/blog/1e8c4c251e968820-c78f4567718ce83e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjq6HyAfiqmnvqnVkFkUM8qst0RlUg0Ld8ApVDT84EkQ4TVrjiZVtTFBg8D4SVjFNPxmPLdeBzQGPV0z6wYvG8WHPhOictp5FzbhNj-URQ0uir4Yn9mMZgSmTNuYX3w1Ooo_jfRVGwT_nhv/s1600/cacti-07a.png)

  

[![](/assets/images/blog/4f1b025923556aa0-9c4af5b18b0c4050.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3Eg4LhOiqZz-8RFg9N8mdmSohX3WVuy8GNW1HLUbp3PaNuPp2t5MiCoeFizFSsSZ-nYxtd5ZCu6gK3XmpjZVazsbPov6E5Z-isWiJsr_5CZRZ4jzto0AUXz2CvtxcLEvlw_o93dMAQPIm/s1600/cacti-08.png)

  

[![](/assets/images/blog/2063cb76e6ad64fa-22e5b7457ef1bd0b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgh11Ynw4Vpr632TIP99QhnyR_91j7ngBB-iIzNYWT0z5OvpH_EfvsRRUNsmab45htLjozkTF1IgbeElUpMTinOPLeMqMuIJ3YjzT_cPYnyG_YnX-aF3SOef-9T4gunP9J9yl8e9PoAHztW/s1600/cacti-09.png)

  

[![](/assets/images/blog/e663bc30161d8738-dd7bf1d7d03411a8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg6ar_6djfH2HttV3dnym-c6eT0gQeNhoifdU72xPbDKzY8gRG3GLxMVbHGmJUsbCOpxdCBYdBajkPAyUmAJnpqFrps4gAKGeXY0Q0RbYYdiRfCg4pjc5HuhvpYfOmoy7jYvJsQxCBH_x8I/s1600/cacti-10.png)

  

[![](/assets/images/blog/f5cdcc28ad9d224d-de73642582f27918.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBWl4iuKXVT7qKwQzcCiCPxY8FqFrFJafvESa1Y_r7EnsvLktWRpmYkGAFquZGjNYtQ4i5FrLfT6QY6kBWYOOeZC7JnW2uJOeXPI_d8dq3kAv55RdVnURqvI7IKA7092xA8lDq98ZNKTnb/s1600/cacti-11.png)

  

[![](/assets/images/blog/e7ba8f403ef2cfb4-d90d067104285291.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgY3qHbgEE3RUqrqmr0SNrYMPLdC_3sHTLk083isfuZP5Xqf_KWcWIK7slrwudmliYbPSm6jiB__6bwlGvkhO-ZTP8Mojt5sexhPIfBxK1blV1bTgdE-1TEUZiTqnSoRx6jz_prq1pSrPNm/s1600/cacti-12.png)

  

[![](/assets/images/blog/eb39841faf4d30dd-aa556cc59491f58f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHCwU_grKOKsgFHcSB0gaHicSvdYojzfwi-vU-1b36xIPs-WyWMzmw1ucBRG1Zs8wec_WzzagyyYwsg9mV-Ku-EbJwe3Jymef9Jw0oCk4I2Wb63XPASyMgcHvyQbe5j9i3HHOrHjoKOrTD/s1600/cacti-13.png)

  

[![](/assets/images/blog/2b7366bfa258c8e5-d020c200c7b8bf6e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBZv7avi9TFAWjilty4Nmec0SThj7579Jvu2k1atlGj6BOTzG2ryvKC1Uic0YkHx-XKyF8KS6MaI6Z_3SWDvAuHZpzngdRDWO-K5G7W7fapWTZPjH5oXslf0JXrJrF7jTqBVzhPuhnaww_/s1600/cacti-14.png)

  

[![](/assets/images/blog/af940f496c8e1704-85702ca234583ce6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNmVxo_eAMZxmu9nHIIBwRh00O99tZQYeT6xVfqcb7eWM9qc5Qkj_dk0F9Tm1veSawQr8-9HLlHYnhhphUFydqHnZs-K6TYLlIDGkzZSZDZEucKRO2ItKzpA7OFvvHl7YUScvUrrcScdVn/s1600/cacti-15.png)

  

[![](/assets/images/blog/bf3770b40ca6ce68-ac1ffc6520503459.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzIxYxCsIGooiGqOSXuxvF_zSwPYoqRD3O6Q6w2qttWgF2uuuS68iT9vB9UtNKhsGG-FwLSrctEGYNnoXjXFardPU7AgRAmwFEkKXLEktl_OPeKxxe4_JsJOVHgMX741adpZLwMGsH0dxu/s1600/cacti-16.png)

  

[![](/assets/images/blog/ebe4dc8c78e7bb7c-7c2e4c646de9942e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhk3tRV7aVE9e1-pnvxfxW4SnYJB3fbMPtgwiH_ivwnsJq6wdX0zCwAEdbsP14nXq7uKC4WsXXInxQrdjv3JMLUF4g1U2g8AmBilVQclYbx4ZlLZLgzYOzdaSYu3FUjJMZoACWKcwRTDj9o/s1600/cacti-17.png)

  

[![](/assets/images/blog/716dd2b6cc4fa6e5-c4054ec3c1ff69d5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJMGtM6ZXVo18OMUXzHjjSmtEEVKtcH1mEAlpKXnzYx-NmiVbFy_WcUzSzcIXl5dgA65aXv7lDq4KUAhJbcxk-QUskVZP5sicsrU456g4UkmGtqf5hnT-j7TE28rmGGulqxEXkslM6PoTf/s1600/cacti-18.png)

  

[![](/assets/images/blog/b996dbacab6dc96b-d5e9b36f046c3e16.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7dUSPqLBqZP_pdVRROUkl0f8CWph33p1gOHhc9VwJsLgY2Trq-ciaCUtUXI6nLeJfCw7qegOClBOPLGKTXAEhSFcAyxdnNUSs2ex5HAG4OfMqD1aL0lJmvojdvZZv2qOelkB9aUiUfaUS/s1600/cacti-19.png)

  

[![](/assets/images/blog/f76ccbc1187df1df-2a249792540d025f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6LEd6mFyxNlAZa_EYvvdsnVml0Gry5a6qyZXAxrA-5_xccBwa0gvGyo4pbu2bDa2YlFHJP5trSgD_UttWpkUpRFvuu5NjVHJnx6mxBUFrU1ChmXwYJG3zabOmoirxhLyY8z_q-2-N7Stn/s1600/cacti-20.png)

  

[![](/assets/images/blog/962b22769c24e50f-e7611406096b5475.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTq3b4Tf5SBO4p9sOhMpKQPPR9MJ4zOjLYyJTBrnizqCmxjKBjzXVWl85TCHvebt7dIaBQW0zqVci1YM8hRKCfJFsJxDZ1OLp2SVV4MNaHBb-mnoKIjsqtOVb9BW-k-l86OiqnDgEwVQ7o/s1600/cacti-21.png)

  

[![](/assets/images/blog/040281d4bce4b6d9-cee9259c5b3c85ab.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhU25SWARQSN8aQ4c7tX_1ilYJ4iKbOEPlP-7jdQ6Kazg3mLZrbVmQgV8rWlJMSfD_eFU3rwk3Ydi2h9hTJkXQVvqnltZJQ4_BMtuL7T_qGLvuwfM-W2mjAEIPP2hyphenhyphenlPe8mnTiYA2YJCCHe/s1600/cacti-22.png)

  

[![](/assets/images/blog/79a8b7a20f629d13-f2a8f7607851cf2d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2cCn93XRvKqO-undr7fuYe_rDG2Q5OBkMvLchwmnxEc4cnHY7xn7o_z830jTViswrVkWGFSpmWBH0rQWco4bVj3NzVKWbB2KNb57cPmGM_PAoiNEKXkW2G50ECghdmAtpFr_qPXE_33-0/s1600/cacti-23.png)

  

[![](/assets/images/blog/1b69f01e775d3980-0ed9f8f62ba96fc5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDLAEYUSLtgVVXKuKOiYbOaeFQxpEC-iI5y7For5YMx7NMpKo77A1s7ujPNv9jUZLTnoL1UelntjdBOS7CPg8LqKjarLCk_xS48Kbtu91CMDkFKhRuJHLYtgZrv-e-uVhp4W4ykwJ1Bx37/s1600/cacti-24.png)

  

[![](/assets/images/blog/a82426488832bfee-744c9913d95b81c7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXg3yoHnzK2HnpgQOltJksejDZdA52nJKSjFaaBEYFgt0tawnYdgb8G-t_7xxQeD6TYeI585opgHJq34dxXgshx4hHiMjre9GcoNCZZPbrRhHYMpbD0rsv7EY1CzgdJt4u-q_upgLRNJzf/s1600/cacti-8.png)

  

[![](/assets/images/blog/da205ad0c0071666-bf493d2e2c665a28.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5qpKfg6hAogyfzaoF-lRppxhme8sMHML_AArmgXvVbDy6a_hxSYtMEy8VzMCBGTzxTyP6MwM0iPfTprZTSutPj6mOtHreDnnNVgayn8RicMvgW5aMx6eL0gCghAcwgjoviPbB9Qyjhb2w/s1600/cacti-di-fan-0.PNG)

  

[![](/assets/images/blog/ed7833e733ce4386-f9a6d063038f075a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjkb1AohpfUdGh4omSEKWp-J9GmfbYX6YPHWfIqXaTZpuWuuFhvO3Uu0Oh4wsbFjD1qmU1u4mfHXpAFmXRdGQswjcldXhPFuIa1kfHYAwdDR0UCl_gDpdAhHPU9rbfV2p8JazjN9tp3eO_/s1600/cacti-di-fan-1.PNG)

  

[![](/assets/images/blog/f3660aecd41a9e8e-dd4ddc2167442ca7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZJunLAhd4LwctOug6D3HfIJBsEWBEPDVfAWoIDqyIoou_aXb4LSQf8lY5VndZp91UJpveTi1jLEYvUX2mS6IIvPGXEry8tyaqfza_fZH19FabMYt2qAcKwjvlgZzyMFmarA-4-OfVbqsV/s1600/cacti-di-temp-1.PNG)

  

[![](/assets/images/blog/9db856d795a2d62a-de80fcc3d5ad4cdd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiLY11ZESYdXtNqGkfO0B9UcXoYtcpZFrefJxKwDq0FUOICJKv0rhyqBuKRxhYGGt_dU3BqQF-x0-5DKYQOpP6Vchzw5Vp0axXgt6GwHK_eLTBTsWbK4ARbu0sSTmP3cvKvEobUkLEYbtMX/s1600/cacti-di-temp-2.PNG)

  

[![](/assets/images/blog/72a2dbd42f9b1ac9-9ee0ed675cf43c67.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgUwGtPzw6nYHoCpFZRcHpgyJjO13x5Lrj-GwSmYyC3Kec3BwVskRp_rI_4hDDctU-jTR4gACi-RRtgKouOK5_OSk5ao-8iDRju4iycB1ObbfnXojZygWaa1qnDL3GyptgJ2nNwS0B2a0pP/s1600/cacti-di-volts-1.PNG)

  

[![](/assets/images/blog/1cd49a3abc06c5ff-18b9493023122aca.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiK5he5CbFwLra3VAT2vF01v9lyTZOL54tIUlotdhT3jJZEBnKiN0eDXwAnor3h3TxWMbr90unagWllUPGaHM5Z6qvdxEQedPsInayVaGL0Ylq9CNM_EZ9ByXHTWNJmPYwhgFFK-MK30TAJ/s1600/cacti-di-volts-2.PNG)

  

[![](/assets/images/blog/ea2fdff9d68775f8-7a4767dfeddaa152.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKjXHXvLBHQAZ_zzRJvTfgb4rFHrQv4ioNirLl9dIHsl9YhJ0vkjPK5edXUq2yoVXZlryFHlUXT8H8UqA4zV9wOUYLTpwx9LxOImU7jy9UIo0yy2xiXZ0iy5N7qah7sPjMGudJrH2MzExZ/s1600/cacti-di.PNG)

  

[![](/assets/images/blog/0d25074758a2c158-0e9e670619d0f4c1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQmFKsuovNtcYWgtZeNPuuPTGb5RU98Nr9xO2bOOiDV4d_hHhymJusEktujjV0aHQkdGAeqwBaOQEaM16jDu1fqj7NoDuKvpG8FymFuiJkFb2xRydjiamYUjHeT2M7dWXcrsMgVIEWNhYC/s1600/cacti-ds-01.png)

  

[![](/assets/images/blog/37c1ae125ba5f5b2-d1887d6dd0d79ac2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgioPvADeKnteVxOm4BUwJbmlNj-wihgQ-ONyCQRpU8XftPrzp2-GKL4HGjnYpe59ii8ZHVixaXACKQfG2zzK2O-1-LNorm0id3_wK6La50ONhha9VSWgnkrNSJabKAt46d1ihbvfaAKyFN/s1600/cacti-ds-02.png)

  

[![](/assets/images/blog/7ab5f8cdb96ad884-f028547cc7789e7c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjtAAyNTQpK63j4uECw2e_Ni9x0i4uO3uB1mr9Lee3845bPEMscxsThraXITe4I9FHvIJ34nNo7eE0-tlCmvaCHMnBPlJvKN-dMlDzpWOvKv0i0cwEHxGPvnZnLjCu5E5JIQbhgnlfrZW1G/s1600/cacti-dt-fan-0.PNG)

  

[![](/assets/images/blog/e3588bbf988eae2f-d8ad6217444b6f9c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEJFL-2Eoeobp4ucjb1VD6eoMyBBA2BiBpijFhjEzJdqzjq8K2cHiBifGEVt_kdp3gyNBIwv-4Lw9EkDnKFIvcPnICwri5Rvp2AfHnjZmRY5QdqxXbwdfoXQuTZi2m_J_NQCFA9adINc-S/s1600/cacti-dt-fan-1.PNG)

  

[![](/assets/images/blog/eed847baf33a0a78-6ccf03c9c3ff2317.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnF1LH-xdNJw2BB3bN-56bVhYI1HcbSzD36y5zF-_wN3M_YxQsv4vKfDgG8zDfp4Mhv2OCdFiB094A57J_9Nt9FpohBTgNO8Zuh1Ay1ORR-9A6Y4wdidaJNyEvEJTw1mKmgA3G8kh3tSLP/s1600/cacti-dt-temps-0.PNG)

  

[![](/assets/images/blog/9bf958f94dc772f9-80f9ec463dc3ee1c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7QlU-m-Ine91EHkRwfmzl_Nd5vFBEK6QtjN9Vyl9FYmhQFmAqU7ukB7gtsFmtoQR-gZ5NBH-ebNo5m3kqRLHpH3SavC-tYPl97hUsA05jagmChHJr25SFfbPWtxcLjdBfCHTd2gn9MMVf/s1600/cacti-dt-temps-1.PNG)

  

[![](/assets/images/blog/ba756be799974bb0-54fc2b253784a97f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSdNip8ZAx0iGGh_ONfkDnye2dwJP26JTldSqm72imLCdCWHHByk7RvB2Hvy54eH6VyJ0FfiX2tuIc_Qs_BgnhPQSu0KQDR2R0x8vOfYBPfrliZ5xP__51VLiBuQH-EXUnxQBxvxXNFzuy/s1600/cacti-dt-volts-0.PNG)

  

[![](/assets/images/blog/2d3ad733ccc1a0a7-7ca5219f9c8b6efb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBCRHcUlgbfNHIPjiOAzT6VHzd4FIELNPrxeU0xrV5kt5c91NewuqRYFLzQIAih_J3QRsfd_Q5hVBp8RS9yZcb-nNl8VJTtTmUHc_xcyFAqO7FtjVH-pAUDWCHtguxsMUTp6jc-t_u2zGQ/s1600/cacti-dt-volts-1.PNG)
