---
title: InfiniTime on Colmi P8 smart watch
description:
date: 2021-09-13
type: post
image: blog/infinitime-on-colmi-p8/preview.jpg
author: Martin Hubáček
tags: ["InfiniTime","P8"]

---

(This article is still work in progress)

I wanted to test some cheap smart watches. The best type I could immediatelly buy in my country was Colmi P8. There were few open firmwares, but it was first hard to decide which is most complete so I investigated first and ordered the watches.

- [WASP-OS](https://github.com/daniel-thompson/wasp-os) is running MicroPython
- [ATCWatch](https://github.com/atc1441/ATCwatch) basic C++ firmware. The issue was that it needed special Arduino IDE.
- [InfiniTime](https://github.com/JF002/InfiniTime/) seemed most promising to me. It uses newest C++ heavily which I would need to learn, but the project seemed that its gaining a momentum and a lot of pull-requests.

First I tried an ATC firmware and DFU firmware update. Created a document for typing notes so I could write exact process of flashing. It freezed right after first DFU flash of hacked bootloader :) So knife to open and J-Link to the rescue.

So I tried to compile InfiniTime. After some battling it compiled fine. I flashed it and the watch came to the life... almost. Touch panel worked, button did not worked and after 7 seconds it periodically rebooted.

So I had to dig deeper and start to discover C++. For now I used only C in embedded programming with different MCUs and NRF chips. 

It turned out that 7 seconds is one of the intervals of wathchdog. So I searched the code for feeding the watchdog and it made sense. The button pin was different on P8 so the InfiniTime code thought, that the button is permanently pressed, which is a safety feature to reboot the wathces.

I found this image somewhere on [http://atcnetz.de/](http://atcnetz.de/) and after the pins were changed, the watch worked almost fine!

{{< load-photoswipe >}}
{{< figure src="/blog/infinitime-on-colmi-p8/p8-pinout.jpg" >}}

Also other 2 pins needed to be changed too. I used this script to automate change.

```
sed -i 's/chargingPin = 12;/chargingPin = 19;/g' src/components/battery/BatteryController.h
sed -i 's/pinReset = 10;/pinReset = 13;/g' src/drivers/Cst816s.h
sed -i 's/pinButton = 13;/pinButton = 17;/g' src/systemtask/SystemTask.h
```

Today also one of my PRs in the infinitime repo was added, so you can build firmware for P8 with new parameter `WATCH_COLMI_P8`.
Check [these build instructions](https://github.com/JF002/InfiniTime/blob/develop/doc/buildAndProgram.md#build).

```
cmake -DWATCH_COLMI_P8=1 -DARM_NONE_EABI_TOOLCHAIN_PATH=/home/martin/gcc-arm-none-eabi-9-2020-q2-update -DNRF5_SDK_PATH=../../nRF5_SDK_15.3.0_59ac345 -DUSE_JLINK=1 -DNRFJPROG=/usr/bin/ ../
```

I also don't use bootloader. So I compile just with

```
make -j pinetime-app
```

If you use bootloader, you have to change pins also there and recompile bootloader.

### Accelerometer

Accelerometer is different, it definitely is not BMA423/421. I took photos, checked footprint, read all 256 I2C registers to search for `WHOAMI` value. I checked over 10 manufacturets, 20 datasheets but nothing was close enough. However it behaved a little familiar when the register value has the highest bit set `0x80`. This is used in STM accelerometers so if you read more bytes on I2C, it is auto incremented. However based on `WHOAMI` register this wasn't STM chip.

Some time later I found somewhere on discord that it is `SC7A20`. A chinese copy of `LIS2DH12`. More investigation details in my [twitter thread](https://twitter.com/hubmartin/status/1418201291524743173).

Current 1.3.0 firmware with some PR improvements and wake on wrist can be found [on my github in p8acc branch](https://github.com/hubmartin/InfiniTime/tree/p8acc). This branch has hard-coded support for LIS2DH12 acceleromter. However this accelerometer does not have step functionality in hardware. So no steps for now.

### Touch screen

In 1.4.0 there are some improvements in InfiniTime firmware for PineTime touchscreen. PineTime is using `cst816s`, Colmi `cst716s`. You can find difference easily - 816 is sending swipe gesture while you stil drag your finger on the screen. Colmi P8 cst716s chip sends swipe gesture AFTER you release the finger.

This now breaks compatibility a bit, because the 716 aparently does not send some status flags/interrupts? It needs to be analyzed and fixed so 1.4.0 and newer firmwares could be used on P8.

### BLE NUS - bluetooth uart

I also added support to use Nordic BLE NUS uart service. With that you can connect over BLE to the watch and have simple console/terminal. In official Infinitime it is [not merged yet](https://github.com/JF002/InfiniTime/pull/560) but in my `p8acc` branch the support is there.

- [https://terminal.hardwario.com/](https://terminal.hardwario.com/) - You can use any chromium based browser with BLE support and connect from the web browser.
- [Python tools for Infinitime BLE NUS](https://github.com/hubmartin/InfiniTime-tools)


{{< figure src="/blog/infinitime-on-colmi-p8/IMG_20210803_151757.jpg" >}}
{{< figure src="/blog/infinitime-on-colmi-p8/IMG_20210723_232118.jpg" >}}

