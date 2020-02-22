---
title: "microCmd"
date: 2012-02-02T12:27:38+06:00
description : "This is meta description"
type: post
image: blog/microcmd/image.jpg
author: Martin Hubáček
tags: ["lib"]
---


MicroCmd is a small battery-operated pocket device. This is the first time I used ARM LM3S5632 chip in my construction - there's nothing to be afraid of as I figured out:)

<!--more-->

### Hardware
- Nokia 3310 LCD
- 3 axis I2C accelerometer
- microSD slot
- mini USB connector
- battery charger circuit
- PlayStation Portable 2 axis analog "joystick"
- IR LED diode to rule all IR devices at home :)

 .. and.. that's it! :)
All that was squeezed to small area 59×38mm. The 2 layer boards will be maufactured by Pool Service in fab house in my country.

### Files

- [Hardware (KiCAD)](microCmd_KiCAD_board.zip)
- [Firmware (CoIDE)](logger_CoIDE_firmware.zip)
- [Schematics (PDF)](schematics.pdf)
 
### Gallery

**More pictures [in the gallery](https://picasaweb.google.com/108501288829330154737/MicroCmd)**

![](bottom-side.jpg)

{{< youtube "H0bVR4JFMfw" >}}

### Construction bugs

**NOT YET FIXED IN SCHEMA/PCB**

-   LCD connector is mirrored, but it could be fixed by using wires and rotating it by 90 degrees. I had to do some framebuffer and software rotating because there's not hardware support for drawing data upside down. But now I see that this layout of LCD is more suitable because the visible area of LCD is starting right of the top of the device.
-   **Accelerometer's footprint has smaller pin pitch** :( So no accelerometer on these boards :( The good news is that the soldering of this package it's not so hard as I thought. The only thing you have to do is prepare some sort of heat holes under the chip to the opposite side of the PCB.
-   Ordered 2,5V step-down DC/DC converter instead of 3.3 V, so I had to do some voltage divider circuit, but fixed :)
-   There's some Errata in the RTC of the LM3S5632 so I had to route wire from external crystal also to the RTC clock input, this could count seconds but I have no idea how precise this will be.
-   There's missing capacitor near the voltage input for Li-Ion charger chip. After small reworking charging works.

Ok, now view from the other side - what is working now in software & hardware?:

-   LCD with frame buffer & Joystick
-   Deep Sleep mode - consumption it's not perfect but I can live with 4mA right now.
-   Timed wake-up useful for logging
-   Access to SD card
-   SD card file browser
-   IR transmitter
-   Battery charging

Now I have to focus to tweak the hardware and low level software, the I create some nokia-like snake game, later IR remote control and some sort of USB device.

### ToDo

-   RTC trimming
-   Voltage logger to CSV files on SD card
-   USB connectivity
-   Text and image file browser

