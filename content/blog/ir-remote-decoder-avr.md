---
title: "IR remote control decoder - AVR"
date: 2011-06-15T22:50:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["AVR"]
---

- Support for NEC, Philips RC5 and Sony SIRC protocols
- Able to learn one code and then react to this code - turn on the computer
- You can use crystal with any frequency you would like - no fixed timing
- Codes are send over UART so you can process the remote codes on PC for example in EventGhost
- Developed for ATmega8, easily portable to other ATmega & ATtiny

<!--more-->

## Protocols

Software decoder supports all protocols at once. Based on the length of the first pulse the software decides which protocol should use to parse the input signal.

**NEC protocol**

Mainly Japanese products http://wiki.altium.com/display/ADOH/NEC+Infrared+Transmission+Protocol
LG, XORO, GENIUS
Full implementation, long button presses are processed correctly
You need 38kHz infrared receiver & decoder

**Philips RC5**

http://wiki.altium.com/display/ADOH/Philips+RC5+Infrared+Transmission+Protocol
You need 36kHz infrared receiver & decoder

**Sony SIRC**

http://picprojects.org.uk/projects/sirc/
Repeatable button presses are processed with some delay.
You need 40kHz infrared receiver & decoder


## Hardware

Developed on ATmega8, possible to port to any ATmega/ATtiny with stack pointer and 16bit timer.

IR sensor & demodulator
NEC: TSOP2238, SFH-5110-38 (38kHz)
RC5 : TSOP2236, SFH-5110-36 (36kHz)
SIRP: 40kHz

I hate if I have to correct timings in someone's project just because I have different crystal frequency.

Now you can use any crystal from range about 1MHz to 20Mhz!
Only if you need accurate UART baudrates, you'll have to use crystal with "magical" frequencies like 11,0592MHz etc.

- IR sensor pin connected to PD2
- Learn IR code jumper PB0
- Computer switch contact to PB1
- Debug LED - connect anywhere on PORTC

## Software

The received code is send over TX (PD1) pin with speed 115200 baud. The text format is "0x12EF\n\l".
For NEC: The first byte (higher 8 bits) is remote control address and the second byte (lower 8bits) is key code.

The code is I hope clear enough and self-explainable.
The pulses are measured with free running 16bit Timer1. There's no interrupts or Input Capture.

✓ - timing is reliable enough

|  Crystal   | Timer | NEC | RC5 | SIRC |
| ---------- | ----- | --- | --- | ---- |
| 7.3728MHz  | 8b    | ✓   | ✓   | ✓    |
|            | 16b   | ✓   | ✓   | ✓    |
| 8MHz       | 8b    |     |     |      |
|            | 16b   |     |     |      |
| 11.0592MHz | 8b    | ✓   | ✓   | ✓    |
|            | 16b   | ✓   | ✓   | ✓    |
| 14.318     |       |     |     |      |
|            |       |     |     |      |
| 16MHz      | 8b    |     |     |      |
|            | 16b   |     |     |      |

## History

- 1.0 - Initial version
- 1.1 - Optional support for 8bit Timer0, so you can use Timer1 and Timer2 for 3 PWM on ATmega8
- 1.2 - Better synchronization of bits in RC5 protocol. Added bit error check for NEC & RC5 so they are perfectly reliable.

## Download

- [IR receiver project](IR_receiver_v1_0.zip)
- [IR decoder lib](ir_decoder_v1_2.zip)