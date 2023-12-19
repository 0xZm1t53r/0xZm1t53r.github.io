---
title: "Oculus2: Chasing down the UART: Part1"
slug: oculus2-chasing-down-the-uart-part1
permalink: oculus2-chasing-down-the-uart-part1
date_published: 2022-10-21T02:48:00.000Z
date_updated: 2023-02-22T19:03:45.000Z
tags: reverse_engineering
categories: Oculus2
image: /assets/img/oculus-top.large.jpeg
---

One of the first things to do with a new device is to find a UART. As an embedded developer (in the past and kind of in the present), I am 99.99% sure that debug UART is present and can be easyish accessible otherwise, Oculus's developers' live was/is a nightmare. Debug UART can be routed to a separate connector or test points or even "hidden" in existing connectors (for example, in USB, audio, or camera connectors).

*Why is debugging UART important? *
It may give you runtime information about a device's execution or even give you extra control of the device(unlikely).

In the beginning, I tried the most straightforward approach:

- switch a device into **fastboot mode**
- reboot the device in the loop (via `fastboot reboot bootloder`)
- check all test points using a logic analyzer

The idea was that debug UART is routed to one of the test points (TP).

Sadly, this approach did not give me any results - I did not find anything interesting on test points using a logic analyzer. My guess is something tricky with debug output -  maybe it is disabled, or I did not wait for enough or checked at the wrong time.

So, let's do oppositely - because SM8250 is not a super new SoC, and Linux kernel already has its support, let's try to find the debug UART SoC's pins and trace them to test points or a connector.

