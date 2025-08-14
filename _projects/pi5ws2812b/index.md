---
layout: post
title: Pi5WS2812b
description: The Raspberry Pi 5 added a separate chip for handling its peripherals (RP1) which none of the previous Pi models had. Pre-existing Open Source packages that controlled WS2812b LEDs are now obsolete. This repo enables control of the popular WS2812b LEDs using the SPI MOSI pin on your Pi5.

main-image: /example.jpg
---

---
## Control WS2812B LED Matrix using your Raspberry Pi 5
This is a C Driver for the new Raspberry Pi 5 RP1 Peripheral chip that can be used to control your WS2812b LEDs.  
Run from your Linux based Raspberry Pi 5 terminal

Huge kudos to [Praktronics](https://github.com/praktronics) for figuring out the SPI RP1 register addresses


### To Run Example which turns on 1st LED Green:
Ensure hardware connections are made as shown in Connections photo below:  
{% include image-gallery.html images="connections.jpg" height="500" %}  

```bash
git clone https://github.com/jovank2001/pi5ws2812b.git
cd pi5ws2812b
gcc rp1Spi.c rp1SpiUtil.c pi5ws2812b.c -o run
sudo ./run
```

Example Result:

{% include image-gallery.html images="example.jpg" height="500" %}  

