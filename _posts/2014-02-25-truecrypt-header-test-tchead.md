---
layout: post
title: "TrueCrypt Header test (TCHead)"
date: 2014-02-25 18:59:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/02/truecrypt-header-test-tchead.html
---

[![](/assets/images/blog/708048a785969943-149bd33d43a1c007.png)](http://www.freeversions.ru/download/truecrypt.png)

[Index of /software/TCHead](http://16s.us/software/TCHead/)  
  
TCHead - FAQ  
  
Q. What is TCHead?  
A. TCHead is software that decrypts and verifies TrueCrypt headers.  
Q. What type of headers can TCHead decrypt?  
A. TCHead supports all the current hashes, individual ciphers, standard volume   
headers, hidden volume headers and system drive encrypted headers (preboot   
authentication). Please read the release notes for a more detailed explanation   
of what volumes TCHead can (and cannot) decrypt.  
  
  
Q. Can law enforcement determine if my hard drive is encrypted with TrueCrypt   
whole disk encryption?  
A. Yes. It's trivial to determine this.  
  
  
Q. Can TCHead brute-force TrueCrypt?  
A. Yes. However, TrueCrypt passwords go through many iterations and are   
strengthened. Cracking them takes time. Very strong passwords will not be   
cracked. Also, in addition to trying multiple passwords an attacker must try   
each password against each combination of hash and cipher (assuming they do not   
know what these are beforehand). System encrypted hard drives use only one hash   
and cipher, so attacking those is faster.  
  
  
Q. Does TCHead work with TrueCrypt system encrypted (preboot authentication)   
hard drives?  
A. Yes (as of version 0.4 and above). Use dd or dcfldd to take an image of the   
entire hard drive. A forensic disk duplicator will work too. Then, run TCHead   
against the image file. Here's an example:  
  
    \* dd if=/dev/sdX of=drive.img bs=1M conv=sync,noerror  
    \* TCHead -f drive.img -P words.txt --system  
  
  
Q. What's the best way to test TCHead?  
A. Create a TrueCrypt volume using the default hash and cipher (RIPEMD-160 and   
AES), set the password to "secret", then run TCHead against it like this and it   
will decrypt the header (provided that the word "secret" is in the word list):  
  
TCHead -f name\_of\_volume.tc -P words.txt  
  
  
Q. How can I decrypt hidden volumes?  
A. TCHead -f name\_of\_volume.tc -P words.txt --hidden  
  
  
Q. How can I try multiple passwords (brute-force)?  
A. Create or download a list of words in a text file (one word per line) using   
words that you think are likely to decrypt the header, then run TCHead against   
it like this. If the correct password is found, the header will be decrypted:  
  
TCHead -f name\_of\_volume.tc -P words.txt  
  
  
Q. Is there a version of TCHead for my operating system?  
A. A Pre-compiled binary is provided for Linux. TCHead compiles and runs just   
fine on any modern desktop operating system (Windows, Mac, Linux, BSDs, etc).   
Feel free to download the source and a build script. You may compile it   
yourself.  
  
End  
  
  
  
  
  

[![](/assets/images/blog/04d6e9937db24c63-254cdfd1edfcb929.png)](http://16s.us/software/TCHead/Pics/TCHead.png)

  
  

[![](/assets/images/blog/04d6e9937db24c63-66f3cbca789984c0.png)](http://16s.us/software/TCHead/Pics/TCHead_Windows.png)

  
  
P.S. Більше: [http://truecrypt.org.ua](http://truecrypt.org.ua/)
