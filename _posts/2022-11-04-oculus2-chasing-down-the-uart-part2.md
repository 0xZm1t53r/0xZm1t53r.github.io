---
title: "Oculus2: Chasing down the UART: Part2"
slug: oculus2-chasing-down-the-uart-part2
date_published: 2022-11-04T07:47:00.000Z
date_updated: 2023-02-22T22:41:18.000Z
tags: reverse_engineering
categories: Oculus2
image: /assets/img/oculus2-top-debug-connector-2.jpg
---

[Oculus2: Chasing down the UART: Part1]({% link _posts/2022-10-20-oculus2-chasing-down-the-uart-part1.md %})

I spent some time and found what connector should be soldered for DEBUG: Hirose Electric - FH58 Series - FH58M-7S-0.25SHW

Also, I decided to design a simple adapter board to make my life a bit easier.

Schematic:

![]({{site.url}}{{site.baseurl}}/assets/img/Schematic_debug-board-simple_2022-11-04-1.png)

PCB:

![]({{site.url}}{{site.baseurl}}/assets/img/PCB_PCB_debug-board-simple_2022-11-22-3.png)

The main problem is the cable. I was not able to find where to buy the cable for FH58M-7S-0.25SHW.

One of the options is to design a flex PCB with cable, but it may be expensive.

I noticed that the same connector is used for the power button board:

![]({{site.url}}{{site.baseurl}}/assets/img/s-l500-1.jpg)

This board costs about ~$14, so it will be much cheaper just to cut off the cable part and solder it directly to the adapter without a connector.
Result:

![]({{site.url}}{{site.baseurl}}/assets/img/debug-adapter-1.png)

