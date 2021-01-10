---
title: "USB Rotary encoder"
date: 2011-06-30T22:56:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["AVR"]
---

Long time ago, I wanted x86 car computer with Linux, MP3 music and cool control of the system like X-Drive :)

<!--more-->

I've made only prototype of the control with mechanic rotary encoder. **Interrupt routines are poorly designed and does not debounce input signals. So faster scrolling is almost impossible to make.** I cannot believe how fast I could rotate the knob on that videos below. Today I took the prototype and it was barely usable at all. So take inspiration, but don't take the code ;)

Device is based on [HIDkeys](http://www.obdev.at/products/vusb/hidkeys.html), I did only tiny changes in input part of the code. The prototype is soldered on protoboard, I have no schematics but you should hold the original HIDkeys schematics with only different, that there are not buttons connected but only one rotary encoder.

The common wire from rotary encoder is connected to the ground on the protoboard.
PhaseA and PhaseB signals are connected PIND3 (INT1) and PIND4. The software will turn on the internal pull-ups.

[Firmware](Rotary_encoder_HIDKeys.zip)

![Tm1638](5834074107_292bd4d4db_b.jpg)

![Tm1638](5834627004_a06310e573_b.jpg)


{{<youtube "t0HXBEH2BeE">}}

{{<youtube "AqUqlN9PWJU">}}
