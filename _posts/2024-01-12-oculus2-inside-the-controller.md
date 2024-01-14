---
title: "Oculus2: Inside the Controller..."
slug: oculus2-inside-the-controller
tags: reverse_engineering
categories: Oculus2
image: /assets/img/2024-01-12/controller.jpg
---

## Controller overview

This time I decided to dig into oculus's controller to see what interesting I can find there.
 I'm taking a closer look at the Oculus Quest 2 controller. I've always been curious about what makes this piece of tech tick, and now, I'm finally digging in to see what's inside.


### Without top cover

![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-wo-top-cover.jpeg)


### Test Pins
The first intriguing discovery can be found inside the battery compartment, concealed beneath the label:
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/battery-holder.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/battery-holder-pins.jpg)

That is very interesing.
I'm now starting to take apart the controller to examine these pins. Fortunately, identifying the pins wasn't difficult as they are clearly labeled:
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/battery-board-pins.jpg)
(yeah, on this photo I already soldered wires to connect an external power source instead of a battery)

A quick examination revealed that the SWD lines are not connected to SoC pins. This leads to an another finding: the SWD lines (SWDCLK and SWDIO) are physically disconnected but can be reconnected by soldering 0R resistors R2 and R1.
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/battery-board-bottom.jpg)

| Name | Pin |
| - | -- |
R2 | SWDCLK
R1 | SWDIO

### Main board

#### Bottom side
I pull out all internal to do better examination.
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-01.jpg)

I traced down the TPs:

| Name | Pin |
| - | -- |
TP1 | nrf52 P0.12 - Rx
TP2 | nrf52 P0.11 - Tx
TP3 | nrf52 SWDIO
TP4 | nrf52 SWCLK
TP6 | nrf52 P0.04
TP11 | VBAT
TP12 | 2.7V
TP13 | 3.3V
TP16 | GND

J1 connector is for connection the battery pcb.

![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-03.jpg)

| Name | Pin |
| - | -- |
TP15 | J4 3rd pin
TP14 | ???
TP17 | GND
TP10 | nrf52 P0.27
TP20 | J5 1st pin

J3 and J4 connectors are for LED ring. J5 is for 3D Analog Joystick

![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-02.jpg)

J1 is the antenna connector.

#### Top side 

![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-bottom-01.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-bottom-02.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-bottom-03.jpg)

#### Individual ICs

![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u7.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u8.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u10.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u11.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u12.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-top-ic-u13.jpg)
![]({{site.url}}{{site.baseurl}}/assets/img/2024-01-12/controller-board-bottom-ic-u6.jpg)



## Summary

The controller is built on the nRF52832 SoC, which, while lacking a verify/secure boot feature, includes read-out protection through the APPROTECT feature. Unfortunately, APPROTECT is activated in the firmware. Fortunately, there's a known [vulnerability](https://limitedresults.com/2020/06/nrf52-debug-resurrection-approtect-bypass/) that allows for the use of fault injection (voltage glitching) to circumvent the APPROTECT and extract the firmware. Nordic addressed this vulnerability in revision 3 (build code Gx0), but luckily, this controller utilizes an earlier version that still contains the vulnerability.


## TODO
Dump the firmware using voltage glitching.
