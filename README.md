# Introduction

This repo contains documentation for FiveNinjas Slice.

## Re-flashing Slice

To access the 4GByte internal eMMC Flash on a Slice (for re-imaging / installing other OSes etc.):

1. Attach Slice to a Windows/Linux PC (or a Pi) via micro USB as if you were using it normally in disk access mode.
2. 'Safely eject' the USB drive that appears (Windows) or unmount the drive in Linux if it was automatically mounted. (NB Don't un-plug the micro-USB connection).
3. With the micro-USB still connected, plug in Slice's main DC power. This will switch Slice into 'reflash' mode (BCM2708 USB Boot mode).
4. Follow the instructions for flashing the Raspberry Pi Compute Moudle [here](https://github.com/raspberrypi/documentation/blob/master/hardware/computemodule/cm-emmc-flashing.md) noting that you are already in USB boot mode (same as for a CMIO board plugging the host machine into USB slave port (J15) and applying power.)

## Linux Kernel

FiveNinjas/linux contains a fork of the Raspberry Pi Linux tree. Slice specific branches are prefixed with slice- (as of this writing the default branch is slice-4.0.y).

To build (and install) the kernel follow the instructions for building a Raspberry Pi kernel [here](https://github.com/raspberrypi/documentation/blob/master/linux/kernel/building.md)

The only difference is that you need to use slice2708_defconfig and not bcmrpi_defconfig and make sure to clone the FiveNinjas Linux repository (https://github.com/FiveNinjas/linux.git) rather than the Raspberry Pi one.

## Installing Raspbian

Raspbian should soon be available to install via the Slice 'NOOBS' installer, however if you want to roll your own now (and flash directly to Slice wiping everything else) here are the basic steps:

1. Flash Slice with the latest Raspberry Pi Raspbian release (currently 2015-05-05).
2. Follow the process above and build and install the Linux kernel, modules and device tree files to Slice.
3. grab the FiveNinjas/slice-firmware repository and compile dt-blob.bin, copy this to the root of the Slice boot partition (first FAT partition).
4. The latest BCM270x firmware is required, grab start.elf and bcm2708_rpi_cm.dtb from https://github.com/raspberrypi/firmware/boot and copy them to the Slice boot partition (NB on future releases of Rasbian this step should not be necessary).
5. Edit config.txt on the Slice boot partition and add the line dtoverlay=slice to add the Slice DT overlay which enables the RTC, IR and Audio.
6. Slice should now boot into Raspbian with all peripheral drivers loaded.
