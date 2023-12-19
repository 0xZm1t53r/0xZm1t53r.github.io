---
title: "Oculus2: Initial setup"
slug: oculus2-init-setup
date_published: 2022-09-09T02:14:00.000Z
date_updated: 2023-01-23T07:17:37.000Z
tags:
categories: Oculus2
image: /assets/img/oculus2-without-cover2.jpeg
---

I bought four devices/boards in different configurations and working conditions. Everything was bought on eBay. All this cost me about $200.

Devices/boards:

1. working headset with 128GB storage (no controllers)
2. working mainboard with 64GB storage
3. salvage headset with two controllers (working state unknown)
4. the headset with an unknown working state.

#### 1. Headset with 128GB storage

Everything works as was claimed.

#### **2. Mainboard with 64GB storage**

It works without any issues, as was claimed. The good thing is that it still has a Day-0 build. That is very interesting and great luck because Day-0 builds usually have issues/vulns, which are fixed in further releases. So it can be an excellent chance to get into device internals to collect more runtime information about the device.

#### 3. Salvage headset with two controllers (working state unknown)

The salvage headset is in a terrible state - the enclosure is broken (it was hit from the camera's side), the lenses and display are broken, and the mainboard was bent from the hit (and obviously does not work). The controllers are in working condition, but the enclosure of the left one is cracked. This is a good deal, even though there are a lot of broken parts because the controllers themselves are costly. The mainboard from this headset will be used for HW reverse engineering.
![]({{site.url}}{{site.baseurl}}/assets/img/mb-bend.png)
#### 4. The headset with an unknown working state

The last headset has a weak battery, but everything else works fine.

### Plans for the near future

- collect the info about Oculus Quest 2 HW.
- Dump the firmware for 128GB and 64GB versions of headsets. 64GB version is interesting because it is DAY-0.
- Find a way (if possible) to update FW on a device without rework.
- Find UART pins or a connection.
- Prepare a comfortable HW setup for further debugging/research.
