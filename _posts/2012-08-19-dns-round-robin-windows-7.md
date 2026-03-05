---
layout: post
title: "DNS Round Robin Windows 7"
date: 2012-08-19 22:55:00 +0000
tags: ["dns", "round robin", "windows"]
blogger_orig_link: https://lexxai.blogspot.com/2012/08/dns-round-robin-windows-7.html
---

### [DNS Round Robin and Destination IP address selection](http://blogs.technet.com/b/networking/archive/2009/04/17/dns-round-robin-and-destination-ip-address-selection.aspx)

Symptom   
  
By default, Windows Vista and Windows Server 2008 follow RFC 3484 for destination IP address selection, which does not honor DNS round robin.   
  
Solution   
  
Add a new registry key with the following settings:   
HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters   
DWORD = OverrideDefaultAddressSelection   
Value data: = 1
