---
title: "Oculus2: Partition layout"
slug: oculus2-partition-layout
date_published: 2022-12-02T08:21:00.000Z
date_updated: 2023-01-23T08:24:14.000Z
tags:
categories: Oculus2
image: /assets/img/Western-Digital-iNAND-HDD-Cover-1.jpg
---

### Oculus 2 partition layout

|N|Name|type|Start|End|Size|
|--- |--- |--- |--- |--- |--- |
|0|||0x000000|6000|0x6000|
|1|fsc|raw|0x6000|26000|0x20000|
|2|fsg|raw|26000|226000|0x200000|
|3|modemst2|raw|226000|426000|0x200000|
|4|modemst1|raw|426000|626000|0x200000|
|5|ALLIGN_TO_128K_2|raw|626000|640000|0x1A000|
|6|secdata|raw|640000|646000|0x6000|
|7|uefivarstore|raw|646000|6C6000|0x80000|
|8|storsec|raw|6C6000|6E6000|0x20000|
|9|pstore|raw|6E6000|8E6000|0x200000|
|10|limits-cdsp|raw|8E6000|8E7000|0x1000|
|11|limits|raw|8E7000|8E8000|0x1000|
|12|spunvm|raw|8E8000|28E8000|0x2000000|
|13|msadp|raw|28E8000|2928000|0x40000|
|14|apdp|raw|2928000|2968000|0x40000|
|15|devinfo|raw|2968000|2969000|0x1000|
|16|hyp_xros_b|raw|2969000|3969000|0x1000000|
|17|vbmeta_vendor_b|raw|3969000|3979000|0x10000|
|18|vbmeta_system_b|raw|3979000|3989000|0x10000|
|19|recovery_b|raw|3989000|7989000|0x4000000|
|20|imagefv_b|raw|7989000|7B89000|0x200000|
|21|featenabler_b|raw|7B89000|7BA9000|0x20000|
|22|uefisecapp_b|raw|7BA9000|7DA9000|0x200000|
|23|dtbo_b|raw|7DA9000|85A9000|0x800000|
|24|vbmeta_b|raw|85A9000|85B9000|0x10000|
|25|qupfw_b|raw|85B9000|85CD000|0x14000|
|26|devcfg_b|raw|85CD000|85ED000|0x20000|
|27|cmnlib64_b|raw|85ED000|866D000|0x80000|
|28|cmnlib_b|raw|866D000|86ED000|0x80000|
|29|boot_b|raw|86ED000|D6ED000|0x5000000|
|30|keymaster_b|raw|D6ED000|D76D000|0x80000|
|31|abl_b|raw|D76D000|D96D000|0x200000|
|32|modem_b|raw|D96D000|1196D000|0x4000000|
|33|hyp_b|raw|1196D000|1216D000|0x800000|
|34|tz_b|raw|1216D000|1256D000|0x400000|
|35|aop_b|raw|1256D000|125ED000|0x80000|
|36|hyp_xros_a|raw|125ED000|135ED000|0x1000000|
|37|vbmeta_vendor_a|raw|135ED000|135FD000|0x10000|
|38|vbmeta_system_a|raw|135FD000|1360D000|0x10000|
|39|recovery_a|raw|1360D000|1760D000|0x4000000|
|40|imagefv_a|raw|1760D000|1780D000|0x200000|
|41|featenabler_a|raw|1780D000|1782D000|0x20000|
|42|uefisecapp_a|raw|1782D000|17A2D000|0x200000|
|43|dtbo_a|raw|17A2D000|1822D000|0x800000|
|44|vbmeta_a|raw|1822D000|1823D000|0x10000|
|45|qupfw_a|raw|1823D000|18251000|0x14000|
|46|devcfg_a|raw|18251000|18271000|0x20000|
|47|cmnlib64_a|raw|18271000|182F1000|0x80000|
|48|cmnlib_a|raw|182F1000|18371000|0x80000|
|49|boot_a|raw|18371000|1D371000|0x5000000|
|50|keymaster_a|raw|1D371000|1D3F1000|0x80000|
|51|abl_a|raw|1D3F1000|1D5F1000|0x200000|
|52|modem_a|raw|1D5F1000|215F1000|0x4000000|
|53|hyp_a|raw|215F1000|21DF1000|0x800000|
|54|tz_a|raw|21DF1000|221F1000|0x400000|
|55|aop_a|raw|221F1000|22271000|0x80000|
|56|ddr|raw|22271000|22471000|0x200000|
|57|cdt|raw|22471000|22491000|0x20000|
|58|ALIGN_TO_128K_1|raw|22491000|224AB000|0x1A000|
|59|xbl_config_b|raw|224AB000|224CB000|0x20000|
|60|xbl_b|raw|224CB000|2284B000|0x380000|
|61|xbl_config_a|raw|2284B000|2286B000|0x20000|
|62|xbl_a|raw|2286B000|22BEB000|0x380000|
|63|userdata|f2fs|22BEB000|1C3664E000|0x1C13A63000 for 128GB (0xD2C263000 for 64GB)|
|64|metadata|raw|1C3664E000|1C3764E000|0x1000000|
|65|xros_b|raw|1C3764E000|1C3F656000|0x8008000|
|66|super|raw|1C3F656000|1DBF656000|0x180000000|
|67|xros_a|raw|1DBF656000|1DC765E000|0x8008000|
|68|frp|raw|1DC765E000|1DC76DE000|0x80000|
|69|misc|raw|1DC76DE000|1DC77DE000|0x100000|
|70|persist|ext4|1DC77DE000|1DCB7DE000|0x4000000|
|71|ssd|raw|1DCB7DE000|1DCB7E0000|0x2000|

