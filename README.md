# tf701

Linux for Asus Transformer Pad tf701 || Author of most stuff: Geometry | Posted by Trel725

# Installation (for Xubuntu rootfs)

- Download the file http://d-h.st/hNmV
- create the directory "/data/media/linuxroot" on your device's internal storage
- extract (as root) the file to this directory

* $ su
* $ cd /data/media/root
* $ tar -xf {path to tf701t-xubuntu-14.04-rootfs.tar.gz}


As well as installing to internal storage, you can install rootfs on SDcard just by unpacking it to SD, formatted in ext* filesystem.


# Booting

Download the "xubuntu-boot.img" file from ~/prebuilt or build it at your own.
Go to bootloader on your tabet (power + volume down)

- from your PC boot the tf701t via fastboot: (assuming you run linux)

* $ sudo fastboot boot xubuntu-boot.img

The standard user is called "ubuntu" with real name "Test User" and password "ubuntu".

It won't destroy any data. When you reboot, you'll get the same android system without any changes.

If you're like linux and want install it permanently, you can flash xubuntu-boot.img instead of recovery by command:

* $ sudo fastboot flash recovery xubuntu-boot.img

You'll not be able to go into recovery, but instead get a complete another OS
You can get your recovery back just by re-flashing it.

If you've installed rootfs in external storage use linux_ext.img from ~/prebuilt instead of xubuntu-boot.img

# Building rootfs

For this to be maximally useful to other developers I have constructed the rootfs in a modular and stepwise fashion as descibed in the following. To reproduce, I assume you use a linux system and have installed the packages "qemu-user-static", "abootimg" and "android-tools-fastboot" (that is how the packages are called under Ubuntu). You will build up the rootfs entirely on your linux system. I write <ROOT> for the directory that serves as the file system root.

1. Extract the following files to <ROOT>:

