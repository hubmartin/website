---
title: "LVDS LCD Hacking with FPGA"
date: 2015-04-24
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["fpga"]
---

For first serious FPGA project I had to wait a while for laptop LCD. I scored quite good LCD panel which had enormous resolution for my first try. It took many hours just to turn the display on with white screen.

<!--more-->

Now I can display any pattern on the screen. The real limit is really now only the lack of external RAM. But for my first experiments the Lattice MachXO2 will do the job.

Now I can display any color on any pixel. There is not that issue with blue color that I mentioned in the second video. I have made code for seven-segment digits that will be used as clock for now. Later I plan to display some small color images from internal ROM and later I would play with small VGA camera.

I've published two videos containing also some hacking of notebook

https://www.youtube.com/playlist?list=PLJawRcvzfp-KChT5BoQ7Tx7oZo5qBf5h3

{{<youtube "MXM3ovkfT74">}}

{{<youtube "BldAWWNUepo">}}

# VHDL Code

https://github.com/hubmartin/FPGA-LVDS-LCD-Hack

# Issues

Since this is my first FPGA project and the Lattice is running on the edge of maximum frequency I have some issues with synthesizing more complicated VHDL. I can display just a simple 7-segment numbers right now. If I do more complicated image then the image does not appear. There must be bottleneck somewhere. It could also be that I'm running the FPGA on the edge of the maximal frequency.

## Full-HD 7-segment death clock counting to the moment I leave (work)

Hours and minutes till I leave the work every day :)

![PCB](CH23oefXAAAbBBk.jpg)