<details>
<summary>Full UART output</summary>

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
S - Serial Number @ 0x00786134 = 0x
S - Feature Config Row 0 @ 0x007841f8 = 0x0040200000000400
S - Feature Config Row 1 @ 0x00784200 = 0xc000000000000000
S - Core 0 Frequency, 1516 MHz
S - PBL Patch Ver: 5
S - PBL freq: 600 MHZ
D -      6209 - pbl_apps_init_timestamp
D -     87661 - bootable_media_detect_timestamp
D -      1058 - bl_elf_metadata_loading_timestamp
D -      9387 - bl_hash_seg_auth_timestamp
D -      6446 - bl_elf_loadable_segment_loading_timestamp
D -      5338 - bl_elf_segs_hash_verify_timestamp
D -      6768 - bl_sec_hash_seg_auth_timestamp
D -       821 - bl_sec_segs_hash_verify_timestamp
D -        35 - pbl_populate_shared_data_and_exit_timestamp
S -    123723 - PBL, End
B -    141611 - SBL1, Start
B -    250008 - SBL1 BUILD @ 18:50:46 on Aug 25 2020
B -    254248 - usb: usb_shared_hs_phy_init: hs phy cfg size , 0xc
D -     10919 - sbl1_hw_init
D -        31 - boot_flash_init
B -    388661 - UFS INQUIRY ID: WDC     SDINDDH4-64G    1308
B -    391284 - UFS Boot LUN: 1
D -      5551 - Auth Metadata
D -    254797 - sbl1_xblconfig_init
D -         0 - boot_config_data_table_default_inat
B -    526765 - hwid[BrdID=0,0,1,0;SoCID=0,1,1] subtype=17
B -    533201 - Using default CDT
D -     10400 - boot_config_data_pable_init
B -    540368 - CDT Version:3,Platform ID:8,Major ID:1,Minor ID:0,Subtype:17
D -     17843 - sbl1_hw_platform_pre_ddr
D -         0 - devcfg init
B -    569892 - PM: PM 0=0x400008000000040:0x0 
B -    569953 - PM: PM 1=0x400008000000040:0x0 
B -    574467 - PM: PM 2=0x400008000000020:0x0 
B -    578981 - PM: POWER ON by CBLPWR,POWER OFF by RAW_XVDD_SHD
B -    663741 - PM: SET_VAL:Skip
B -    664107 - PM: PSI: b0x00_v0x4d
B -    669505 - PM: Device Init # SPMI Transn: 4491
B -    677954 - PM: Driver Init # SPMI Transn: 468
B -    690123 - PM: battery Id: 24658
B -    690520 - PM: APSD result: 255
B -    693661 - PM: APSD in progress
B -    697169 - PM: Vbatt: 3765; Ibatt: 0
B -    700615 - PM: CHG Init # SPMI Transn: 63
D -    142160 - pmic XBL init
D -     28548 - vsense_railway_cpr init
D -    178913 - sbl1_hw_pre_ddr_init
D -         0 - boot_dload_handle_forced_dload_timeout
D -     18483 - sbl1_load_ddr_training_data
D -     18635 - sbl1_ddr_set_params
B -    792359 - DSF version = 156.8.10
B -    792390 - Manufacturer ID = ff, Device Type = 7
B -    795958 - Rank 0 size = 6144 MB, Rank 1 size = 0 MB
D -     34282 - sbl1_ddr_init
D -        30 - boot_pre_ddi_entry
B -    808921 - do_ddr_training, Start
B -    838597 - DDR: Start of DDR Training Restore
B -    848052 - Frequency = 1555 MHz
B -    848052 - DDR: End of DDR Training Restore
D -     39345 - do_ddr_training, Delta
D -     47000 - sbl1_do_ddr_training
D -        30 - sbl1_hand_control_to_devprog_ddr
D -       640 - boot_ddi_entry
B -    867389 - Pimem init cmd, entry
D -     10766 - Pimem init cmd, exit
D -     13877 - sbl1_post_ddr_init
D -         0 - sbl1_hw_init_secondary
B -    889441 - APDP Image Load, Start
D -      1769 - APDP Image Loaded, Delta - (0 Bytes)
D -         0 - boot_dload_dump_security_regions
B -    900726 - usb: UFS Serial - 
B -    904111 - usb: chgr -  SDP_CHARGER
D -      8418 - boot_dload_check
D -         0 - boot_cache_set_memory_barrier
D -        31 - boot_smem_debug_init
D -       366 - boot_smem_init
D -         0 - boot_smem_alloc_for_minidump
B -    925339 - PM: SMEM Chgr Info Write Success
D -      4118 - boot_smem_store_pon_status
D -        30 - sbl1_hw_platform_smem
D -        61 - boot_ddr_share_data_to_aop
D -       519 - boot_clock_init_rpm
D -         0 - boot_vsense_copy_to_smem
D -         0 - boot_share_flash_data
D -        30 - boot_populate_ram_partition_table
D -        31 - boot_populate_ddr_details_shared_table
D -         0 - sbl1_tlmm_init
D -         0 - sbl1_efs_handle_cookies
D -         0 - boot_apt_test
B -    971852 - OEM_MISC Image Load, Start
D -      2775 - OEM_MISC Image Loaded, Delta - (0 Bytes)
B -    978562 - QTI_MISC Image Load, Start
D -      5185 - QTI_MISC Image Loaded, Delta - (0 Bytes)
B -    994757 - PM: PM Total Mem Allocated: 2122 
D -      7107 - sbl1_pm_aop_pre_init_wrapper
B -    999332 - AOP Image Load, Start
D -      5154 - Auth Metadata
D -      1891 - Segments hash check
D -    101016 - AOP Image Loaded, Delta - (198240 Bytes)
B -   1103673 - QSEE Dev Config Image Load, Start
D -      5063 - Auth Metadata
D -       549 - Segments hash check
D -     32391 - QSEE Dev Config Image Loaded, Delta - (42716 Bytes)
B -   1145031 - QSEE Image Load, Start
D -      8388 - Auth Metadata
D -     21685 - Segments hash check
D -    334554 - QSEE Image Loaded, Delta - (3244468 Bytes)
D -       183 - sbl1_hw_play_vibr
B -   1491968 - SEC Image Load, Start
D -        30 - SEC Image Loaded, Delta - (0 Bytes)
B -   1495476 - CPUCPFW Image Load, Start
D -      4727 - CPUCPFW Image Loaded, Delta - (0 Bytes)
B -   1504046 - QHEE Image Load, Start
D -      5063 - Auth Metadata
D -      2989 - Segments hash check
D -     40962 - QHEE Image Loaded, Delta - (440120 Bytes)
B -   1548332 - STI Image Load, Start
D -      5276 - STI Image Loaded, Delta - (0 Bytes)
B -   1557604 - APPSBL Image Load, Start
D -      5155 - Auth Metadata
D -     13603 - Segments hash check
D -     29982 - APPSBL Image Loaded, Delta - (2621440 Bytes)
D -         0 - sbl1_appsbl_arch_determination
B -   1596766 - SBL1, End
D -   1459090 - SBL1, Delta
S - Flash Throughput, 14000 KB/s  (6628806 Bytes,  461757 us)
S - DDR Frequency, 1555 MHz
UEFI Start     [ 1772]
 - 0x09FC01000 [ 1775] Sec.efi
ASLR        : ON
DEP         : ON (RTB)
Timer Delta : +20 mS
RAM Entry 0 : Base 0x080000000  Size 0x03CC00000
RAM Entry 1 : Base 0x0C0000000  Size 0x140000000
Total RAM   : 0x17CC00000
UEFI Ver    : 5.0.200825.BOOT.XF.3.2-00280-SM8250-3
Build Info  : 64b Aug 25 2020 18:53:18
Boot Device : UFS
PROD Mode   : TRUE
Retail      : TRUE
PM0: 30, PM1: 32, PM2: 31, PM3: 33, 
UFS INQUIRY ID: WDC     SDINDDH4-64G    1308
UFS Boot LUN: 1
QseeLoadServiceImageSyscall Failed
Failed to start featenabler_a TA, status = 1
HW Wdog Setting from PCD : Disabled
DisplayDxe: Resolution 1920x3664 (2 intf)
SPSSDxe [read_iar_db_file] E: MountFatPartition(spunvm) failed, ret [0xE]
Overriding PIL cfg by caller
UsbConfigLibOpenProtocols: PMI version (0x20)
UsbConfigPortsQueryConnectionChange: usbport->connectstate: ATT
APC1 IDDQ WC 0 , L2:0, CPU[0]:175, CPU[1]:175, CPU[2]:175, CPU[3]:175
 APC1 Total 700
