---
title: "Evalbot LED blink"
date: 2011-01-25T12:27:38+06:00
description : "This is meta description"
type: post
image: blog/evalbot-led-blink/image.png
author: Martin Hubáček
tags: ["ARM", "Evalbot"]
---

Today arrived Evalbot kit from Texas Instruments so I built it together and tried hello world application. I'm using arm-none-eabi GCC compiler so if you don't like IAR compiler, you can start your project from my sample. 

<!--more-->

In the sourcecode is header file from another kit from LM so change it. I still don't donwloaded new Stellarisware and I'm using header filer from my older kit with LM3S6965 processor.

For uploading you'll need [LM Flash Programmer from LuminaryMicro](http://focus.ti.com/docs/toolsw/folders/print/lmflashprogrammer.html). For compilation you'll have to download [StellarisWare](http://focus.ti.com/docs/toolsw/folders/print/sw-lm3s.html) for sourcecode and header files and [Sourcery G++ compiler](http://www.codesourcery.com/sgpp/lite/arm/portal/release830).

In zip file is complete project with Makefile.. it looks terrible but can do compilation and simple cleaning :)

The code bellow is just for your idea, you have to download the whole project with other files to run this sample application.

[Download source project](LM3S9B92evalbotledblink.zip)

LED2 is on the PIND5

```
#include "inc/lm3s6965.h"
#include "inc/hw_types.h"
#include "driverlib/gpio.h"
#include "driverlib/sysctl.h"
#include "inc/hw_memmap.h"

// Evalbot hello world - blinking
// Martin Hubacek
// electronics.martinhubacek.cz

// System timer interrupt
// look in gcc_Startup.c a search for SysTickHandler to
// understand how work with interrupts
void SysTickHandler()
{
  // Invert pin4
  GPIO_PORTF_DATA_R ^= 0x01 << 4;
}


void main(void)
{
  // Enable systick - system timer
  SysTickPeriodSet(SysCtlClockGet() / 1);
  SysTickEnable();
  SysTickIntEnable();

   // LED is on pin PF4
    SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOF);
    GPIOPinTypeGPIOOutput(GPIO_PORTF_BASE, GPIO_PIN_4);

  // Main program does nothing so do an infinte loop
  while(1);
 
}
```