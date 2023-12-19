---
title: "Oculus2: Inside the Battery..."
slug: oculus2-inside-the-battery
permalink: oculus2-inside-the-battery
date_published: 2023-02-24T08:29:50.000Z
date_updated: 2023-12-18T19:47:52.000Z
tags: reverse_engineering
categories: Oculus2
image: /assets/img/battery.jpg
---

The battery connector is the most annoying thing in my test setup - constantly having to plug and unplug a small connector for each modification is not only frustrating but also time-consuming, as there's always a fear of damaging it. So, I had an idea to come up with some solution for a while and finally decided to see what was inside the battery.

Battery connector:
![]({{site.url}}{{site.baseurl}}/assets/img/battery_cable-1.jpg)

Unfortunately, I already disconnected the extra flex cable in this photo, which goes into the battery pack. The cable is connected like this:

![]({{site.url}}{{site.baseurl}}/assets/img/battery_cable_2-1.jpg)

This tiny flex cable is ended up by the flex PCB with test/contact pads:

![]({{site.url}}{{site.baseurl}}/assets/img/battery_extra_pins.jpg)

The purpose of pads is unknown to me - pads are not connected to pins on the connector.
Let's take the battery out of the case and look closer. The controller is on top of the battery and protected by a small plastic cover.

The controller looks like this:
![]({{site.url}}{{site.baseurl}}/assets/img/battery_controller.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/battery_controller_2.jpg)

I did the following modification - cut off the controller from the battery bank and soldered wires with connectors to separate parts.

Controller:
![]({{site.url}}{{site.baseurl}}/assets/img/battery_controller_only_mod.jpg)

Battery:
![]({{site.url}}{{site.baseurl}}/assets/img/battery_wo_connector_mod.jpg)

The controller is covered by compounds that are very hard to remove (even with hot air), but I removed some compounds from the top of the elements and read the names of some of the ICs in the microscope.
![]({{site.url}}{{site.baseurl}}/assets/img/battery-controller-bottom.png)

Controller w/o top compounds in the microscope![]({{site.url}}{{site.baseurl}}/assets/img/microscope-ic-photos.jpg)ICs in the microscope![]({{site.url}}{{site.baseurl}}/assets/img/battery-controller-labeled-2.jpg)

| # | Description |
| - | -- |
| 1 |Panasonic 7F0207
| 2 | Unknown
| 3 | Unknown
| 4 | Panasonic 7F0207
| 5 | 9N1H
| 6 | Unknown
| 7 | P1GV

Unfortunately, IC #6 has just a gleaming surface on top without any name on it - I tried different angles with different lights but could not see any symbols in the microscope. I was not able to find datasheets for ICs :(

Battery test pins on the main PCB:
![]({{site.url}}{{site.baseurl}}/assets/img/Battery-pins-1.jpg)

| # | Description |
| - | -- |
|1|ID|
|2|VP+|
|3|GND|
|4|P+|
|5|TH|
|6|RB|
|7|C|
|8|D|

I soldered wires to test points on the main board and put them together into a small 8-pin connector for easy access to the battery lines.
![]({{site.url}}{{site.baseurl}}/assets/img/battery_pins_connector_labeled.jpg)

| # | Description|
| - | -- |
|1|P+
|2|P-
|3|RB
|4|VP+
|5|C
|6|D
|7|ID
|8|TH

I have zero experience with batteries in modern embedded devices, so the meaning of all these lines (except P+ and P-) is unclear to me. So I messed around with all these lines in the logic analyzer.

Some screenshots from Logic Analyzer, where I did some experiments.

**Capture 1:** USB cable is connected, battery controller is also connected, but the battery element is not:
![]({{site.url}}{{site.baseurl}}/assets/img/batt_controller-without-battery.png)
**D**xx - digital lines; **A**xx - analog lines.

**Capture 2:** USB cable is connected, and the battery element is connected, but the battery controller is not:
![]({{site.url}}{{site.baseurl}}/assets/img/With-battery-but-without-controller.png)

**Capture 3:** USB cable is connected, and the battery element and controller are also connected (normal situation):
![]({{site.url}}{{site.baseurl}}/assets/img/With-battery---controller.png)

