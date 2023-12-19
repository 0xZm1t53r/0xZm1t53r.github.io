---
title: "Oculus2: SoC JTAG..."
slug: oculus2-soc-jtag
date_published: 2023-01-29T07:49:51.000Z
date_updated: 2023-01-29T08:14:35.000Z
tags: 
categories: Oculus2
image: /assets/img/jtagulator.png
---

JTAG pins were found in my previous post "[Oculus2: HW reverse engineering - test points.]({% link _posts/2022-11-11-oculus2-hw-reverse-engineering-test-points.md %})" I am sure for 99.99999% that, SoC JTAG pins are disabled. A modern SoC has an OTP configuration bit to disable JTAG and other debug features. This step is essential; a product will fail its first security review without this.

JTAGulator has been laid in the closet for a while, but I never tried to use it (that's the shame!). Thus I decided to test JTAG pins and JTAGulator at the same time.

As I expected, JTAGulator had an ancient FW version 1.1. The update process is a bit tricky because the project's [GitHub](https://github.com/grandideastudio/jtagulator) does not have releases anymore, so I had to find a VM with Windows and install the appropriate SW to build and update my JTAGulator. There is Joe Grand's (the author of the tool) video on [youtube](https://youtu.be/xlXwy-weG1M) with instructions on how to do this.

BTW [Joe Grand](https://en.wikipedia.org/wiki/Joe_Grand) is a cool guy, I was at his presentation on the Hardwear.io 2022 USA, and he left only the best impressions!!!

Finally, I connected JTAGulator and Oculus2 and tested them.
![]({{site.url}}{{site.baseurl}}/assets/img/oculus_jtag_connection.jpg)
Disclaimer - no surprises:

- JTAGulator worked as I expected,
- JTAG pins do not look working.

JTAGulator JTAG mode commands:
```
    JTAG Commands:
    J   Identify JTAG pinout
    I   Identify JTAG pinout (IDCODE Scan)
    B   Identify JTAG pinout (BYPASS Scan)
    R   Identify RTCK (adaptive clocking)
    D   Get Device ID(s)
    T   Test BYPASS (TDI to TDO)
    Y   Instruction/Data Register (IR/DR) discovery
    P   Pin mapper (EXTEST Scan)
    O   OpenOCD interface

    General Commands:
    V   Set target I/O voltage
    H   Display available commands
    M   Return to main menu
```

**"Identify JTAG pinout (IDCODE Scan)"** mode somehow detected pins correctly:

```
    JTAG> I
    Enter starting channel [0]:
    Enter ending channel [7]:
    Possible permutations: 336

    Bring channels LOW before each permutation? [y/N]:
    Press spacebar to begin (any other key to abort)...
    JTAGulating! Press any key to abort...

    TDI: N/A
    TDO: 1
    TCK: 2
    TMS: 5
    Device ID #1: 1100 0000101100000000 00000011011 1 (0xC0B00037)
    TRST#: 3

    ---
    IDCODE scan complete.
```

But **"Test BYPASS (TDI to TDO)"** failed:
```
    JTAG> T
    Enter TDI pin [0]:
    Enter TDO pin [1]:
    Enter TCK pin [2]:
    Enter TMS pin [5]:
    Number of devices detected: 2
    Pattern in to TDI:    11101011101101000000000101110101
    Pattern out from TDO: 11110011101101100000000001110110
    No Match!
```

**"Get Device ID(s)"** results also do not look correct:
```
    JTAG> D
    TDI not needed to retrieve Device ID.

    Enter TDO pin [1]:
    Enter TCK pin [2]:
    Enter TMS pin [5]:

    Device ID #1: 1100 0000101100000000 00000011011 1 (0xC0B00037)
    -> Manufacturer ID: 0x01B
    -> Part Number: 0x0B00
    -> Version: 0xC

    IDCODE listing complete.
```

**"Instruction/Data Register (IR/DR) discovery"** also returns the error:
```
    JTAG> Y
    Enter TDI pin [0]:
    Enter TDO pin [1]:
    Enter TCK pin [2]:
    Enter TMS pin [5]:
    Ignore single-bit Data Registers? [Y/n]:
    More than one device detected in the chain!
```

Also, I have tried **"OpenOCD interface"** mode with OpenOCD but did not get any response from the board.

So, for now, my summary is: JTAG pins do not work.