iNAND flash is split into 6 logical units (lun)

<details>
  <summary><b>64GB flash LUN layout</b></summary>

<details>
  <summary>Physical partition number: 0</summary>

  <div markdown="1">
```
Block count  : 15471616 (0xEC1400)
Size         : 59.02 GB (60436.00 MB)

P00: GPT              (00000000, 00000006)    24    KB
P01: ssd              (00000006, 00000002)    8     KB
P02: persist          (00000008, 00004000)    64    MB
P03: misc             (00004008, 00000100)    1024  KB
P04: frp              (00004108, 00000080)    512   KB
P05: xros_a           (00004188, 00008008)    128   MB
P06: super            (0000C190, 00180000)    6     GB
P07: xros_b           (0018C190, 00008008)    128   MB
P08: metadata         (00194198, 00001000)    16    MB
P09: userdata         (00195198, 00D2C263)    52.69 GB
```
  </div>

</details>

<details>
  <summary>Physical partition number: 1</summary>

  <div markdown="1">
```
Block count  : 2048 (0x800)
Size         : 8.00 MB

P00: GPT              (00000000, 00000006)    24    KB
P01: xbl_a            (00000006, 00000380)    3584  KB
P02: xbl_config_a     (00000386, 00000020)    128   KB
```
  </div>

</details>

<details>
  <summary>Physical partition number: 2</summary>

  <div markdown="1">
```
Block count  : 2048 (0x800)
Size         : 8.00 MB

P00: GPT              (00000000, 00000006)    24    KB
P01: xbl_b            (00000006, 00000380)    3584  KB
P02: xbl_config_b     (00000386, 00000020)    128   KB
```
  </div>

</details>

<details>
  <summary>Physical partition number: 3</summary>

  <div markdown="1">
```
Block count  : 2048 (0x800)
Size         : 8.00 MB

P00: GPT              (00000000, 00000006)    24    KB
P01: ALIGN_TO_128K_1  (00000006, 0000001A)    104   KB
P02: cdt              (00000020, 00000020)    128   KB
P03: ddr              (00000040, 00000200)    2048  KB
```
  </div>
</details>

<details>
  <summary>Physical partition number: 4</summary>
  <div markdown="1">
