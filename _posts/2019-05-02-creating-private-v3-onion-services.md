---
layout: post
title: "Creating Private V3 Onion Services"
date: 2019-05-02 23:02:00 +0000
tags: ["Onion", "privacy", "security", "tor"]
blogger_orig_link: https://lexxai.blogspot.com/2019/05/creating-private-v3-onion-services.html
---

[![](/assets/images/blog/977c6da593df1c0c-d9ace7a2d2fe8c11.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiv0Yen-dJrzMyxnbTK49178CXJE2lyoAnoJV_EYz8terEAn0unS-JIX-urDTHUfA8r6e6lSdyM3DRwJ-7dqEdvM66Ox3T7kUIspK-kb8TCvwd16mNv06_k_aUlR0JF6UoNPGNMWVhW2nPg/s1600/torrr.PNG)  
V3 Onion Services додала можливість автентифікувати клієнтів що під'єднуються до певного Onion сервісу, за допомогою пари ключів.  
  
Наприклад, onion адреса є y34f3abl2bou6subajlosasumupsli2oq7chfo3oqfqznuedqhzfr5yd.onion  
  

### 1. Generate a key for Alice

Someone needs to generate a key for Alice to use. I don't think it really matters if Bob generates it for her instead. I will assume it is Alice. I would like to see Tor produce something themselves (perhaps inside little-t tor, perhaps a script shipped with its source code, etc.) but for now you have to figure out how to do it yourself.  
  
Use [simple python3 script](https://github.com/pastly/python-snippits/blob/master/src/tor/x25519-gen.py) to generate an x25519 key pair. It requires [PyNaCl](https://pynacl.readthedocs.io/en/stable/).  
  
Record the base32-encoded key pair somewhere. You'll need it soon. Here's some example *output.public: MEE25GRMPHS7NKNV3B7MHB6Y46FVGBALIC2OZUOD47CGYQMKQ56A
private: NQ2IJRNRZWPKVJNGWV7N6KJFUS235N27IP5NZ7UAXMXWUMILNLJA*   

### 

### 2. Bob tells his Tor about the public key for Alice

Assume Bob already has this torrc snippet.  
  
/etc/tor/torrc  
  
HiddenServiceDir /var/lib/tor/foo\_v3\_onion/ HiddenServicePort 5248 He should have an authorized\_clients directory inside foo\_v3\_onion/. If it doesn't already exist, he should figure out what is wrong because Tor should have made it for him.  
  
Inside authorized\_clients/, Bob should make a file ending in .auth; for example, alice.auth. Inside that file, he should put the following content.  
  
descriptor:x25519:<base32-encoded-public-key> Using an example public key ...  
  
/var/lib/tor/foo\_v3\_onion/authorized\_clients/alice.auth  
  
*descriptor:x25519:MEE25GRMPHS7NKNV3B7MHB6Y46FVGBALIC2OZUOD47CGYQMKQ56A* Bob should then restart his Tor.  
  
If Bob wants to add more users, he can repeat this process with additional files in this directory.  

### 

### 3. Alice tells her Tor about her private key

First she should check that her torrc has a ClientOnionAuthDir option set. These paths will be significantly different based on if she is configuring her system's background Tor daemon or if she is configuring Tor Browser. (T) means an example system Tor daemon path and (TB) means an example Tor Browser path. Remember, yours may still be different.  
  
(T) /etc/tor/torrcClientOnionAuthDir /var/lib/tor/onion\_auth
  
  
(TB) [Tor Browser folder]/Browser/TorBrowser/Data/Tor/torrc# In case this path ends up not making sense on your system ...
# The directory I'm aiming for onion\_auth to be in is the same
# directory that contains the torrc
ClientOnionAuthDir TorBrowser/Data/Tor/onion\_auth
  
  
  
After restarting Tor, if this directory doesn't exist, Alice should make it with 0700 permissions.  
  
Inside this directory, she then should add a file ending in .auth\_private; for example, bob.auth\_private. Inside that file, she should add the following content.<onion-address>:descriptor:x25519:<base32-encoded-private-key>
  
  
Using an example onion address and private key ...  
  
(T) /var/lib/tor/onion\_auth/bob.auth\_privatey  
*34f3abl2bou6subajlosasumupsli2oq7chfo3oqfqznuedqhzfr5yd:descriptor:x25519:NQ2IJRNRZWPKVJNGWV7N6KJFUS235N27IP5NZ7UAXMXWUMILNLJA*  
  
  
(TB) [Tor Browser folder]/Browser/TorBrowser/Data/Tor/onion\_auth/bob.auth\_privatey  
*34f3abl2bou6subajlosasumupsli2oq7chfo3oqfqznuedqhzfr5yd:descriptor:x25519:NQ2IJRNRZWPKVJNGWV7N6KJFUS235N27IP5NZ7UAXMXWUMILNLJA*  
  
Alice should then restart her Tor.  
  
If Alice needs keys for more onion addresses, she can repeat this process with additional files in this directory.  
  
Notes:   
The .onion suffix in the address is removed in those .auth\_private files.   
I haven't actually tried this on Tor Browser, I'm merely relaying what [a brave Redditor managed to figure out](https://reddit.com/r/TOR/comments/anu1f7/how_to_set_up_version_3_hidden_service_with/efzcdd9/?context=100).   
Tor Browser doesn't expect you to edit its torrc, so if you change Tor settings graphically in Tor Browser, you may find it has generated a new torrc without your changes.   
  
  
За матеріалами: [Creating Private V3 Onion Services - Matt Traudt](https://matt.traudt.xyz/p/FgbdRTFr.html)
