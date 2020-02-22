---
title: "LCD Menu v2.0"
date: 2013-03-18T12:27:38+06:00
description : "This is meta description"
type: post
image: blog/lcd-menu-v2/image.jpg
author: Martin Hubáček
tags: ["lib"]
---


Improved and more advanced menu for small embedded systems.

<!--more-->

### Features

* Simple implementation on character or graphic displays
* Multilanguage support
* Checkbox items
* Items with displayed parameters: integer, string
* Automatic transparent update of menu and parameters on the screen in time
* Callbacks for every item
* Scrolling with cursor and selected item in the middle on the screen, so you won't accidentaly skip item that you're looking for

### Download

[Download menu library v2](m2lib_2_0.zip)

### Demo

{{< youtube "H0bVR4JFMfw" >}}

### Simple example

![](simple_menu_320.png)

```
#include "m2lib/m2.h"

// Simple menu
MenuItem menuItem0 = {{"First"}};
MenuItem menuItem1 = {{"Second"}};
Menu simpleMenu = {{"Simple Menu"}, .items = {&menuItem0, &menuItem1, 0}};

void m2Test()
{
    unsigned char ret;

    // Simple menu
    // return value is index of the selected item
    ret = menu2(&simpleMenu);
}
```


### Advanced menu example

![](advanced_menu_320.png)

```
//
// Advanced menu definitions
//

// Items
MenuItem item0 = {{"Tick"}, callback, MENU_PARAMETER_IS_NUMBER | MENU_CALLBACK_IS_FUNCTION, (int)&msTick};
MenuItem item1 = {{"Lang SubMnu"}, (void*)&subMenu,  MENU_CALLBACK_IS_SUBMENU, 0};
MenuItem itemTime = {{"Time"}, callback, MENU_PARAMETER_IS_STRING | MENU_CALLBACK_IS_FUNCTION, (int)"12:05"};
MenuItem item2 = {{"Count"}, callback, MENU_ITEM_IS_CHECKBOX | MENU_ITEM_IS_CHECKED | MENU_CALLBACK_IS_FUNCTION};
MenuItem item3 = {{"Lights"}, callback, MENU_ITEM_IS_CHECKBOX  };
MenuItem item4 = {{"Sound"}, callback, MENU_ITEM_IS_CHECKBOX | MENU_ITEM_IS_CHECKED };
MenuItem item5 = {{"Fan"}, callback, MENU_ITEM_IS_CHECKBOX };
MenuItem item6 = {{"Buzzer"}, callback, MENU_ITEM_IS_CHECKBOX | MENU_ITEM_IS_CHECKED };
MenuItem item7 = {{"Last check"}, callback, MENU_ITEM_IS_CHECKBOX };

// Menu
// Notice that some items are duplicated, the same menuItem can be used in multiple menus or in thee same menu
Menu menu = {
    {"Menu ver2"},
    .items = {&item0, &item1, &itemTime, &item2, &item3, &item2, &item5, &item6, &item7, &item3, &item4, 0},
    .refresh = 200
};

void m2Test()
{
    // Advanced menu
    menu2(&menu);
}

// Callback
void callback(void *m)
{
    Menu* menu = ((Menu*)m);
    MenuItem *selectedItem = menu->items[menu->selectedIndex];

    // Increment value in second menu item
    if(selectedItem == &item2)
    {
        selectedItem->flags |= MENU_PARAMETER_IS_NUMBER;
        selectedItem->parameter = (int)&testVal;
        testVal++;
        return;
    }

    displayClear();
    displayString("Item callback", 0, ROW(0));
    displayString("Index:", 0, ROW(1));
    displayNumber(menu->selectedIndex, COL(8), ROW(1));
    displayString("Item name:", 0, ROW(2));
    displayString(selectedItem->text[menuLanguage], 0, ROW(3));
    displayDraw();

    while(evtGet() == 0);
}
```

### Multilanguage menu example

![](english_menu_320.png)

```
//
// Submenu definitions, multilanguage menu
//

// Items
MenuItem aa = {{"Prvni", "First"}, 0, MENU_PARAMETER_IS_NUMBER, (int)&msTick};
MenuItem bb = {{"Druha", "Second"}, 0, MENU_PARAMETER_IS_NUMBER, (int)&testVal};
MenuItem cc = {{"Zmen jazyk", "Change lang"}, switchLanguage, MENU_CALLBACK_IS_FUNCTION};
// Menu
Menu subMenu = {{"Czech Menu", "English Menu"}, .items = {&aa, &bb, &cc, 0}, .refresh = 1000};

// Simple switching between two languages
void switchLanguage(void *m)
{
    menuLanguage = menuLanguage == LANGUAGE_CZECH ? LANGUAGE_ENGLISH : LANGUAGE_CZECH;
}
```