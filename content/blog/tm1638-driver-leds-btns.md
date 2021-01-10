---
title: "TM1638 driver - LEDs&Btns"
date: 2011-06-30T22:56:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["AVR"]
---

I've ordered one interesting module from DealExtreme. Today I've ported code from Intel x51 example sourcecode.

<!--more-->

I played with driver on Arduino to make it more easier for younger players :) Library is bit improved, you can display numbers, your own segments, turn on red and green LEDs and read buttons. It works very nice.

If you wan't true Arduino library for this module, I suggest you [this more advanced library](https://github.com/rjbatista/tm1638-library) based on my original code. It was improved by Ricardo Batista and supports also "chained" modules.

[Firmware](tm1638_test.zip)

{{<youtube "s5c2kZjGeVk">}}

![Tm1638](Tm1638.jpg)

## Example code

```c
unsigned char numbers[] = {1,2,0x0A,0x0B,0x0C,0x0D,0x0E, 0x0F};

// Text: "Hello"
unsigned char helloLCD[] = { 0,0b01110100, 0b01111001, 0b00111000, 0b00111000, 0b01011100, 0, 0};
// Text: "by Hub"
unsigned char byHub[] = { 0, 0b01111100, 0b01110010 ,0 , 0b01110110, 0b00011100, 0b01111100, 0};

void setup()
{     
  TM1638_init();                             
  TM1638_write_display(helloLCD);     
  TM1638_write_LED(0b00011111 | 0b11111000 << 8);
  delay(3000);
  TM1638_write_display(byHub);   
}

void loop()
{
  unsigned char i, key;

  key = TM1638_read_key();
  if(key < 8)
  {
    for(i=0; i<8; i++)
      numbers[i] = key;
     
    TM1638_write_numbers(numbers);

    // Alternate RED & GREEN LEDs
    TM1638_write_LED(((key % 2 == 0) ? (1 << key) : 0) | (key % 2 ? 1 << (key+8) : 0));
  }
}
```
