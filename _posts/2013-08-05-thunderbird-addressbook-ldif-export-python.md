---
layout: post
title: "Thunderbird addressbook LDIF export (Python)"
date: 2013-08-05 20:56:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/08/thunderbird-addressbook-ldif-export.html
---

<http://code.activestate.com/recipes/476224-fixing-thunderbird-addressbook-ldif-export/>  
  
The CVS repository of FreeBSD contains the package [py-ldap2](http://www.freebsd.org/cgi/cvsweb.cgi/ports/net/py-ldap2/).  
  
  
addons:  

```
ignore_attribute = [
 'modifytimestamp',
 'mozillaSecondEmail',
 'mozillaHomeStreet',
 'mozillaHomeCountryName',
 'mozillaHomeLocalityName',
 'mozillaHomePostalCode',
 'birthday',
 'birthmonth',
 'birthyear'
 ]

copy_attribute = [
 ['sn', 'cn'],
 ]

ignore_objectclass = [
 'organizationalPerson',
 'mozillaAbPersonObsolete',
 'mozillaAbPersonAlpha'
 ]
```

  
  
#cd /usr/ports/net/py-ldap2  
#make install clean
