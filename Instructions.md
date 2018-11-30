# Building Debian image yourself

You can build the image yourself using debootstrap if you're building on an ARM64 host and qemu-debootstrap if you're building on a x64 host.

## Building root filesystem

First of all, you'll need to install the dependencies for building:

<code>sudo apt install debootstrap</code>

<code>sudo apt install debian-archive-keyring</code> (only run this command if you are on an Ubuntu host)

If you're on a x64 host, you'll also need to install QEMU User Mode Emulation packages:

<code>sudo apt install qemu-user-static binfmt-support</code>

After this, you’ll need to create a blank image file. This tutorial assumes that you want to create a minimal Debian install that does not have a desktop environment by default.  

<code>dd if=/dev/zero of=debian-rpi3.img iflag=fullblock bs=1M count=3800 status=progress</code>

Adjust the size by replacing 3800 with the amount of megabytes you want your image to be. For example, to create an image for use on an 8GB storage device, run: 

<code>dd if=/dev/zero of=debian-rpi3.img iflag=fullblock bs=1M count=7800 status=progress</code>

Now, mount the image as a loopback device: 

<code>sudo losetup -f -P --show debian-rpi3.img</code>

The output will tell you the name of the loopback device (eg, /dev/loop13).

If you don’t have GParted installed, install the gparted package with apt. Then, run: 

<code>sudo gparted /dev/[name of loopback device]</code>

After that, you’ll need to create a partition table and then create partitions. Select the unallocated space on the loopback device, and click on the Device menu, and then create a partition table. Leave the partition table type as msdos. The first partition must be formatted as FAT32 or FAT16 and will contain the bootloader files needed for the Pi to boot. The second one must be formatted as ext4 and will contain the Debian filesystem. Apply the operations and exit GParted. 

Now, mount the loopback device:

<code>sudo mount /dev/[name of loopback device]p2 /mnt</code>

<code>sudo mkdir /mnt/boot</code>

<code>sudo mount /dev/name of loopback device]p1 /mnt/boot</code>

By default, loopback devices are mounted as noexec, meaning that you cannot execute commands within them. Remount the device with the ‘exec’ and ‘dev’ flags to remove this restriction: 

<code>sudo mount -i -o remount,exec,dev /mnt</code>

Now, install a minimal Debian system onto the ext4 partition. If you’re building on ARM64, run: 

<code>sudo debootstrap stretch --arch=arm64 /mnt</code>

If you’re building on x64, run: 

<code>sudo qemu-debootstrap stretch --arch=arm64 /mnt</code>

This will take a bit of time depending on your internet connection. 

Once it’s done, use systemd-nspawn to chroot into your ext4 filesystem. 

<code>sudo systemd-nspawn -D /mnt</code>

Install ca-certificates and sudo packages:

<code>apt install ca-certificates sudo</code>

Now, replace /etc/fstab with the following: 

<code>proc /proc proc defaults 0 0</code>

<code>/dev/mmcblk0p1 /boot vfat defaults 0 2</code>

<code>/dev/mmcblk0p2 / ext4 defaults,noatime 0 1</code>

If you plan on using this image on a USB stick or hard drive rather than a microSD card, you’ll need to have programmed the OTP to boot from a USB external device if no microSD is found beforehand (this is unnecessary on the Pi 3B+ as USB booting is already on by default). To configure Debian to boot from USB, put the following in your /etc/fstab instead: 

<code>proc /proc proc defaults 0 0</code>

<code>/dev/sda1 /boot vfat defaults 0 2</code>

<code>/dev/sda2 / ext4 defaults,noatime 0 1</code>

Now, run the following commands to enable networking: 

<code>systemctl enable systemd-networkd.service</code>

<code>systemctl enable systemd-resolved.service</code>

After that, create a user. If you’re creating an image for distribution purposes, use a username such as debian. If you’re creating an image for personal use and do not plan on distributing it, use a personal username: 

<code>adduser [username]</code>

<code>usermod –aG sudo [username]</code>

Once done, exit the chroot environment with the ‘exit’ command. 

## Building ARM64 kernel

The first thing the Raspberry Pi does upon boot is try to locate a FAT partition that contains the bootloader files, and then loads these files. The bootloader search for an ext4 partition and then tries to load the Linux OS from it. 

You can download the latest firmware by running one the following commands: 

<code>git clone https://github.com/raspberrypi/firmware.git</code>

The bootloader files are stored in firmware/boot. Copy all of these files across to the FAT partition found at /mnt/boot. After that, you’ll need to compile a 64-bit kernel and install kernel modules. There’s a nice guide for this here: https://devsidestory.com/build-a-64-bit-kernel-for-your-raspberry-pi-3/ 

## Installing and booting Debian

Once you’ve compiled the kernel and copied it to the FAT partition, and installed the kernel modules to the ext4 partition, you should be good to go! Unmount the ext4 and FAT partitions with the following commands: 

<code>sudo umount /mnt/boot</code>

<code>sudo umount /mnt</code> 

<code>sudo losetup –d debian-rpi3.img</code> 

Now, flash the Debian image file to your Pi’s microSD card or USB storage device. First, find out what the device name of your microSD card or USB storage device. To find out, run sudo fdisk –l and look for the device that has similar properties to your storage device. For example, if you have an 8GB storage device, a 7.9GB device may be your storage device. 

Once you’ve found out the device name of your storage device, flash the image file to it with the following command: 

<code>sudo dd if=debian-rpi3.img of=/dev/<device name> bs=4M status=progress</code>

Once the image has finished being flashed, remove the storage device from your PC and put it into the Pi, and plug in the Pi’s power supply. The Pi should boot to the login prompt, where you can sign into the user account you created earlier. 
