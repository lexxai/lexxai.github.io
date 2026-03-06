---
layout: post
title: "Нотатки: Hardware modding TP-Link TL-MR3420 v2 встановлення пам'яті на 8МБ. OpenWrt 19.07.6."
date: 2021-02-01 20:19:00 +0000
tags: ["firmware", "internet", "linux", "luci", "open-wrt", "openwrt", "router", "tp-link", "u-boot", "usb", "Wi-Fi", "wifi"]
blogger_orig_link: https://lexxai.blogspot.com/2021/02/hardware-modding-tp-link-tl-mr3420-v2-8.html
---

[![](/assets/images/blog/e5e1c4f6686f11ed-d683ada29323ec50.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-BvJl9d5kPwE9RfkVSVPQBI0hVKYHIBYd-Esj6opwb2MWoCQvKe7OQCPQoQYPlf1gLxDYeE9YbsWhowDrVT6JvsORkxcAkMoDBKjiqvUXyRcsyZwqGiqkRDcemoHwN1-XDsrdzceSl0TR/s4129/IMG_20210131_220807.jpg)У продовженні [попередньої реанімації роутера](/2021-01-17-нотатки-tp-link-mp-3420-відновлення-openwrt-19075.md) TP-Link TL-MR3420 встановлюю flash пам'ять на 8МБ. І нотую для себе крок за кроком.

 1. Зберігаю розділ ART, з інтерфейсу OpenWRT, або [іншим методом з mtd](https://openwrt.org/docs/guide-user/installation/restore_art_partition).  

[![](/assets/images/blog/29b070c14fc575af-2b441501acfb914a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiE8KxugoeW0dfxRcozBDlrlPE3jAsb2Y-XURID46EnnANHhwDWkeH5JDCDpBjStjOknV39CRq8Tw1Uu_swcIWPrAOgSZ5gLXdsKwnbHJWheRvMATZuYP5evrNcmzEWYDRxJKEvqUa7aW0K/s771/flash-3420-05.PNG)  
*розділ ART*

2. Придбав 8 MB Flash : Winbond 25Q64FV

[![](/assets/images/blog/a86ba814ebd0007c-febd35634681a501.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjefCB9T_nnfbIt0H4h_6zyLpLaCPHFfFED5_FtmRD6Fc7DyvkjhFh7uig1wQen4OdgmELA7DC1RcPoCRZZ_4-EtS-EUcGZ0u0grMyYFBbh4wmj8GBk-cnPRUX3pPl23CBpZPaE2sHccgEX/s3456/IMG_20210131_230633.jpg)  
*Winbond 25Q64FV*

3. Load [U-Boot Console 1.1.4 by hackpascal (MR3420 v2)](https://4pda.ru/forum/index.php?showtopic=627012&view=findpost&p=77112909)

4. Прошиваємо в програматорі згідно з інструкцією, змінюємо MAC адресу до прошивання, або може змінити MAC адресу у Web інтерфейсі пізніше.

[![](/assets/images/blog/0c9c75b82ae6cd85-d683ada29323ec50.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEja7ufpo9DsBCd5h0brvg61OxCVgooGTQGOFeyLQz02fz4IDhkPGY-nX0kbV0XF8IkMOtVC3cFwS1ON2tnz_lLFF7FEvmrn_CtagUi5Cc1WrTjg7BklIRh0Vm0GreEDJ3JBUd1wV2pODKOJ/s4129/IMG_20210131_220807.jpg)  
*Програматор СH341A для 25Q64FV*

[![](/assets/images/blog/ced1589bd235ad6d-d9b5de89a0db96ab.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgc-S3wgG4NNWndJPUK_J2ya-F_JwgC3TdL8hqs7wQ5lDnoH-ZBdJjIceUllyMHoEbHSez4MVsnVQ8vVlWSgb333RqgT1el4O-AZXnAZw3ls_siaZ6Ca12skbBhLpUoSwrKRL-YftTejw3a/s995/flash-3420.PNG)  
*Програма для програмування 25Q64FV*

3. Випаюю оригінальну мікросхему флеш пам'яті на 4МБ

[![](/assets/images/blog/6732d7b8e80a7edc-5a7ce83d15347605.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5LD5dOu-EnhZVc3CTr-gc1xjZh7sfaxdJSgZuB35VNlhr0W4N2QY7wxeiJ169oV-8Bbq8gq-bp4Fob3vhdeqc22dlJiS5A52y_7T7tYMSEZQj20cp7-FDZqIZPe0NHz15HYqgtQEBn9iB/s2181/IMG_20210131_224504%257E2.jpg)  
*Оригінальна мікросхема Spansion FL032PIF*

  
4. Запаюю прошиту мікросхему флеш пам'яті на 8МБ замість оригінальної.  

[![](/assets/images/blog/17662f550101bfd0-9efc5144da5134f1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhB4jxxt1QTUHOQ_gUmVSXFHO91V43h2Hn4VCgGHHcKLKizzZOjw92QMxiYLguBk4l35gwY-MttnnxRGBQt1hX_VYWyLG3-fm_DT998dXf3neKkp6xdkaDCy3Cw1TWE5H1_Vnlu3ka_54nl/s4608/IMG_20210131_230743.jpg)  
*8МБ в 3240*

5. Вмикаємо роутер з підключеним мережевим кабелем. Комп'ютер автоматично візьме адресу від роутера по DHCP. Заходимо в браузері за адресою http://192.168.1.1

[![](/assets/images/blog/d35d12280c21a330-9733f9eaef9abff7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzt0R7t6HyalL-IoyxM50Lh-ZLh3r1o-_uvhplKiXt6iZvyysyAqnGjBF-rFVfj9xiXgRMLab-UilHCwnUGwYuL5mwNArCVJHeuwLAUIZiO8jDNJpDDU9xjUJ4e4G1mkW1vIHFJc7f_77e/s892/flash-3420-B01.PNG)  
*U-Boot*

6. Завантажую готову [модифіковану прошивку на для 3240v2 на 8МБ](https://4pda.ru/forum/index.php?showtopic=627012&view=findpost&p=98651873).

7. Завантажую прошивку (factory) і збережений раніше розділ ART в інтерфейсі U-Boot Console, для прошивки роутера.

[![](/assets/images/blog/101283623af27920-621d96f3001076a9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0VcF7T-5tW4etcZ7jq5PWwPcqY4kqy4ymdoi7TU40U31VcTg8Kz6vXKwG5fboDvniTDeAsUfrTgMwtEqEW2EiVe9MYIt8T3BIK5UjrqqZigZs_p1_wo_qzd6qQf5jcsSTPRb-Kzu5yMEm/s903/flash-3420-B02.PNG)  
*Вибір файлів для прошивання розділів*

[![](/assets/images/blog/fd43325e62cd5c32-e4c067efe09384b4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNaJmWydOscAcfQ5fPRU1A78VP2qLA32nDqOLGbkAgSkJRcP-Y0VWkn6L8Zxc_71-_BaYSXuLCzNck52s4s0wFuE3c41rBbvTxJz-UVWny61UsR1ZhhpEPQm6K3jlz8tt0TlStCtCE3dF6/s888/flash-3420-01.PNG)  
*Процес прошивання*

8. Після перезавантаження отримаємо інтерфейс OpenWrt

[![](/assets/images/blog/1ea57eef383e7403-977823f58bd93189.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhznPqkXv4M9EF4d-SJ0gfIsXk-99SiNqdujmUoortmk6JzXOJStKo9681OAcdzHSJPtmtoeFu5Zc8GbBh0KwkWLbRasM29JliN66NN-wZrsITFw6oneYDupkp5dSkhMwecFmjLUO5u_oB8/s991/flash-3420-03.PNG)  
*OpenWrt*

  

[![](/assets/images/blog/3a3fbe89da50f4cf-a3b36685b1d6153d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCUndKJl-Qv7RaLjdIDNFHjM0t2N7twEqQcaXRIfrGsxHXsoeElBotB9NoTGl2X_Oks17oMZWy2oxN-n4SqlfpA595k_LCY4OVW3S3luAhdoqtLB-cERi8uyT1c6GhL71JIcKosO1ugsm7/s691/flash-3420-02.PNG)  
*OpenWRT 8MB*

  

[![](/assets/images/blog/2d7a9e2939677da2-a99e0f94607aeff7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirk8i0TQ05KJiEzC9MWVkeaUBpHHO6X3oNWLWDo-99GwIAIPVDlnbOMbog7syW3fxFr1HpC4iyaR4RS6JSuGishwkaT7uQeYEH4KaZ0i54y8i76tuSlqwMo7sREkL5qa4P5qMwBZdXz6QW/s979/flash-3420-04.PNG)  
*Вільне місце 2.3МБ*

9. Встановлені програми:

```
opkg list-installed
base-files - 204.2-r11063-85e04e9f46
block-mount - 2020-05-12-84269037-1
busybox - 1.30.1-5
cgi-io - 19
dnsmasq - 2.80-16.1
dropbear - 2019.78-2
firewall - 2019-11-22-8174814a-1
fstools - 2020-05-12-84269037-1
fwtool - 2
getrandom - 2019-06-16-4df34a4d-3
hostapd-common - 2019-08-08-ca8c2bd2-3
hostapd-openssl - 2019-08-08-ca8c2bd2-3
ip6tables - 1.8.3-1
iptables - 1.8.3-1
iptables-mod-conntrack-extra - 1.8.3-1
iptables-mod-ipopt - 1.8.3-1
iw - 5.0.1-1
iwinfo - 2019-10-16-07315b6f-1
jshn - 2020-02-27-7da66430-1
jsonfilter - 2018-02-04-c7e938d6-1
kernel - 4.14.180-1-0f910cae00ea4cd3774f60535348fa2e
kmod-ath - 4.14.180+4.19.120-1-1
kmod-ath9k - 4.14.180+4.19.120-1-1
kmod-ath9k-common - 4.14.180+4.19.120-1-1
kmod-cfg80211 - 4.14.180+4.19.120-1-1
kmod-gpio-button-hotplug - 4.14.180-3
kmod-ifb - 4.14.180-1
kmod-ip6tables - 4.14.180-1
kmod-ipt-conntrack - 4.14.180-1
kmod-ipt-conntrack-extra - 4.14.180-1
kmod-ipt-core - 4.14.180-1
kmod-ipt-ipopt - 4.14.180-1
kmod-ipt-nat - 4.14.180-1
kmod-ipt-offload - 4.14.180-1
kmod-ipt-raw - 4.14.180-1
kmod-lib-crc-ccitt - 4.14.180-1
kmod-mac80211 - 4.14.180+4.19.120-1-1
kmod-nf-conntrack - 4.14.180-1
kmod-nf-conntrack6 - 4.14.180-1
kmod-nf-flow - 4.14.180-1
kmod-nf-ipt - 4.14.180-1
kmod-nf-ipt6 - 4.14.180-1
kmod-nf-nat - 4.14.180-1
kmod-nf-reject - 4.14.180-1
kmod-nf-reject6 - 4.14.180-1
kmod-nls-base - 4.14.180-1
kmod-ppp - 4.14.180-1
kmod-pppoe - 4.14.180-1
kmod-pppox - 4.14.180-1
kmod-sched-connmark - 4.14.180-1
kmod-sched-core - 4.14.180-1
kmod-scsi-core - 4.14.180-1
kmod-slhc - 4.14.180-1
kmod-usb-core - 4.14.180-1
kmod-usb-ehci - 4.14.180-1
kmod-usb-ledtrig-usbport - 4.14.180-1
kmod-usb-ohci - 4.14.180-1
kmod-usb-storage - 4.14.180-1
kmod-usb2 - 4.14.180-1
libblobmsg-json - 2020-02-27-7da66430-1
libc - 1.1.24-2
libelf1 - 0.177-1
libgcc1 - 7.5.0-2
libip4tc2 - 1.8.3-1
libip6tc2 - 1.8.3-1
libiwinfo-lua - 2019-10-16-07315b6f-1
libiwinfo20181126 - 2019-10-16-07315b6f-1
libjson-c2 - 0.12.1-3.1
libjson-script - 2020-02-27-7da66430-1
liblua5.1.5 - 5.1.5-3
liblucihttp-lua - 2019-07-05-a34a17d5-1
liblucihttp0 - 2019-07-05-a34a17d5-1
libnl-tiny - 0.1-5
libopenssl1.1 - 1.1.1g-1
libpthread - 1.1.24-2
libubox20191228 - 2020-02-27-7da66430-1
libubus-lua - 2019-12-27-041c9d1c-1
libubus20191227 - 2019-12-27-041c9d1c-1
libuci20130104 - 2019-09-01-415f9e48-3
libuclient20160123 - 2019-05-30-3b3e368d-1
libxtables12 - 1.8.3-1
logd - 2019-06-16-4df34a4d-3
lua - 5.1.5-3
luci - git-20.136.49537-fb2f363-1
luci-app-firewall - git-20.136.49537-fb2f363-1
luci-app-opkg - git-20.136.49537-fb2f363-1
luci-app-qos - git-20.136.49537-fb2f363-1
luci-base - git-20.136.49537-fb2f363-1
luci-compat - git-20.136.49537-fb2f363-1
luci-i18n-base-ru - git-20.136.49537-fb2f363-1
luci-i18n-firewall-ru - git-20.136.49537-fb2f363-1
luci-i18n-opkg-ru - git-20.136.49537-fb2f363-1
luci-i18n-qos-ru - git-20.136.49537-fb2f363-1
luci-lib-ip - git-20.136.49537-fb2f363-1
luci-lib-jsonc - git-20.136.49537-fb2f363-1
luci-lib-nixio - git-20.136.49537-fb2f363-1
luci-mod-admin-full - git-20.136.49537-fb2f363-1
luci-mod-network - git-20.136.49537-fb2f363-1
luci-mod-status - git-20.136.49537-fb2f363-1
luci-mod-system - git-20.136.49537-fb2f363-1
luci-proto-ipv6 - git-20.136.49537-fb2f363-1
luci-proto-ppp - git-20.136.49537-fb2f363-1
luci-theme-bootstrap - git-20.136.49537-fb2f363-1
mtd - 24
netifd - 2019-08-05-5e02f944-1
odhcp6c - 2019-01-11-e199804b-16
odhcpd-ipv6only - 2020-05-03-49e4949c-3
openwrt-keyring - 2019-07-25-8080ef34-1
opkg - 2020-05-07-f2166a89-1
ppp - 2.4.7.git-2019-05-25-3
ppp-mod-pppoe - 2.4.7.git-2019-05-25-3
procd - 2020-03-07-09b9bd82-1
qos-scripts - 1.3.1-1
rpcd - 2019-12-10-aaa08366-2
rpcd-mod-file - 2019-12-10-aaa08366-2
rpcd-mod-iwinfo - 2019-12-10-aaa08366-2
rpcd-mod-luci - 20191114
rpcd-mod-rrdns - 20170710
swconfig - 12
tc - 5.0.0-2.1
uboot-envtools - 2018.03-3
ubox - 2019-06-16-4df34a4d-3
ubus - 2019-12-27-041c9d1c-1
ubusd - 2019-12-27-041c9d1c-1
uci - 2019-09-01-415f9e48-3
uclient-fetch - 2019-05-30-3b3e368d-1
uhttpd - 2020-03-13-975dce23-1
urandom-seed - 1.0-1
urngd - 2020-01-21-c7f7b6b6-1
usign - 2019-08-06-5a52b379-1
wireless-regdb - 2019.06.03-1
wpa-supplicant-openssl - 2019-08-08-ca8c2bd2-3
zlib - 1.2.11-3
```

10. Для повторного заходу в меню U-Boot, при ввімкненні роутера затискаємо кнопку роутера WiFi або WPS. І можемо знайти додаткові розділі, таки як повна копія та інші:

[![](/assets/images/blog/7c725312b9e5dda6-84e939c8bc2c06dc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhID_c2yiahpa-cA57svfbpXxcGGAjIiprNx84s9VrmwQjH5tIevBBSoH7cYs1TMmWpeB4S5A4G9FvQNb0t-BYp66Oj0mYvxRcAsehpscY7GxNf2nT-n9sbiFY-_OJfaADXSxA339nrMfdl/s873/flash-3420-B03.PNG)  
*Збільшення робочої частоти процесора*

[![](/assets/images/blog/9690b04a4ad145f4-ae933739a63337fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFRwZxfbX5i8NjF7RgpOk0iEyVS1M5PpI7eka8ZGnsnuIn0FPtfWvjTTx8P3afJwitasYcx1bjOLQaTTw4XONCe_35CyLLXXntNxwaO0upDyNLKgiIfBg1txqlrTR9Wa6a4cjwllpaJkUj/s872/flash-3420-B04.PNG)  
*Розділ відновлення*

  

[![](/assets/images/blog/f69c8d0a3617db30-174dc196ffe6fdef.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPi78YkP3Js79Ep_EMWW0Lj8g3F0ow3KkAXgCqFtKDe-PPNHUMm2OewACdDFyThNc3onOWKZe_Y0HaTtFUVBTX4nMjGZ63otz3uRCa9hUYeFRLkKjdwPYcqCmgPCF2LoRd8om2O-Q71Ej7/s862/flash-3420-B05.PNG)  
*Про розробника*

11. Власний образ для 8МБ створюється на основі : "[Маємо те, що маємо. Усе що відбувається - на краще: Hardware modding TP-Link TL-WR841N v9 4M to 8M Flash and compile custom OpenWrt](/2019-07-15-hardware-modding-tp-link-tl-wr841n-v9-4m-to-8m-flash-and-compile-custom-openwrt.md)"

```
git clone https://git.openwrt.org/openwrt/openwrt.git/

cd openwrt
git tag
git branch
git checkout v19.07.6
./scripts/feeds update -a
./scripts/feeds install -a
```

Правимо конфігурацію для ar71xx, (для ath79 не підходить - зависає)   
в файлі target/linux/ar71xx/image/tiny-tp-link.mk замінуємо tplink-4mlzma на tplink-8mlzma

```
define Device/tl-mr3420-v2  
  $(Device/tplink-8mlzma)  
  DEVICE_TITLE := TP-LINK TL-MR3420 v2  
  DEVICE_PACKAGES := kmod-usb-core kmod-usb2 kmod-usb-ledtrig-usbport  
  BOARDNAME := TL-MR3420-v2  
  DEVICE_PROFILE := TLMR3420  
  TPLINK_HWID := 0x34200002  
endef  
TARGET_DEVICES += tl-mr3420-v2
```

[![](/assets/images/blog/7446dc908f6531ac-5080411da491c59a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrplnqM5gE6-_cxL9T3-kRFx7TOurpDCjsqlhxSdAkFydOd-75dgkvHvr8aOszi_9w0ECgEq3HkyTbeTNJGdjZLguPRE1J_mTQaeFpzvFUaUurFqKgVkjNrsAQN9zX20VV61x24TsndtDp/s673/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-02-01+03-10-02.png)  
*tplink-8mlzma*

Запускаємо конфігуратор

```
make menuconfig
```

В конфігурацію додаю Luci, block-mount, kmod-fs-ext4, kmod-usb-storage, wpad-openssl, KERNEL\_SWAP, mc

config.buildinfo

```
CONFIG_TARGET_ar71xx=y  
CONFIG_TARGET_ar71xx_tiny=y  
CONFIG_TARGET_ar71xx_tiny_DEVICE_tl-mr3420-v2=y  
# CONFIG_BUSYBOX_DEFAULT_FEATURE_IPV6 is not set  
# CONFIG_IPV6 is not set  
# CONFIG_KERNEL_IPV6 is not set  
CONFIG_KERNEL_SWAP=y  
CONFIG_LIBSSH2_MBEDTLS=y  
CONFIG_MC_CHARSET=y  
CONFIG_MC_EDITOR=y  
CONFIG_MC_SUBSHELL=y  
CONFIG_MC_VFS=y  
CONFIG_OPENSSL_ENGINE=y  
CONFIG_OPENSSL_PREFER_CHACHA_OVER_GCM=y  
CONFIG_OPENSSL_WITH_ASM=y  
CONFIG_OPENSSL_WITH_CHACHA_POLY1305=y  
CONFIG_OPENSSL_WITH_CMS=y  
CONFIG_OPENSSL_WITH_DEPRECATED=y  
CONFIG_OPENSSL_WITH_PSK=y  
CONFIG_OPENSSL_WITH_SRP=y  
CONFIG_OPENSSL_WITH_TLS13=y  
CONFIG_PACKAGE_block-mount=y  
CONFIG_PACKAGE_cgi-io=y  
CONFIG_PACKAGE_glib2=y  
CONFIG_PACKAGE_kmod-crypto-crc32c=y  
CONFIG_PACKAGE_kmod-crypto-hash=y  
CONFIG_PACKAGE_kmod-fs-ext4=y  
CONFIG_PACKAGE_kmod-lib-crc16=y  
# CONFIG_PACKAGE_kmod-nf-ipt6 is not set  
CONFIG_PACKAGE_kmod-scsi-core=y  
CONFIG_PACKAGE_kmod-usb-storage=y  
CONFIG_PACKAGE_libattr=y  
CONFIG_PACKAGE_libblkid=y  
CONFIG_PACKAGE_libffi=y  
CONFIG_PACKAGE_libiwinfo-lua=y  
CONFIG_PACKAGE_liblua=y  
CONFIG_PACKAGE_liblucihttp=y  
CONFIG_PACKAGE_liblucihttp-lua=y  
CONFIG_PACKAGE_libmbedtls=y  
CONFIG_PACKAGE_libmount=y  
CONFIG_PACKAGE_libncurses=y  
CONFIG_PACKAGE_libopenssl=y  
CONFIG_PACKAGE_librt=y  
CONFIG_PACKAGE_libssh2=y  
CONFIG_PACKAGE_libubus-lua=y  
CONFIG_PACKAGE_libuuid=y  
CONFIG_PACKAGE_lua=y  
CONFIG_PACKAGE_luci=y  
CONFIG_PACKAGE_luci-app-firewall=y  
CONFIG_PACKAGE_luci-app-opkg=y  
CONFIG_PACKAGE_luci-base=y  
CONFIG_PACKAGE_luci-lib-ip=y  
CONFIG_PACKAGE_luci-lib-jsonc=y  
CONFIG_PACKAGE_luci-lib-nixio=y  
CONFIG_PACKAGE_luci-mod-admin-full=y  
CONFIG_PACKAGE_luci-mod-network=y  
CONFIG_PACKAGE_luci-mod-status=y  
CONFIG_PACKAGE_luci-mod-system=y  
CONFIG_PACKAGE_luci-proto-ppp=y  
CONFIG_PACKAGE_luci-theme-bootstrap=y  
CONFIG_PACKAGE_mc=y  
CONFIG_PACKAGE_rpcd=y  
CONFIG_PACKAGE_rpcd-mod-file=y  
CONFIG_PACKAGE_rpcd-mod-iwinfo=y  
CONFIG_PACKAGE_rpcd-mod-luci=y  
CONFIG_PACKAGE_rpcd-mod-rrdns=y  
CONFIG_PACKAGE_terminfo=y  
CONFIG_PACKAGE_uhttpd=y  
# CONFIG_PACKAGE_wpad-mini is not set  
CONFIG_PACKAGE_wpad-openssl=y  
CONFIG_PACKAGE_zlib=y
```

Готовий файл прошивки тут: bin/targets/ar71xx/tiny-

Для прошивки з U-Boot  : openwrt-ar71xx-tiny-tl-mr3420-v2-squashfs-factory.bin   
Для прошивки з openwrt: openwrt-ar71xx-tiny-tl-mr3420-v2-squashfs-sysupgrade.bin

[![](/assets/images/blog/5a505f7695a46117-84d186320c28657f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOlf2OcTg2qJgc_nKEcIQFhj-u2OBliPUVX33y7Zx1lSCYa6B90gPumV9WZ_VfsbWUS3y4zB6xOjPOnHUwjGtsrAug-4u-FXD1oEN2UtJT6VBxqdg7pfnmXO5w4tF-UuNYGElXF28nyobW/s1005/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-02-01+03-42-04.png)  
*OpenWrt 19.07.6, TP-Link TL-MR3420 v2*