Device tree [sm8250.dtsi](https://github.com/torvalds/linux/blob/master/arch/arm64/boot/dts/qcom/sm8250.dtsi) from Linux upstream defines the next UARTs:

- UART2: GPIO117, GPIO118
- UART6: GPIO16, GPIO17, GPIO18, GPIO19
- UART12: GPIO34, GPIO35
- UART17: GPIO52, GPIO53, GPIO54, GPIO55
- UART18: GPIO58, GPIO59'**compatible = "qcom,geni-debug-uart";**' is present only for two: UART2 and UART12.

More digging through forums/docs/DTSs and commit logs for this DTS inclines me to that UART12 is the correct one. Thus we are interested in GPIO34 (TX) and GPIO35(RX) of SM8250 SoC.
It is mostly impossible to find a datasheet for Qualcomm's SoC (especially for new ones).
Digging through the internet gave me a terrible-quality [picture](https://bbs.16rd.com/thread-572749-1-1.html) of SoC pinout (half).

![]({{site.url}}{{site.baseurl}}/assets/img/pinout-2.jpeg)

Big thanks to "Google Translate" which helped me translate Chinese to English on the fly.
It took me a few days to recover some information from this picture, and for my luck, GPIO34 and GPIO35 were there.

Recovered pic:
![]({{site.url}}{{site.baseurl}}/assets/img/SoC_pinout_recovered_1080p.png)

IMPORTANT: I can't guarantee that everything was recovered correctly.

Next steps:
- desolder  SoC + LPDDR4
- solder thing wire to GPIO 34 and 35
- search where these GPIOs were routed using a multimeter.

I found it!!! Moreover, I found not only UART pins but a whole debug connector. Further research showed that this connector also has VOLUME DOWN, VOLUME UP, and POWER buttons routes.

![]({{site.url}}{{site.baseurl}}/assets/img/oculus2-top-debug-connector-1.jpg)

Pinout of connector:

![]({{site.url}}{{site.baseurl}}/assets/img/UART-connector-1.png)

A logic analyzer proved that found correctly.
The problem with my "simplest approach" was that not much data were printed to UART, and my timelines were not correct (I did not wait for enough)

<details>
<summary>UART output</summary>

<div markdown="1">
```
Format: Log Type - Time(microsec) - Message - Optional Info
Log Type: B - Since Boot(Power On Reset),  D - Delta,  S - Statistic
S - QC_IMAGE_VERSION_STRING=BOOT.XF.3.2-00280-SM8250-3
S - IMAGE_VARIANT_STRING=Soc8250LAB
S - OEM_IMAGE_VERSION_STRING=android-build
S - Boot Interface: UFS
S - Secure Boot: On
S - Boot Config @ 0x00786070 = 0x00000001
S - JTAG ID @ 0x00786130 = 0x200ce0e1
S - OEM ID @ 0x00786138 = 0x01370001
S - Serial Number @ 0x00786134 = 0x2f8397f2
S - Feature Config Row 0 @ 0x007841f8 = 0x0040200000000400
S - Feature Config Row 1 @ 0x00784200 = 0xc000000000000000
S - Core 0 Frequency, 1516 MHz
S - PBL Patch Ver: 5
S - PBL freq: 600 MHZ
D -      6210 - pbl_apps_init_timestamp
D -     98602 - bootable_media_detect_timestamp
D -      1054 - bl_elf_metadata_loading_timestamp
D -      9388 - bl_hash_seg_auth_timestamp
D -      6382 - bl_elf_loadable_segment_loading_timestamp
D -      5330 - bl_elf_segs_hash_verify_timestamp
D -      6758 - bl_sec_hash_seg_auth_timestamp
D -       821 - bl_sec_segs_hash_verify_timestamp
D -        33 - pbl_populate_shared_data_and_exit_timestamp
S -    134578 - PBL, End
B -    148321 - SBL1, Start
B -    253363 - SBL1 BUILD @ 18:50:46 on Aug 25 2020
B -    257511 - usb: usb_shared_hs_phy_init: hs phy cfg size , 0xc
D -     10614 - sbl1_hw_init
D -         0 - boot_flash_init
B -    387441 - UFS INQUIRY ID: WDC     SDINDDH4-64G    1308
B -    389973 - UFS Boot LUN: 1
D -      5429 - Auth Metadata
D -    148870 - sbl1_xblconfig_init
D -         0 - boot_config_data_table_default_init
B -    423492 - hwid[BrdID=0,0,1,0;SoCID=0,1,1] subtype=17
B -    428982 - Using default CDT
D -     10095 - boot_config_data_table_init
B -    436699 - CDT Version:3,Platform ID:8,Major ID:1,Minor ID:0,Subtype:17
D -     17324 - sbl1_hw_platform_pre_ddr
D -         0 - devcfg init
B -    465338 - PM: PM 0=0x80008080000000c1:0x0
 
B -    465430 - PM: PM 1=0x8000018000000040:0x0
B -    469883 - PM: PM 2=0x8000018000000020:0x0
 
B -    474366 - PM: HARD RESET by KPDPWR
B -    480771 - PM: PMA_2 PON:KPD ON:PON OFF:XVDD
B -    552782 - PM: SET_VAL:Skip
B -    553148 - PM: PSI: b0x00_v0x4d
B -    558546 - PM: Device Init # SPMI Transn: 4507
B -    567025 - PM: Driver Init # SPMI Transn: 468
B -    579195 - PM: battery Id: 24929
B -    579561 - PM: APSD result: 255
B -    582702 - PM: APSD in progress
B -    586210 - PM: Vbatt: 4369; Ibatt: 23
B -    589656 - PM: CHG Init # SPMI Transn: 63
D -    135603 - pmic XBL init
D -     27542 - vsense_railway_cpr init
D -    171197 - sbl1_hw_pre_ddr_init
D -         0 - boot_dload_handle_forced_dload_timeout
D -      1556 - sbl1_load_ddr_training_data
D -      5124 - sbl1_ddr_set_params
B -    658922 - DSF version = 156.8.10
B -    658952 - Manufacturer ID = ff, Device Type = 7
B -    662521 - Rank 0 size = 6144 MB, Rank 1 size = 0 MB
D -     26260 - sbl1_ddr_init
D -         0 - boot_pre_ddi_entry
B -    675483 - do_ddr_training, Start
B -    705160 - DDR: Start of DDR Training Restore
B -    714615 - Frequency = 1555 MHz
B -    714615 - DDR: End of DDR Training Restore
D -     39345 - do_ddr_training, Delta
D -     47001 - sbl1_do_ddr_training
D -         0 - sbl1_hand_control_to_devprog_ddr
D -       641 - boot_ddi_entry
B -    733952 - Pimem init cmd, entry
D -     10767 - Pimem init cmd, exit
D -     13878 - sbl1_post_ddr_init
D -         0 - sbl1_hw_init_secondary
B -    755820 - APDP Image Load, Start
D -      1952 - APDP Image Loaded, Delta - (0 Bytes)
D -         0 - boot_dload_dump_security_regions
B -    767532 - usb: UFS Serial - 9279e24f
B -    891362 - usb: chgr -  DCP_CHARGER
D -    125233 - boot_dload_check
D -         0 - boot_cache_set_memory_barrier
D -         0 - boot_smem_debug_init
D -       366 - boot_smem_init
D -         0 - boot_smem_alloc_for_minidump
B -    908747 - PM: SMEM Chgr Info Write Success
D -      4117 - boot_smem_store_pon_status
D -        31 - sbl1_hw_platform_smem
D -        61 - boot_ddr_share_data_to_aop
D -       518 - boot_clock_init_rpm
D -        31 - boot_vsense_copy_to_smem
D -         0 - boot_share_flash_data
D -        31 - boot_populate_ram_partition_table
D -        30 - boot_populate_ddr_details_shared_table
D -         0 - sbl1_tlmm_init
D -         0 - sbl1_efs_handle_cookies
D -         0 - boot_apt_test
B -    955229 - OEM_MISC Image Load, Start
D -      2776 - OEM_MISC Image Loaded, Delta - (0 Bytes)
B -    961970 - QTI_MISC Image Load, Start
D -      5185 - QTI_MISC Image Loaded, Delta - (0 Bytes)
B -    978135 - PM: PM Total Mem Allocated: 2122
D -      7106 - sbl1_pm_aop_pre_init_wrapper
B -    982710 - AOP Image Load, Start
D -      5032 - Auth Metadata
D -      1982 - Segments hash check
D -     16744 - AOP Image Loaded, Delta - (198240 Bytes)
B -   1002779 - QSEE Dev Config Image Load, Start
D -      5093 - Auth Metadata
D -       549 - Segments hash check
D -     14030 - QSEE Dev Config Image Loaded, Delta - (42716 Bytes)
B -   1025837 - QSEE Image Load, Start
D -      8296 - Auth Metadata
D -     21564 - Segments hash check
D -     73322 - QSEE Image Loaded, Delta - (3244468 Bytes)
D -       183 - sbl1_hw_play_vibr
B -   1108126 - SEC Image Load, Start
D -      2836 - SEC Image Loaded, Delta - (0 Bytes)
B -   1114470 - CPUCPFW Image Load, Start
D -      4727 - CPUCPFW Image Loaded, Delta - (0 Bytes)
B -   1123040 - QHEE Image Load, Start
D -      5063 - Auth Metadata
D -      2958 - Segments hash check
D -     15708 - QHEE Image Loaded, Delta - (440120 Bytes)
B -   1142042 - STI Image Load, Start
D -      5276 - STI Image Loaded, Delta - (0 Bytes)
B -   1151344 - APPSBL Image Load, Start
D -      5033 - Auth Metadata
D -     13573 - Segments hash check
D -     26932 - APPSBL Image Loaded, Delta - (2621440 Bytes)
D -         0 - sbl1_appsbl_arch_determination
B -   1187487 - SBL1, End
D -   1043100 - SBL1, Delta
S - Flash Throughput, 170000 KB/s  (6628806 Bytes,  38965 us)
S - DDR Frequency, 1555 MHz
```
</div>

</details>

### TO BE CONTINUED

[Oculus2: Chasing down the UART: Part2]({{site.url}}{{site.baseurl}}{% link _posts/2022-11-04-oculus2-chasing-down-the-uart-part2.md %})
