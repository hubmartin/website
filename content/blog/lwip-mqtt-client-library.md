---
title: "LwIP MQTT client library"
date: 2014-05-26T12:27:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["lib", "mqtt", "lwIP"]
---

Some time ago I needed simple plain C MQTT library for my **TIVA LM3S6965** board (former Stellaris, former LuminaryMicro). I started rewriting library from Fan Yilun who written nice C++ library for MBed.

<!--more-->

So I've created simple library on top of lwIP TCP stack. I've added some cleaning when you disconnect, also periodic keep-alive messages are handled "in background". You can also register your callback routine.

### Github repo
https://github.com/hubmartin/mqtt_lwip_library

### mqtt.c API

```
void mqttInit(Mqtt *mqtt, struct ip_addr serverIp, int port, msgReceived fn, char *devId);
uint8_t mqttConnect(Mqtt *this);
uint8_t mqttPublish(Mqtt *this,char* pub_topic, char* msg);
uint8_t mqttDisconnect(Mqtt *this);
uint8_t mqttSubscribe(Mqtt *this, char* topic);
uint8_t mqttLive(Mqtt *this);
void mqttDisconnectForced(Mqtt *this);
```

You have to implement variable `msTimer` which counts number of milliseconds from start of the program. Also call `mqttAppHandle()` in the main loop. This function handles keep-alive packets.
Connection tested with mosquitto MQTT broker.