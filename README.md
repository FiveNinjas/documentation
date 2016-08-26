# Introduction

This repo contains documentation for FiveNinjas Slice.

## Re-flashing Slice

To access the 4GByte internal eMMC Flash on a Slice (for re-imaging / installing other OSes etc.):

1. Attach Slice to a Windows/Linux PC (or a Pi) via micro USB as if you were using it normally in disk access mode.
2. 'Safely eject' the USB drive that appears (Windows) or unmount the drive in Linux if it was automatically mounted. (NB Don't un-plug the micro-USB connection).
3. With the micro-USB still connected, plug in Slice's main DC power. This will switch Slice into 'reflash' mode (BCM2708 USB Boot mode).
4. Follow the instructions for flashing the Raspberry Pi Compute Moudle [here](https://github.com/raspberrypi/documentation/blob/master/hardware/computemodule/cm-emmc-flashing.md) noting that you are already in USB boot mode (same as for a CMIO board plugging the host machine into USB slave port (J15) and applying power.)

## Setting up a filesystem

Once you have used the above rpiboot software to convert the Slice into a mass storage device to access the flash you should be able to use dd on an Mac or Linux machine or use Win32DiskImager on Windows to write a standard Raspbian Lite image to the disk.

After you have done this your Slice will boot to a prompt but USB and Ethernet will not work.  So you have to add a dt-blob.bin to the first (FAT32) partition of the flash.

1. Repeat the instructions above to run rpiboot to convert the Slice into a Mass storage device to gain access to the flash, when you have done this the Slice's first FAT32 directory should appear as a new drive and contain files such as 'bootcode.bin' and 'start.elf'
2. If you backed up your Slice directory first then you could access the dt-blob.bin that came with your Slice.  You can download it from the updates site [here](http://updates.fiveninjas.com/slice/dt-blob.bin) or build it yourself using the [sources](https://github.com/FiveNinjas/LibreELEC.tv/tree/master/distributions/Slice/config)
3. Copy dt-blob.bin into the FAT32 partition of the flash, so it is alongside config.txt
4. Eject the drive and then reboot Slice

## Linux Kernel

FiveNinjas/linux contains a fork of the Raspberry Pi Linux tree. Slice specific branches are prefixed with slice- (as of this writing the default branch is slice-4.0.y).

To build (and install) the kernel follow the instructions for building a Raspberry Pi kernel [here](https://github.com/raspberrypi/documentation/blob/master/linux/kernel/building.md)

The only difference is that you need to use slice2708_defconfig and not bcmrpi_defconfig and make sure to clone the FiveNinjas Linux repository (https://github.com/FiveNinjas/linux.git) rather than the Raspberry Pi one.

## Installing Raspbian

Here are the basic steps to install Raspbian:

1. Flash Slice with the latest Raspberry Pi Raspbian image (currently 2015-05-05).
2. Follow the process above and build and install the Linux kernel, modules and device tree files to Slice.
3. grab the FiveNinjas/slice-firmware repository and compile dt-blob.bin, copy this to the root of the Slice boot partition (first FAT partition).
4. The latest BCM270x firmware is required, grab start.elf and bcm2708-rpi-cm.dtb from the /boot folder in https://github.com/raspberrypi/firmware/ and copy them to the Slice boot partition (NB on future releases of Rasbian this step should not be necessary).
5. Edit config.txt on the Slice boot partition and add the line dtoverlay=slice to add the Slice DT overlay which enables the RTC, IR and Audio.
6. Slice should now boot into Raspbian with all peripheral drivers loaded.
7. Once booted into Raspbian firstly copy your /boot/kernel.img to e.g. /boot/kernel-slice.img, then do an rpi-update to update all of the firmware files, then copy back the /boot/kernel-slice.img to /boot/kernel.img (as the firmware updater will overwrite the kernel with it's own non-slice version).
