---
title: EvalBot peripherals code
date: 2011-01-25T12:27:38+06:00
description : "This is meta description"
type: post
image: blog/evalbot-peripherals-code/image.png
author: Martin Hubáček
tags: ["ARM", "Evalbot"]
---

I put together all the interesting examples from Stellarisware and ported them to work together on Evalbot and CooCox CoIDE.

<!--more-->

Now some rubbish from the comments:

```c

/*
 * Start with EvalBot is little harder because
 * there are no ported examples of peripherals in Stellarisware.
 *
 * This code can help you to start, develop and learn with EvalBot.
 *
 * If you compile this example in CooCox CoIDE, you can try these functionality:
 *
 * - MicroSD card slot can be used to read data
 * - USB device connector is USB Mass Storage and you can access through it to the microSD card
 * - OLED display is initialized and shows some debug info about card and ethernet IP
 * - There's lwIP ethernet stack with DHCP client running on the EvalBot
 * - You can browse web page thanks to the httpd. Pages are in folder /fs. make_filesystem.bat is for converting the folder to *.h file
 * - SSI (server side include) is enabled and there's on example in index.shtml
 * - You can find Evalbot on your network with Locator utility from Stellarisware /tools/bin/finder.exe
 * - You can flash firmware with LM Flash over ethernet
 *
 * All source code is from StellarisWare. I ported examples from other part's and edited them so they could
 * be compiled in CoIDE and run on Evalbot. I had to change definition of the microSD card pins, set USB multiplexer
 * to the right connector and spent about a 2 more days to learn and force the USB to enumerate, because Evalbot doesn't have
 * connected USB_ID pin :)

 * libdriver.a and libusb.a is includes. Only the LWIP is loading from the Stellarisware directory.


*/
```

So what's this good for? You can take this as the starting point for your experiments with this perfect board. Good luck

[Download evalbot project](hub_peripherals_v1_0.zip)