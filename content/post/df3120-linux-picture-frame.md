---
title: DF3120 Linux picture frame
description:
date: 2018-04-02
menu:
  main:
    parent: tutorials
---
Here are some useful links. I had many errors during compilation so I post also my own changes in configuration of minifs.

The good place to start are these two spain articles part 1 (translated) and part 2 (translated).

Of cource I cannot forget about the main page of the group, that ported Linux to this device https://sites.google.com/site/repurposelinux/df3120

Another useful straightforward guide you can see here http://w.droidcloud.info/ParrotInstruct.html

This utility maybe could put IMG file without need to install Linux https://launchpad.net/win32-image-writer

My own compiled image file can be downloaded here. I have enabled CHMOD so if you have toolchain and do not want to compile image, this is the file for you.

Zlib - You have to change zlib donwload location, search configuration files for 1.2.5, you have to change the URL to version 1.2.6
autolib - the same here, last version is donwloaded but you have to supply version 2.2.4 in your config


## Ubuntu
I'm running on Windows 7 Pro, for development purporses I installed VirtualBox with Ubuntu 10.04 LTS.

## Flashing IMG file:
I was not succesfull with DD copying the file to the SD card. First I copyied the IMG file to the /dev/sdb but you have to create some partition ( for example with gparted ) and then copy the IMG to /dev/sdb1 (notice the number 1).
