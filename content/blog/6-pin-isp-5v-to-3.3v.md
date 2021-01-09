---
title: "AVR 6-pin ISP 5V to 3.3V"
date: 2011-01-22T22:50:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["AVR"]
---

This simple PCB can be used if you have 5V Atmel programmer and you want program 3.3V device. 

<!--more-->

This board have also LF33 linear stabilizer so you can power your target board too. It's a bit older design where are missing capacitors. This was designed to use parts I already had at home. Today I would use proper level shifter. But it did its job perfectly fine.

The traces are curved just because I wanted.. for fun :)

[EAGLE CAD data](usbProgrammer3v3.zip)

![layout](prg.png)

![schematics](schm.png)
