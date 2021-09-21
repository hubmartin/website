---
title: Waveshare E-paper HAT issues
description:
date: 2021-09-21
type: post
image: blog/waveshare-epaper-hat-issues/preview.jpg
author: Martin Hubáček
tags: ["epaper","waveshare"]

---

I used few smaller e-papers with ESP32. I also tried 7.5" V2 display but I had memory and reboot issues on ESP32. First I wanted to make low power battery operated client display with ESP32, but now I changed my mind and I use power supply with ESP32 and epaper.

With permanent power supply and issues with ESP32 developemnt, I wanted more flexible update, debug and control of my devices. So I connected Raspberry Pi 3B+ and created a simple client script which just displays received PNG image over SSH ([rpi-epaper](https://github.com/hubmartin/rpi-epaper)). The server part creates a screenshot and sends it to the Rpi clients with displays.

This worked fine until I created final hardware with Raspberry Pi Zero W. This is really small Rpi with slower processor, but WiFi. The code worked fine for a while but the code got stuck sometimes with `e-Paper busy` text.

I found quite a lot of people solving this issue on github issues and forums. They claimed that with 3B+ RPI it works fine, with Zero they have power issues. They also mention that the 40 pin connector has this busy issue and when they use just the wires from second connector to the Rpi gpio pins, the issue disappears.

I've had this `e-Paper busy` issues on my Zero W only when the image was send over SSH. When I updated the picture locally, then it never had this issue of `e-Paper busy`.

So I connected BUSY signal to the scope and checked it when the display worked, and when it had `e-Paper busy` issue.

Busy OK when loaded locally

![](busy_ok.jpg)

BUSY fails when image is send over SSH

![](busy_fail.jpg)

I did a lot of tests. The SCP copy failed every second try. While the local image never failed. This, and the analog-style of BUSY signal let me to focus on power supply. The idea was that the WiFi transfer and the powering on of the display created a current spike that the RPI did not liked.

Later I realized, that the BUSY signal has 5 V voltage level. The RPI has 3.3 V tolerant inputs! There is no resistor in series so the current was limited only by the 8 channel bidirectional level shifter TXS0108E on the HAT. This might also be the issue that RPI sometimes rebooted.

Now it clicked together - on forums people wrote that they had issues with [permanent ghosting picture](https://github.com/waveshare/e-Paper/issues/46) on epaper displays when powered over 40 pin connector. Never when they used the separate wires wired to the second signal connector.

So I checked [Waveshare epaper HAT schematics](https://www.waveshare.com/wiki/File:E-Paper-Driver-HAT-Schematic.pdf) and discovered, that **40 pin connector used 5 V for power**, however the separate pins and the [documenation signals table](https://www.waveshare.com/wiki/E-Paper_Driver_HAT#Hardware_connection) says you have to connect VCC to the 3.3 V RPI pins.

![](5v-signal.png)

So I removed two 5 V pins from my Rpi zero and supplied 3.3 V to the VCC HAT power input. This fixed the 5 V going to the RPI but the pictures over WiFi still had issues to display in most cases and stuck on `e-Paper busy`.

I've found some suggestion to add the timeout to the while loop. I've added some links to the similar issues in my github repo [issue #1](https://github.com/hubmartin/rpi-epaper/issues/1).

I've added [this timeout](https://github.com/hubmartin/rpi-epaper/commit/5ddebd3ffde8f99289fb343089ceea90d8726824) so at least the code did not get stuck here. But investigation continues.

## Focus on power

I've connected BUSY and switched VCC signal to check the power supply to the epaper display. Because the BUSY signal went slowly down, I suspected the powering voltage might be the issue.

And I was right. When the local image was displayed without an issue the voltage looked like this:

![](reset-short.jpg)

You see the small voltage dip, interesting.. Now the trace with failed attempt to redraw epaper and `e-Paper busy` stuck.

![](reset-long.jpg)

You see that the yellow BUSY trace in the middle is much longer in the logical 0 state. And the voltage goes basically to the zero.

The code has [2 ms delay in Python](https://github.com/hubmartin/rpi-epaper/blob/main/client/waveshare_epd/epd7in5_V2.py#L108) but the pulse is 15 ms long? Now it started to make sense. The short pulse resets the epaper, but does not have time to disable power. However when HAT have to be shut down to lower power consuption, the same RESET signal is used to cut power. So longer pulse compeltely disconnectes power.

Because Python or Linux is not realtime operating system, then when relatively underpowered Raspberry Pi Zero W has some other work to do, it makes this 2 ms delay sometimes over 15 ms long. This way the LDO and power MOSFET disconnects power and the display is completely shut off.

## Solution?

So what can we do about it? Since this HAT will be used forever with my Rpi Zero W I do some hacking. There might be some other solutions, but I lost already lot of time analyzing this, so let's get it to work somehow.

The first thing is to have 3.3 V for power. I removed two pin headers with 5 V power on Rpi and wired 3.3 V pin to the VCC pin on the HAT 40 pin connector. I also stuffed some plastic inside those 5 V pins in case I forget and try to put it to RPI with all 40 pins populated.

The second fix was the enable pin of the LDO. I cut the trace and connected EN to the VIN so it never disconnects the power. However there is MOSFET before this LDO which also shuts down the power. So power is disconnected during the HAT is in the sleep. I'm not sure why there is power disconnected by the MOSFET and the LDO. One point to disconnect should be ok, but this HAT might be more universal and maybe they have it for some other kit and edge case.

![](hack-schematics.png)

![](hack-overview.jpg)
![](ldo-zoom.jpg)
![](plastic.jpg)
![](rpi-5v-pins-removed.jpg)


## Final tests

Now even when the RESET pulse is longer than 15 ms, the voltage is fine. It gets a little lower, but I'm not going to investigate it now, it works for me :)

![](scope-fixed.jpg)
![](final.jpg)


