# Introduction

This repo contains documentation for FiveNinjas Slice.

## Re-flashing Slice

To access the 4GByte internal eMMC Flash on a Slice (for re-imaging / installing other OSes etc.):

1. Attach Slice to a Windows/Linux PC (or a Pi) via micro USB as if you were using it normally in disk access mode.
2. 'Safely eject' the USB drive that appears (Windows) or unmount the drive in Linux if it was automatically mounted. (NB Don't un-plug the micro-USB connection).
3. With the micro-USB still connected, plug in Slice's main DC power. This will switch Slice into 'reflash' mode (BCM2708 USB Boot mode).
4. Follow the instructions for flashing the Raspberry Pi Compute Moudle [here](https://github.com/raspberrypi/documentation/blob/master/hardware/computemodule/cm-emmc-flashing.md) noting that you are already in USB boot mode (same as for a CMIO board plugging the host machine into USB slave port (J15) and applying power.)

## Linux Kernel

FiveNinjas/linux contains a fork of the Raspberry Pi Linux tree. Raspberry Pi specific branches start rpi- (e.g. as of this writing rpi-4.0.y is the branch in active development). Slice specific branches are prefixed with slice- (as of this writing the active (and only) branch is slice-4.0.y).

To build the kernel follow the instructions for building a Raspberry Pi kernel [here](https://github.com/raspberrypi/documentation/blob/master/linux/kernel/building.md)

The only difference is that you need to use slice2708_defconfig and not bcmrpi_defconfig.
