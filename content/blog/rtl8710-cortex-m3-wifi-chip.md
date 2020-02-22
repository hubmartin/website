---
title: "RTL8710 Cortex M3 Wifi chip"
date: 2013-03-18T12:27:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["lib"]
---

On this page I put findings related to my RTL8710 Cortex M3 Wifi board.

<!--more-->

![](rtl8710-module.png)

### Pinout
- SWDIO: GE3
- SWCLK: GE4

### RGB LED
- GC4
- GC0
- GC1

Button is routed to the GC3. On GC3 is 3V and the button grounds this pin to GND

### Links

- https://hackaday.io/project/19163/logs
- https://hackaday.io/project/19163-rtl8710-easy-programming-by-arduino-ide