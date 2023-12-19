---
title: "Oculus2: HW Information"
slug: oculus2-hw-information
date_published: 2022-10-07T02:19:00.000Z
date_updated: 2023-01-23T07:53:15.000Z
tags:
categories: Oculus2
image: /assets/img/Hardware-oculus_832x700-1.png
---

## HW Spec

- Storage:​ UFS iNAND 64GB, 128GB, or 256GB
- Display Panel:​ Fast-switch LCD
- Display Resolution: ​1832×1920 per eye
- Display Refresh: ​72Hz at launch; 90Hz support to come
- SoC:​ Qualcomm® Snapdragon™ XR2 Platform (7 nm): Octa-core (1x2.84 GHz Kryo 585 & 3x2.42 GHz Kryo 585 & 4x1.8 GHz Kryo 585); Adreno 650.
- Part Number: SM8250
- Audio: ​Integrated speakers and microphone; also compatible with 3.5mm headphones.
- RAM: ​6GB
- Battery Life:​ You can expect between 2-3 hours based on the kind of content you’re using on Quest 2; closer to 2 hours if you’re playing games and closer to 3 hours if you’re watching media.
- Charge Time: ​With the provided USB-C power adapter, Quest 2 will charge to a full battery in about 2.5 hours.
- IPD: ​Adjustable IPD with three settings for 58, 63, and 68mm.
- Playspace:​ Stationary or Roomscale supported. Roomscale requires a minimum of 6.5 feet x 6.5 feet of obstruction-free floor space.

## Secure Boot
![]({{site.url}}{{site.baseurl}}/assets/img/content_qualcomm_firmware_2.png)

As you can see, the boot chain has evolved significantly. In 2015, the possible attack area was condensed, and the Secondary Bootloader (SBL) chain was merged into one unified SBL. As we move further down the line, we see SBL entirely replaced with Qualcomm’s new proprietary solution, the eXtensible Bootloader (XBL), which mitigated many of the security issues SBL presented.
Aboot has also matured from LittleKernel (an open-source bootloader) with some additions into a fully independent solution now called the proprietary Android Bootloader (ABL). This new bootloader allows the use of UEFI, among many other security and quality-of-life enhancements, for developers/OEMs.
The system_as_root configuration has also improved security, as well as the general architecture, significantly. It moves the Android-ramdisk from being stored in the boot image to being stored in the system partition, which, as the name would imply, is mounted as ’/’. This was done in part to allow it to be verified by dm-verity/Android Verified Boot.
NOTE: The new seamless update system coined “A/B” is separate from system_as_root, even though they are commonly seen hand in hand. OEM’s have the option of implementing one and not the other.

## Boot Modes

Fastboot: VOL_DOWN + PWR

Emergency Download Mode (EDL): VOL_DOWN + VOL_UP + PWR

## Mainboard

Bottom:
![]({{site.url}}{{site.baseurl}}/assets/img/oculus2-back-1.jpg)
Top:
![]({{site.url}}{{site.baseurl}}/assets/img/oculus2-top-1.jpg)
