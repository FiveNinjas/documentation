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

Now when you boot, the USB and Ethernet should work, but there is no support in the kernel for the audio codec or the LEDs and the RTC has not been enabled.  That's next!

## Linux Kernel

