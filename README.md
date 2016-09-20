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
2. If you backed up your Slice directory first then you could access the dt-blob.bin that came with your Slice.  You can download it from the updates site [here](http://updates.fiveninjas.com/slice/dt-blob.bin) or build it yourself using the [sources](https://github.com/FiveNinjas/LibreELEC.tv.old/tree/master/distributions/Slice/config)
3. Copy dt-blob.bin into the FAT32 partition of the flash, so it is alongside config.txt
4. Eject the drive and then reboot Slice 

Now when you boot, the USB and Ethernet should work, but there is no support in the kernel for the audio codec or the LEDs and the RTC has not been enabled.  That's next!

## Linux Kernel

To build the linux kernel you'll need to get the kernel patch from the main Slice LibreELEC distribution and apply it to the upstream Raspberry Pi kernel.

First using either the Slice box, a Raspberry Pi or (much better) cross compile on a linux PC, check out the linux sources:

    $ git clone https://github.com/raspberrypi/linux

Next grab the patch from the sources [here](https://github.com/FiveNinjas/LibreELEC.tv.old/tree/master/projects/Slice/patches/linux) you'll need patches 2 and 3.

Apply the patches to the linux kernel:

    $ cd linux
    $ patch -p1 -m < linux-02-Slice.patch
    $ patch -p1 -m < linux-03-cs4265.patch

The patches may not apply cleanly (the output will have something like "NOT MERGED" in it) but the -m flag will make patch insert conflict markers.  Just edit the files it had trouble with and fix...  In general this is the Kconfig and Makefiles for the sound soc hardware, all we're trying to do is add the extra blocks for the slice drivers, directly reading the patch should help understand the change required.

Next you need to refer to the [Raspberry Pi documentation](https://www.raspberrypi.org/documentation/linux/kernel/building.md) for building the kernel (or cross compiling it), based on cross compiling with a toolchain on the path I use:

    ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make bcmrpi_defconfig
    ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make menuconfig

Then you need to enable the following drivers:
    SND_SOC_CS4265=m
    SND_BCM2708_SOC_SLICE=m
    BCM2835_WS2812=m
    
Hint: You can search for them by typing [/] then 'CS4265' and [Enter], it'll find a match and put a little number in brackets by it, press that number to quickly move to the item

Next build the kernel, the modules and dtb files

    ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=root make zImage dtbs modules -j12

Finally you need to follow instructions from the Raspberry Pi documentation to copy the files across, I'd suggest you boot the Slice and log in there and copy the files over the network, if you nfs mount the filesystem then you can actually do the following to install the modules to /lib/modules

    sudo make modules_install
    
From within the linux tree, don't also forget to run scripts/mkkrnlimg on the kernel and copy it over /boot/kernel.img and copy across all the dtb* files from arch/arm/boot/dts/ and arch/arm/boot/dts/overlays into /boot
