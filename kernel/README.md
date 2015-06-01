# Kernel for Asus tf701t

Source code: https://github.com/cyanogenmod/android_kernel_asus_tf701t

Patches, config by Geometry

Additional patches (for apparmor):
https://bazaar.launchpad.net/~ubuntu-branches/ubuntu/trusty/apparmor/trusty/files/head:/kernel-patches/3.4/

Kexec-hardboot patch by Tasssadar
Ported by Trel725


**Important notice for wifi:**

The kernel "3.4.107-cmaalxhb-17" is configured to look for the wifi firmware files in "/lib/firmware/".
Under Android these files are located at:

- "/vendor/firmware/bcm43341/fw_bcmdhd.bin"
- "/data/misc/wifi/nvram.txt"

Copy both of these files to "/lib/firmware/" for wifi to work.


The older kernel "3.4.105-cmaalx-15" does not include the kexec-hardboot patch and looks for the wifi files in the standard Android locations.

