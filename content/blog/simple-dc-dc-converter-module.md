---
title: "Simple DC/DC converter module"
date: 2011-01-24T22:56:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["electronics"]
---

This simple DC/DC step-down converter is perfect for breadboard design or replacement of 78xx stabilizers. It's not so powerful but it can give about 350mA and even if you apply higher input woltage, it doesn't get much hot like the 78xx ones.

<!--more-->

**THIS WAS MY FIRST SWITCHED CONVERTER AND LOT OF THINGS ARE TERRIBLY WRONG IN LAYOUT OF SWITCHING PATH. NOW AS A PROFFESIONAL PCB DESIGNER AFTER LOT OF YEARS I SMILE WHAT MISTAKES I DID. ANYWAY IT SOMEHOW WORKED AND I WILL KEEP THIS PAGE JUST FOR CURIOSITY.**


The pinout is the same like in 7805. I've made bigger pads for capacitors or inductor so you can solder almost anything size :) Theoreticaly the input voltage can be 40V according to the datasheet. You have to be careful and select proper input capacitor of suitable voltage. Also instead of SMD inductor you can use bigger thru-hole types.

You can get the MC34063 in SMD package easily on eBay, 20pcs with shipping costs about $6.

Download archive with eagle project and Inkscape PCB files are on the bottom of this page.

## Download

- [EAGLE CAD data](DCDCstepdown.zip)

![PCB](board.png)
![PCB](schematics.png)

![PCB](DSC_8314.JPG)
![PCB](DSC_8315.JPG)
![PCB](DSC_8316.JPG)
![PCB](DSC_8320.JPG)
