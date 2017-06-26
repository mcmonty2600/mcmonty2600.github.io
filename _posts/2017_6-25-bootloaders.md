---
layout: post
title: "Bootloaders"
date: 2017-6-25
excerpt:
---

If you are building a custom board with a microcontroller IC on it, the question will arise: what are the options for getting your software binary onto the controllers non-volitile memory?

## Some background reading

### JTAG

[Programming external flash with JTAG](https://www.intellitech.com/products/eclipseboundaryscanflash.asp). A simple explanation of how the flash memory address and data lines are set by shifting bits through the boundary scan registers.

### SWD

### OpenSDA



## Examples

### Case 1: Teensy 3.2

The Teensy 3.2 is a development board from [PJRC](https://www.pjrc.com/store/teensy32.html) which uses NXP's [MK20DX256VLH7](http://www.nxp.com/docs/en/data-sheet/K20P64M72SF1.pdf), and Arm Cortex-M4 core.

The board [uses another microcontroller](https://www.pjrc.com/store/ic_mkl02.html) IC as a bootloader. This is the MKL02 or [MKL04](http://cache.freescale.com/files/32bit/doc/data_sheet/KL04P48M48SF1.pdf), which has an Arm Cortex-M0 core. This is a smaller, less powerful controller in a small package. Looking at the Teensy [schematic](https://www.pjrc.com/teensy/schematic.html), you can see this controller has IO pins connected to the target processor's four JTAG pins (MK20's PTA0, PTA1, PTA2, PTA3, which are TCLK, TMS, TDI, TDO). The purpose of this IC is to load the bootloader program into the RAM of the M4 processor. This is done through the JTAG connection. So the MKL04 "bootloader" IC is really a "bootloader loader."

Once the bootloader is installed on the MK20's memory, the MK20 will be reset to begin executing this bootloader. Notice that the MKL04 does not have a USB connection to the external world. Therefore, loading of the application program into the MK20's memory is not done by the MKL04. The MK20 has an integrated USB controller. This is key, because once the bootloader program is running on the MK20, the writing of the application to the MK20's flash can be accomplished by the MK20 itself, through this USB connection.

I am still uncertain of where the bootloader program is written to on the MK20. It might be written to the RAM, and not the flash. This would leave the entire flash memory avaialble to the application program.

### Case 2: NXP S32K144EVB Evaluation board

The NXP S32K144 is an automotive grade Arm Cortex-M4F. The evaluation platform uses OpenSDA to implement bootloader function. Looking at the [schematic](http://cache.nxp.com/downloads/en/schematics/S32K144EVB-SCH.pdf?fsrch=1&sr=3&pageNum=1), you can see a MK20DX128 (another Cortex-M4) controller is being used to implement the openSDA. This IC has an integrated USB controller; this USB is the interface to the user's PC from where the bitfile will be transferred. The MK20 interfaces to the target controller with SWD interface and also the UART. Thus, open SDA implements both a programming/debug interface and also a serial interface.

The JTAG header J12 actually goes to the JTAG of the MK20. So this must be for reprogramming the bootloader IC, not the target processor.

### Case 3: MAX32630FTHR