```
Block count  : 140288 (0x22400)
Size         : 548.00 MB

P00: GPT              (00000000, 00000006)    24    KB
P01: aop_a            (00000006, 00000080)    512   KB
P02: tz_a             (00000086, 00000400)    4     MB
P03: hyp_a            (00000486, 00000800)    8     MB
P04: modem_a          (00000C86, 00004000)    64    MB
P05: abl_a            (00004C86, 00000200)    2048  KB
P06: keymaster_a      (00004E86, 00000080)    512   KB
P07: boot_a           (00004F06, 00005000)    80    MB
P08: cmnlib_a         (00009F06, 00000080)    512   KB
P09: cmnlib64_a       (00009F86, 00000080)    512   KB
P10: devcfg_a         (0000A006, 00000020)    128   KB
P11: qupfw_a          (0000A026, 00000014)    80    KB
P12: vbmeta_a         (0000A03A, 00000010)    64    KB
P13: dtbo_a           (0000A04A, 00000800)    8     MB
P14: uefisecapp_a     (0000A84A, 00000200)    2048  KB
P15: featenabler_a    (0000AA4A, 00000020)    128   KB
P16: imagefv_a        (0000AA6A, 00000200)    2048  KB
P17: recovery_a       (0000AC6A, 00004000)    64    MB
P18: vbmeta_system_a  (0000EC6A, 00000010)    64    KB
P19: vbmeta_vendor_a  (0000EC7A, 00000010)    64    KB
P20: hyp_xros_a       (0000EC8A, 00001000)    16    MB
P21: aop_b            (0000FC8A, 00000080)    512   KB
P22: tz_b             (0000FD0A, 00000400)    4     MB
P23: hyp_b            (0001010A, 00000800)    8     MB
P24: modem_b          (0001090A, 00004000)    64    MB
P25: abl_b            (0001490A, 00000200)    2048  KB
P26: keymaster_b      (00014B0A, 00000080)    512   KB
P27: boot_b           (00014B8A, 00005000)    80    MB
P28: cmnlib_b         (00019B8A, 00000080)    512   KB
P29: cmnlib64_b       (00019C0A, 00000080)    512   KB
P30: devcfg_b         (00019C8A, 00000020)    128   KB
P31: qupfw_b          (00019CAA, 00000014)    80    KB
P32: vbmeta_b         (00019CBE, 00000010)    64    KB
P33: dtbo_b           (00019CCE, 00000800)    8     MB
P34: uefisecapp_b     (0001A4CE, 00000200)    2048  KB
P35: featenabler_b    (0001A6CE, 00000020)    128   KB
P36: imagefv_b        (0001A6EE, 00000200)    2048  KB
P37: recovery_b       (0001A8EE, 00004000)    64    MB
P38: vbmeta_system_b  (0001E8EE, 00000010)    64    KB
P39: vbmeta_vendor_b  (0001E8FE, 00000010)    64    KB
P40: hyp_xros_b       (0001E90E, 00001000)    16    MB
P41: devinfo          (0001F90E, 00000001)    4     KB
P42: apdp             (0001F90F, 00000040)    256   KB
P43: msadp            (0001F94F, 00000040)    256   KB
P44: spunvm           (0001F98F, 00002000)    32    MB
P45: limits           (0002198F, 00000001)    4     KB
P46: limits-cdsp      (00021990, 00000001)    4     KB
P47: pstore           (00021991, 00000200)    2048  KB
P48: storsec          (00021B91, 00000020)    128   KB
P49: uefivarstore     (00021BB1, 00000080)    512   KB
P50: secdata          (00021C31, 00000006)    24    KB
```
  </div>
</details>

<details>
  <summary>Physical partition number: 5</summary>

  <div markdown="1">
```
Block count  : 2048 (0x800)
Size         : 8.00 MB

P00: GPT              (00000000, 00000006)    24    KB
P01: ALIGN_TO_128K_2  (00000006, 0000001A)    104   KB
P02: modemst1         (00000020, 00000200)    2048  KB
P03: modemst2         (00000220, 00000200)    2048  KB
P04: fsg              (00000420, 00000200)    2048  KB
P05: fsc              (00000620, 00000020)    128   KB
```
  </div>

</details>

</details>
