---
title: "LCD Menu v1.0"
date: 2012-04-25T12:27:38+06:00
description : "This is meta description"
type: post
image: blog/lcd-menu-v1/image.jpg
author: Martin Hubáček
tags: ["lib"]
---


This small code allows you to create simple menus in your project.

<!--more-->

![](DSC_0070.JPG)

### Features

* Tested on AVRs ( incl. Arduino ) and ARMs
* Needs just four keys: left, right, up, down
* Works on **character** and **graphical LCDs**
* **Multilanguage**
* Simple configuration in file `menu.h`
* Has also some optional number/temperature/time edit dialogs, but needs more tweaking

### Download

[Download menu library v1](MenuV1.zip)

### Examples
The code itself is well commented and also has the samples in the comments.

```
static char *mainMenu[] = { "Menu title", "First item", "Second item", 0};

switch(showMenu(mainMenu))
{
 case 0:
     // First item selected
     break;
 case 1:
     // Second item selected
     break;
}
```

All you need to do is provide to the menu library your functions for clearing the screen and placing the text.


```
// Clear display
#define displayClear()    lcdBufferClear()

// Display string
#define displayString(str, posx, posy) lcdBufferString(str, posx, posy)
// If you have separate functions for set position and print text, use define below
//#define displayString(str, posx, posy) {lcdGotoXY(posx, posy); lcdBufferString(str);}
```
### Buttons 

This library doesn't handle keypresses itself, you have to provide events to the globar variable keyPress, allowed values are `BTN_LEFT`, `BTN_RIGHT`, `BTN_UP` and `BTN_DOWN`. After handling of this event the menu sets the `keyPress` variable back to zero and waits for another keypress. Your key press events have to be set in your interrupt routine which scans the keys and deals with deboucing etc.

### Optional configuration

The menu can have more than one language, you can change them on runtime. In that case the code looks like this:

Global value `actualLanguage` selects the language

```
static char *mainMenu[] = { "Language 0 title", "lang0 item", "lang0 item", 0,
                                        "Language 1 title", "lang1 item", "lang1 item", 0};
```

Nice option is to create second array of function pointers and call them afterwards (ARM impelmetation):

```
static char *mainMenu[] = { "microCmd", "Joystick", "Painting", "Clock", 0, };
static void (*funcMenu[])()= {appJoystick, appPaint,  appClock};

int menuSel = - 1;

for(;;) {
   // Preselect last selected item
   menuPreselect = menuSel;

   // Call menu
   menuSel = showMenu(mainMenu);

   // If user pressed BTN_RIGHT, run function
   if(menuSel != -1)
       (*funcMenu[(uint8_t)menuSel])();
   else    // else exit from the loop
       break;
}
```

Here's the configuration part from `main.h`

```
// www.martinhubacek.cz
// Martin Hubáček 21.4.2012

// Define some macros according to your display APIs,
// used languages and configure menu size

// Your code has to provide events to the variable keyPress
// you have to feed this values from your keyboard interrupt routine!
// Necessary values are BTN_LEFT, BTN_RIGHT, BTN_UP and BTN_DOWN

// If you use different languages, define them here
#define LANGUAGE_CZECH 0
#define LANGUAGE_ENGLISH 1

// Set rows/cols based on your font (for graphical displays)
#define ROW(x) ((x)*8)
#define COL(y) ((y)*6)
// For character LCDs use definitions below
//#define ROW(x) (x)
//#define COL(y) (y)

// Number of items on one screen
// Not including title
#define MENU_LINES 5

// Symbol which is displayed in front of the selected item
// This symbol doesn't appear when MENU_LINES == 1
#define ARROW_SYMBOL ">"
// How many spaces is between arrow symbol and menu item
// useful to set zero on smaller displays
#define ARROW_GAP 1

// Clear display
#define displayClear()    lcdBufferClear()

// Display string
#define displayString(str, posx, posy) lcdBufferString(str, posx, posy)
// If you have separate functions for set position and print text, use define below
//#define displayString(str, posx, posy) {lcdGotoXY(posx, posy); lcdBufferString(str);}

// Display number
#define displayNumber(str, posx, posy) lcdBufferNumber(str, posx, posy)
// If you have separate functions for set position and print number, use define below
//#define displayNumber(str, posx, posy) {lcdGotoXY(posx, posy); lcdBufferNumber(str)}

// Optional function to write buffer to display - comment if not used
#define displayDraw()        lcdBufferDraw()

// Optional edit dialogs
// Needs some more tweaking directly in menu.c according to your drawing routines
//#define MENU_EDIT_NUMBER
//#define MENU_EDIT_TIME
//#define MENU_EDIT_TEMPERATURE

// Some stdlib definitions according to your compiler
#define strlen(x) ustrlen(x)

// Nothing more to configure, that's all
// -----------------------------------------
```