---
layout: post
title: "DHCP Alternate Configuration PowerShell Module"
date: 2014-02-25 21:22:00 +0000
tags: ["dhcp", "ip", "windows"]
blogger_orig_link: https://lexxai.blogspot.com/2014/02/dhcp-alternate-configuration-powershell.html
---

[DHCP Alternate Configuration PowerShell Module](http://gallery.technet.microsoft.com/scriptcenter/DHCPAlternateConfiguration-90e92431)  
  

[![](/assets/images/blog/3bd59a1ea67f999c-8f9b2dff1aa886bb.png)](http://i.stack.imgur.com/tYJlO.png)

  
  
  
  
  
Description  
  
There is currently no documented API for managing the [DHCP client alternate configuration.](http://technet.microsoft.com/en-us/library/cc779231(v=ws.10).aspx) Hence this module reads from / writes to the Windows Registry directory. The settings are saved in the value 'Options' under the key  
  
HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\services\Dhcp\ Configurations\Alternate\_<NICID>.  
  
The config saved there needs to be set to active. This is done in the value 'ActiveConfiguration' under the key  
  
HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\services\Tcpip\ Parameters\Interfaces\<NICID>  
  
This module provides cmdlets to retrieve, remove and set the DHCP clients alternate configuration. The value 'Options' is a binaray value. The length varies from 48 to 128 bytes, depending on the settings made. The format of the value is documented in the function GenrateConfigBytes.
