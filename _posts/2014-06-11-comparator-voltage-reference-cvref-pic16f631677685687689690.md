---
layout: post
title: "Comparator Voltage Reference CVREF (PIC16F631/677/685/687/689/690)"
date: 2014-06-11 11:28:00 +0000
tags: ["microchip", "microcontroller", "pic"]
blogger_orig_link: https://lexxai.blogspot.com/2014/06/comparator-voltage-reference-cvref.html
---

Додаю як нотатку, результати розрахунку напруги CVREF компаратора мікроконтролерів #Microchip #PIC16F631/677/685/687/689/690  

[![](/assets/images/blog/cc5cced09c90d9ca-916bbe2178208786.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEji3nUXDLChMTRjAIa5pnGbCl9PN2xOQbndHrOLyoM4DtSbgfi84O5hCa2wu8GDbZvdHDskN8VB3ht2trRkAXfInfZKVohNvZAjgtFVy9iqyzGQRHN-VLma3mDeME9jJpY-EOjF1eYyVbzs/s1600/Comparator+Voltage+Reference+CVREF.PNG)  
*Comparator Voltage Reference CVREF (PIC16F631/677/685/687/689/690)*

VRR = 1: CVREF = (VR<3:0>/24) \* VDD  
VRR = 0: CVREF = VDD/4 + (VR<3:0>/32) \* VDD
