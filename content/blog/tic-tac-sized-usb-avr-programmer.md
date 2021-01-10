---
title: "Tic-tac sized usb avr programmer"
date: 2011-01-30T22:56:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["AVR"]
---

This is my board layout for USBasp programmer. On the [fischl programmer homepage](http://www.fischl.de/usbasp/) is a lot of designs, different MCU types and package sizes of processors. But nothing that I would like. I wanted really small size but with the same features. I used the original schematics from Fischl and created new layout with some SMD parts.
<!--more-->

The biggest problem was with the biggest part - Atmega8. I have tried lot of layouts of MCU and connectors but this final seems to be the best. [Someone have similar](http://tomeko.net/misc.php#USBasp) but I wanted stick all parts together as much as it was possible. Some resistors and capacitors were replaced by SMD packages.

I was using also [AvrUSBboot](http://www.fischl.de/avrusbboot/) but it happened a lot of times that the MCU somehow slightly reprogrammed itself and that made me always crazy. So only the programmer firmware is on the bottom of this page in attachments.

Jumper1 selects if you want power to the target device. It's 5V board so you can program only 5V targets.
JP2 - connecting pin 1-2 will use slower clock cycle because the firmware is older one a cannot change the speed with software from PC.
connecting pin 2-3 has no purpose, this was uses in original design to start bootloader.

[EAGLE CAD data](usbNewProgrammer.zip)

[Firmware](NewProgrammerFW.zip)

![PCB picture](DSC_8334.jpeg)
![PCB picture](pcb.jpg)
![PCB picture](schematics.jpg)
