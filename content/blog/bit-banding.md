---
title: "Bit-Banding - fast and safe bit manipulation"
description:
date: 2018-04-02
author: Martin Hubáček
menu:
  main:
    parent: tutorials
---


This is short text summary of my Advanced ARM Tips video

{{< youtube "h78DyF1NOio" >}}


In your code, sometimes you need to flip a single bit in your flag variable to signal that for example the doors are open or the output LED should be turned on. In this situation you can use typical way of setting a bit with OR command which looks like this:

```
// Set the first bit (1 << 1) in flag variable
flag |= 0x02;
```

This can work well but at some circumstances it can cause issues. If you use interrupts and you also access the same variable for writing a race condition can occur. This is caused because the C language OR instruction in the example above it's actually compiled to three assembler instruction.

In ARM disassembly you can see:

```
08000358:   ldr r3, [r7, #4]      // Load variable from RAM to register
0800035a:   orr.w r3, r3, #2      // Apply OR expression
// >> Interrupt fires here
0800035e:   str r3, [r7, #4]      // Write the result back to RAM
```

If you have this example code in main loop and after the OR instruction the timer or pin change interrupt is fired. Then if you check the same variable in your interrupt routine you don't see yet the bit set in the RAM. This can lead to data race issues. Its better if you google different causes on different platforms.

To prevent this race conditions you have several options.
On AVR you can use SBI  and CBI for setting or clearing bit in I/O register. There also instruction SBR and CBR for setting and clearing the registers. But you don't have any atomic instruction for bit access to the internal SRAM. You can solve this on AVR by disabling the interrupts for a while so you can be sure no interrupt is fired in the critical section.

On ARM Cortex M3 M4 and M7 you have option to do a bit-banding. You can write a byte value in the Bit-band memory alias region that will atomically change a single bit in your normal SRAM memory space. Nice explanation with bit-band examples is on the ARM website.

So what we need is to make life easier and create MACRO definitions for each of the RAM and peripheral memory space:

```
#define BITBAND_PERIPH(address, bit) ((__IO uint32_t *)(PERIPH_BB_BASE + (((uint32_t)address) - PERIPH_BASE) * 32 + (bit) * 4))
#define BITBAND_SRAM(address, bit) ((__IO uint32_t *)(SRAM_BB_BASE + (((uint32_t)address) - PERIPH_BASE) * 32 + (bit) * 4))
```

Then you create a variable which will point to bit-band alias of our flag variable by calling:

volatile uint32_t flag = 0;
volatile uint32_t * const bbFlag = BITBAND_SRAM(&flag, 1);

Volatile is there so optimizer don't do clever things like removing your code from output binary. And now you only write value one or zero to change your single bit in "flag" variable:

```
*bbFlag = 1;
*bbFlag = 0;
```

And thats it. More practical examples are in the video on my YouTube channel above.

Another Bit-band related articles:

http://download.mikroe.com/documents/compilers/mikroc/arm/help/arm_m4_specifics.htm
https://spin.atomicobject.com/2013/02/08/bit-banding/
http://mightydevices.com/?p=144
http://www.micromouseonline.com/2010/07/14/bit-banding-in-the-stm32/
http://www.scienceprog.com/bit-band-operations-with-arm-cortex-microcontrollers/