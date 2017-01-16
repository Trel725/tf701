# Kernel for Asus tf701t

Source code: https://github.com/cyanogenmod/android_kernel_asus_tf701t

Patches, config by Geometry

Additional patches (for apparmor):
https://bazaar.launchpad.net/~ubuntu-branches/ubuntu/trusty/apparmor/trusty/files/head:/kernel-patches/3.4/

Kexec-hardboot patch by Tasssadar
Ported by Trel725

**Update 2017-01-16**

The most recent kernel is "3.4.113-cmaalxhb-22". It is based on the "cm-12.1" branch of the cyanogenmod source. This is the recommended kernel. It includes the kexec-hardboot patch, has improved security features and includes selinux (turned off by default). It also includes the BFQ scheduler (selected as default) that should improve I/O latency for concurrent operations on SSD, see here: http://algo.ing.unimo.it/people/paolo/disk_sched/


**Important notice for wifi:**

The kernels "3.4.113-cmaalxhb-22" and "3.4.107-cmaalxhb-17" are configured to look for the wifi firmware files in "/lib/firmware/".
Under Android these files are located at:

- "/vendor/firmware/bcm43341/fw_bcmdhd.bin"
- "/data/misc/wifi/nvram.txt"

Copy both of these files to "/lib/firmware/" for wifi to work.


The older kernel "3.4.105-cmaalx-15" does not include the kexec-hardboot patch and looks for the wifi files in the standard Android locations.