- "ubuntu-core-14.04.2-core-armhf.tar.gz" (http://cdimage.ubuntu.com/ubuntu-core/releases/14.04/release/ubuntu-core-14.04.2-core-armhf.tar.gz) from the Ubuntu core repository. This is a generic minimal rootfs.
- "kernel-cmaalx-15.tar.gz" from ~/kernel. This provides kernel and modules.
- "fstab_bind.tar.gz" (in ~/modules) This assures mounting of the Android /system partition
- "inet-tf701t-trusty.tar.gz" (in ~/modules) This sets a hostname and sets up the ubuntu software repository.
- "initramfs-bindmount.tar.gz" (in ~/modules)  This enables bind mount for the initramfs.
- "nomod.tar.gz" (in ~/modules) Makes sure that the initramfs does not include extra modules as these would make it too large.
- "root_media_linuxroot.tar.gz" (in ~/modules) This makes sure the initramfs looks for the rootfs at "/data/media/linuxroot". Change this if you want to put it in a different directory.
- "init-tf701t.tar.gz" (in ~/modules) Some necessary or useful initialization commands for the tf701t after boot.
- "bluetooth-tf701t.tar.gz" (in ~/modules) This makes bluetooth work. It works similarly as done previously for the tf300t.
- "udev-tf701t.tar.gz" (in ~/modules) Some necessary device settings for the tf701t.

2. Now do:


*  $ cd <ROOT>
*  $ sudo mkdir install
*  $ sudo mkdir data
*  $ sudo ln -s data host
*  $ sudo mkdir system
*  $ sudo ln -s system/vendor vendor

"install" will serve to hold some instalation files. The other directories are for mount points and for the kernel to find wifi and bluetooth firmware.

3. Copy "androcompat.sh.txt" (in ~/scripts) to <ROOT>/install/ and copy "/usr/bin/qemu-arm-static" to <ROOT>/usr/bin/
(Make sure "androcompat.sh.txt" is executable)

4. Enter the rootfs via chroot:


*  $ LC_ALL=C sudo chroot <ROOT> /usr/bin/qemu-arm-static /bin/bash -i

In the chroot do the following:


*  $ apt-get update
*  $ apt-get dist-upgrade
*  $ /install/androcompat.sh.txt
*  $ adduser <USERNAME>
*  $ addgroup <USERNAME> adm
*  $ addgroup <USERNAME> sudo

The apt-get commands initalize the Ubuntu software repository and update packages. The "androcompat.sh" script changes the base UID to 5000 and adds users by default to some Android groups. This is useful for better compatibility with Android. You can omit this setp if not desired. The adduser command adds a user. Choose the name you like as <USERNAME>. Leave the chroot with "exit"

At this point the rootfs is a fully functional non-graphical Ubuntu 14.04. If this is what you want skip to step 9. If you want a system with GUI, proceed.

5. You need a number of xorg packages from previous Ubuntu releases. Get these from the corresponding Ubuntu armhf repositories:
- from 12.04:
libxi6 (this is more up to date than the 13.04 version)
- from 13.04:
xserver-xorg-core
libxfixes3
xserver-xorg-input-evdev
xserver-xorg-input-multitouch
xserver-xorg-video-dummy (might not be necessary)
- from 13.10:
libgl1-mesa-dri

Copy all these packes to <ROOT>/install/
Also copy "graphics-setup-tf701t.sh.txt" (in ~/scripts) to <ROOT>/install/
Make sure this is executable.

6. Now enter a chroot as in step 4. In the chroot:


*  $ dpkg -i /install/*.deb
*  $ apt-mark hold xserver-xorg-core libgl1-mesa-dri
*  $ apt-get -f install

Exit the chroot. In the first step the xorg packaes are installed. Ignore the error messages. The second step ensures that they will not be updated to newer (and incompatible) versions. The third step fixes the missing dependencies.

7. Now you need the nvidia graphics drivers for Tegra 4, from this page. Download the Dalmor driver packages file and codec packages file. Extract the following files to <ROOT>:
- "nvidia_drivers.tbz2" to be found inside the extracted driver file
- "nvgstapps.tbz2" also inside the extracted driver file
- "restricted_codecs.tbz2" from inside the extracted codecs file
Also extract:
- "xorg_conf.tar.gz" (in ~/modules) Configuration file for xorg.
- "brightness-fix-tf701t.tar.gz" (in ~/modules) This fixes the display brightness issues for lightdm and upon suspend/resume, includes saving and restoring display brightness values.
- "nvpmxfce.tar.gz" This fixes display corruption upon resume, as for the tf300t, see here.

8. Enter again the chroot:


*  $ /install/graphics-setup-tf701t.sh.txt
*  $ apt-get install language-pack-en xubuntu-desktop
*  $ apt-get remove gnome-power-manager unity-control-center unity-settings-daemon gnome-session-bin gnome-user-share gnome-bluetooth indicator-session indicator-datetime indicator-bluetooth indicator-keyboard indicator-power
*  $ apt-get autoremove

And exit. The first step sets up some nvidia graphics libraries. The second step installs the xubuntu system. This may take some time. The remaining setps clean up some superfluous packages.

9. Still in the chroot do:


*  $ apt-get clean
*  $ update-initramfs -c -k 3.4.105-cmaalx-15

The first setp is to clean the package cache so that the rootfs becomes smaller. The second step generates the initramfs.

10. For additional clean up remove the files in <ROOT>/install:


*  $ sudo rm -r <ROOT>/install

Now the rootfs in finished and you may wrap it up. For example with tar:


*  $ cd <ROOT>
*  $ sudo tar czv --acl --xattrs -f <TARGETFILE.tar.gz> .

This you deploy to the tf701t as described at the beginning of the post.
 

This procedure should be easily adaptable to other ubuntu flavours, by replacing "xubuntu-desktop" in step 8 with "lubuntu-desktop" for example, also KDE might be worth a try. (standard Ubuntu itself does not work, however, as it needs an up to date xserver.) I hope that many of the provided ingredients could also be useful for other linux distros.

# Boot image generation

It remains to generate the boot image. To this end copy the file "boot_cfg.txt" from ~/kernel to a directory. Then, copy "initrd.img-3.4.105-cmaalx-15" (the initramfs) and "vmlinuz-3.4.105-cmaalx-15" (the kernel) from <ROOT>/boot/ to this same directory (this files in ~/kernel/kernel-cmaalx-15.tar.gz). In this directory create the boot image with the "abootimg" tool:


*  $ abootimg --create xubuntu-boot.img -f boot_cfg.txt -k vmlinuz-3.4.105-cmaalx-15 -r initrd.img-3.4.105-cmaalx-15



# Arch Linux

As well as installing Xubuntu, you can install Arch. It is not so friendly and some some things working worse. Installation is same, but you should download arch rootfs: https://mega.co.nz/#!b8w3VJaJ!SpD1GTIkNiiAmYiCUxmhOYjX6NYN8IeFPhiY6N9Xtkc, directory for installing should has "archlinux" and you should use arch_bind.img instead of xubuntu-boot.img. If you like booting from SDcard, you can use same linux_ext.img