LoadSys  TIME 0ms
tsens  TIME 1ms
scm  TIME 2ms
Load CPU 0 Slp: 0x64, Int: 0xBE
Load CPU 1 Slp: 0xE0, Int: 0xFFFFFF65
Load CPU 2 Slp: 0x8D, Int: 0x52
Load CPU 3 Slp: 0x107, Int: 0xFFFFFFB1
Load GPU 0 Slp: 0xB4, Int: 0xFFFFFFE9 adc_min:0x1A8 adc_max:0x1AC
Load NPU 0 Slp: 0x30, Int: 0x17
LmhIsenseInit Pre CPU  TIME 0ms
GLD IDDQ WC 0
LmhIsenseSubSysEntry Post SubSysEntryCb  TIME 0ms
LmhIsenseSubSysEntry Post LmhIsenseSubSysInit  TIME 0ms
LmhIsenseInitSubSys Post SubSysEntry  TIME 0ms
LmhIsenseInitSubSys Post SubSysTrim  TIME 0ms
LmhIsenseInitSubSys Post SubSysExit  TIME 0ms
LmhIsenseInit Post CPU  TIME 0ms
GPU IDDQ 71
LmhIsenseSubSysEntry Post SubSysEntryCb  TIME 0ms
LmhIsenseSubSysEntry Post LmhIsenseSubSysInit  TIME 0ms
LmhIsenseInitSubSys Post SubSysEntry  TIME 0ms
LmhIsenseInitSubSys Post SubSysTrim  TIME 0ms
LmhIsenseInitSubSys Post SubSysExit  TIME 1ms
LmhIsenseInit Post GPU  TIME 2ms
Entering in func1 of NPU  TIME 2ms
NPU IDDQ 78
LmhIsenseSubSysEntry Post SubSysEntryCb  TIME 0ms
LmhIsenseSubSysEntry Post LmhIsenseSubSysInit  TIME 0ms
LmhIsenseInitSubSys Post SubSysEntry  TIME 0ms
LmhIsenseInitSubSys Post SubSysTrim  TIME 0ms
LmhIsenseInitSubSys Post SubSysExit  TIME 0ms
LmhIsenseInit Post NPU  TIME 2ms
isense  TIME 5ms
ISENSE TOTAL TIME 5ms
Disp init wait [ 2850] 
-----------------------------
Platform Init  [ 3098] BDS
INFO: UEFI NV tables are enabled as VOLATILE!
UEFI Ver   : 5.0.200825.BOOT.XF.3.2-00280-SM8250-3
Platform           : MTP
Chip Name          : SM8250
Chip Ver           : 2.1
Chip Serial Number : 0x
Silver cluster Core 0 Freq: 1516 MHz
-----------------------------
4, 4, UEFI Total : 2786 ms
POST Time      [ 4558] OS Loader
Loader Build Info: Aug 25 2020 19:12:02
Battery lid touch: 118294 Ohm(s)
VB: RWDeviceState: Succeed using rpmb!
Initializing Oculus extension...
Total DDR Size: 0x000000017CC00000 
KeyPress:0, BootReason:0
Fastboot=0, Recovery:0
GetVmData: No Vm data present! Status = (0x3)
VM Hyp calls not present
Booting from slot (_a)
Booting Into Mission Mode
Load Image vbmeta_a total time: 1 ms 
Load Image vbmeta_system_a total time: 1 ms 
Load Image vbmeta_vendor_a total time: 1 ms 
Load Image boot_a total time: 260 ms 
Load Image dtbo_a total time: 24 ms 
VB2: Authenticate complete! boot state is: green
VB2: boot state: green(0)
GetVmData: No Vm data present! Status = (0x3)
Memory Base Address: 0x80000000
Apply Overlay total time: 390 ms 
Device will boot into off mode charging mode
Cmdline: androidboot.memcg=1 lpm_levels.sleep_disabled=1 video=vfb:640x400,bpp=32,memsize=3072000 msm_rtb.filter=0x237 service_locator.enable=1 swiotlb=2048 loop.max_part=7 androidboot.hardware=hollywood buildvariant=user androidboot.verifiedbootstate=gre
RAM Partitions
Add Base: 0x0000000080000000 Available Length: 0x000000003CC00000 
Add Base: 0x00000000C0000000 Available Length: 0x0000000140000000 
ERROR: Could not get splash memory region node
Update Device Tree total time: 70 ms 
Shutting Down UEFI Boot Services: 5441 ms
Start EBS        [ 5442] 
BDS: LogFs sync skipped, No Media
App Log Flush : 5 ms
Exit EBS        [ 5456] UEFI End
```
  </div>

</details>