**Capture 4:** The battery is connected typically, and the Power button is pressed in the middle:
![]({{site.url}}{{site.baseurl}}/assets/img/PWR_Button-pressed-in-the-middle.png)

So, the system works **only** if the controller and the battery are both connected.

Also, based on observation, the **RB**(SMBCLK)/**TH**(SMBDAT) and **C**(SMBCLK)/**D**(SMBDAT) wires are SMBus/I2C lines.

The purpose of the pair **RB**/**TH** is unclear to me - "Byte 0x00 ACK" is the only thing transmitted via these lines. Is it something like "PING"? I do not know, and I would love to know more from someone familiar with it.

The pair **C**/**D** is Smart Battery protocol with some extra commands. More details can be found in ["Smart Battery Data Specification"]({{site.url}}{{site.baseurl}}/assets/docs/sbdat110.pdf) from http://sbs-forum.org/specs/ .


<details>
<summary>Smart Battery logs from Logic Analyzer</summary>
<div markdown="1">
```
Write address 0x55 ACK Protocol=Read word
Command Current 0x0A
Read address 0x55 ACK Protocol=Read word
Word 0x8000 ACK
Write address 0x55 ACK Protocol=Read word
Command AtRateTimeToEmpty 0x06
Read address 0x55 ACK Protocol=Read word
Word 0x990B ACK
Write address 0x55 ACK Protocol=Read word
Command BatteryStatus 0x16
Read address 0x55 ACK Protocol=Read word
BatteryStatus 0xFFFF OVER CHARGED ALARM=1, TERMINATE CHARGE ALARM=1, OVER TEMP ALARM=1, TERMINATE DISCHARGE ALARM=1, REMAINING CAPACITY ALARM=1, REMAINING TIME ALARM=1, INITIALIZED=1, DISCHARGING=1, FULLY CHARGED=1, FULLY DISCHARGED=1, ErrorCode=UnknownError
Write address 0x55 ACK Protocol=Read word
Command DesignCapacity 0x18
Read address 0x55 ACK Protocol=Read word
Word 0x0905 ACK
Write address 0x55 ACK Protocol=Read word
Command AverageTimeToEmpty 0x12
Read address 0x55 ACK Protocol=Read word
Word 0x260E ACK
Write address 0x55 ACK Protocol=Read word
Command  0x2C
Read address 0x55 ACK Protocol=Read word
Word 0x0B00 ACK
Write address 0x55 ACK Protocol=Read word
Command Current 0x0A
Read address 0x55 ACK Protocol=Read word
Word 0x8000 ACK
Write address 0x55 ACK Protocol=Read word
Command  0x2A
Read address 0x55 ACK Protocol=Read word
Word 0x5400 ACK
Write address 0x55 ACK Protocol=Read word
Command DeviceChemistry 0x22
Read address 0x55 ACK Protocol=Read word
Word 0x3800 ACK
Write address 0x55 ACK Protocol=Read word
Command OptionalMfgFunction4 0x3C
Read address 0x55 ACK Protocol=Read word
Word 0x2E0E ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x60 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x40 ACK
Byte 0x11 ACK
Byte 0x1A ACK
Byte 0x09 ACK
Byte 0xDF ACK
Byte 0x07 ACK
Byte 0x33 ACK
Byte 0xF5 ACK
Byte 0x72 ACK
Byte 0xF6 ACK
Byte 0x5C ACK
Byte 0xFC ACK
Byte 0x39 ACK
Byte 0xFA ACK
Byte 0x37 ACK
Byte 0xF9 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0xFA NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x62 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0xFE ACK
Byte 0xAD ACK
Byte 0xC2 ACK
Byte 0x03 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x2D NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x63 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x3C ACK
Byte 0x00 ACK
Byte 0x53 ACK
Byte 0x00 ACK
Byte 0x36 ACK
Byte 0x00 ACK
Byte 0x54 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x11 ACK
Byte 0x08 ACK
Byte 0x00 ACK
Byte 0xFE ACK
Byte 0xAD ACK
Byte 0xC2 ACK
Byte 0x03 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0xFA NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x65 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x2F ACK
Byte 0x5B ACK
Byte 0x02 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x0E NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x66 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x68 ACK
Byte 0xA6 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x28 ACK
Byte 0x7C ACK
Byte 0x08 ACK
Byte 0x00 ACK
Byte 0x7B ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x1E ACK
Byte 0x19 ACK
Byte 0x19 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
4 NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x67 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x06 ACK
Byte 0xD7 ACK
Byte 0xFE ACK
Byte 0x00 ACK
Byte 0xDF ACK
Byte 0x99 ACK
Byte 0x24 ACK
Byte 0x00 ACK
Byte 0x0D ACK
Byte 0x95 ACK
Byte 0x86 ACK
Byte 0x00 ACK
Byte 0x5C ACK
Byte 0xC8 ACK
Byte 0x24 ACK
Byte 0x01 ACK
Byte 0x08 ACK
Byte 0x45 ACK
Byte 0x35 ACK
Byte 0x00 ACK
Byte 0x22 ACK
Byte 0x7E ACK
Byte 0x1D ACK
Byte 0x00 ACK
Byte 0xB6 ACK
Byte 0xAE ACK
Byte 0x19 ACK
Byte 0x00 ACK
Byte 0x72 ACK
Byte 0xCF ACK
Byte 0x52 ACK
Byte 0x00 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x61 NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x68 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
0 ACK
Read address 0x55 ACK
Byte 0x19 ACK
Byte 0x0E ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0xF3 ACK
Byte 0x59 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x99 ACK
Byte 0xB7 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x1E ACK
Byte 0x00 ACK
Byte 0x01 ACK
Byte 0x00 ACK
Byte 0x7E ACK
Byte 0xFB ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x68 ACK
Byte 0x2F ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x86 ACK
Byte 0x26 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x49 ACK
Byte 0x1C ACK
Byte 0x00 ACK
Byte 0x00 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x94 NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x69 ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x92 ACK
Byte 0x02 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x27 ACK
Byte 0x12 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x74 ACK
Byte 0xAD ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x2B ACK
Byte 0x7C ACK
Byte 0x01 ACK
Byte 0x00 ACK
Byte 0xF5 ACK
Byte 0x0F ACK
Byte 0x02 ACK
Byte 0x00 ACK
Byte 0x6B ACK
Byte 0x86 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x5C ACK
Byte 0x32 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0xCA ACK
Byte 0x1D ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x94 NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x6A ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0xCF ACK
Byte 0x04 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x97 ACK
Byte 0x0B ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0xDA ACK
Byte 0x26 ACK
Byte 0x01 ACK
Byte 0x00 ACK
Byte 0x07 ACK
Byte 0xC0 ACK
Byte 0x02 ACK
Byte 0x00 ACK
Byte 0x9A ACK
Byte 0x50 ACK
Byte 0x01 ACK
Byte 0x00 ACK
Byte 0xC8 ACK
Byte 0x6F ACK
Byte 0x01 ACK
Byte 0x00 ACK
Byte 0xFF ACK
Byte 0x9C ACK
Byte 0x00 ACK
0 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x98 NACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction2 0x3E
Byte 0x6B ACK
Write address 0x55 ACK Protocol=Write byte
Command OptionalMfgFunction1 0x3F
Byte 0x00 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 ACK
Byte 0x00 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x60
Read address 0x55 ACK Protocol=Read byte
Byte 0x94 NACK
Write address 0x55 ACK Protocol=Read word
Command Temperature 0x08
Read address 0x55 ACK Protocol=Read word
Word 0x7F0E ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xD9FF ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xD9FF ACK
Write address 0x55 ACK Protocol=Read word
Command  0x2E
Read address 0x55 ACK Protocol=Read word
Word 0x6200 ACK
Write address 0x55 ACK Protocol=Read word
Command  0x28
Read address 0x55 ACK Protocol=Read word
Word 0x8B0B ACK
Write address 0x55 ACK Protocol=Read word
Command FullChargeCapacity 0x10
Read address 0x55 ACK Protocol=Read word
Word 0x7B01 ACK
Write address 0x55 ACK Protocol=Write word
Command OptionalMfgFunction2 0x3E
Word 0x7000 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x31 ACK
Byte 0x43 ACK
Byte 0x5A ACK
Byte 0x30 ACK
Byte 0x34 ACK
Byte 0x39 ACK
Byte 0x37 ACK
Byte 0x59 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x36 ACK
Byte 0x37 ACK
Byte 0x33 ACK
Byte 0x44 ACK
Byte 0x53 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x35 ACK
Byte 0x31 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x47 ACK
Byte 0x4D ACK
Byte 0x51 ACK
Byte 0x41 ACK
5 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x61
Read address 0x55 ACK Protocol=Read byte
4 NACK
Write address 0x55 ACK Protocol=Read word
Command Temperature 0x08
Read address 0x55 ACK Protocol=Read word
Word 0x7F0E ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xD9FF ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xD9FF ACK
Write address 0x55 ACK Protocol=Read word
Command  0x2E
Read address 0x55 ACK Protocol=Read word
Word 0x6200 ACK
Write address 0x55 ACK Protocol=Read word
Command  0x28
Read address 0x55 ACK Protocol=Read word
Word 0x8B0B ACK
Write address 0x55 ACK Protocol=Read word
Command FullChargeCapacity 0x10
Read address 0x55 ACK Protocol=Read word
Word 0x7B01 ACK
Write address 0x55 ACK Protocol=Write word
Command OptionalMfgFunction2 0x3E
Word 0x7000 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x31 ACK
Byte 0x43 ACK
Byte 0x5A ACK
Byte 0x30 ACK
Byte 0x34 ACK
Byte 0x39 ACK
Byte 0x37 ACK
Byte 0x59 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x36 ACK
Byte 0x37 ACK
Byte 0x33 ACK
Byte 0x44 ACK
Byte 0x53 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x35 ACK
Byte 0x31 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x47 ACK
Byte 0x4D ACK
Byte 0x51 ACK
Byte 0x41 ACK
5 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x61
Read address 0x55 ACK Protocol=Read byte
4 NACK
Write address 0x55 ACK Protocol=Read word
Command Temperature 0x08
Read address 0x55 ACK Protocol=Read word
Word 0x830E ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xACFE ACK
Write address 0x55 ACK Protocol=Read word
Command MaxError 0x0C
Read address 0x55 ACK Protocol=Read word
Word 0xACFE ACK
Write address 0x55 ACK Protocol=Read word
Command  0x2E
Read address 0x55 ACK Protocol=Read word
Word 0x6200 ACK
Write address 0x55 ACK Protocol=Read word
Command  0x28
Read address 0x55 ACK Protocol=Read word
Word 0x8B0B ACK
Write address 0x55 ACK Protocol=Read word
Command FullChargeCapacity 0x10
Read address 0x55 ACK Protocol=Read word
Word 0x7B01 ACK
Write address 0x55 ACK Protocol=Write word
Command OptionalMfgFunction2 0x3E
Word 0x7000 ACK
Write address 0x55 ACK
Byte 0x40 ACK
Read address 0x55 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x31 ACK
Byte 0x43 ACK
Byte 0x5A ACK
Byte 0x30 ACK
Byte 0x34 ACK
Byte 0x39 ACK
Byte 0x37 ACK
Byte 0x59 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x36 ACK
Byte 0x37 ACK
Byte 0x33 ACK
Byte 0x44 ACK
Byte 0x53 ACK
Byte 0x30 ACK
Byte 0x30 ACK
Byte 0x33 ACK
Byte 0x30 ACK
Byte 0x35 ACK
Byte 0x31 ACK
Byte 0x31 ACK
Byte 0x30 ACK
Byte 0x47 ACK
Byte 0x4D ACK
Byte 0x51 ACK
Byte 0x41 ACK
Byte 0x35 NACK
Write address 0x55 ACK Protocol=Read byte
Command  0x61
Read address 0x55 ACK Protocol=Read byte
Byte 0x24 NACK
```
</div>

</details>

### Summary of the Battery pins

| Name | Description |
| -- | -- |
| P+ | Battery Positive
| P- | Battery Negative
| RB | SMBCLK
| VP+ | |
| C | SMBCLK (Smart Battery)
| D | SMBDAT (Smart Battery)ID???THSMBDAT

### TODO

The idea for a future project:

1. write/create a fake(emulator) battery for Oculus. The most interesting part is the "Smart Battery" communication because it looks like readings are made after bootloaders, and it would be interesting to do some fuzzing(why not :) ), or in general, it can be interesting.
2. Dive into the battery firmware. Min goal - get more info about it.
