#!/bin/sh
#

echo "/usr/lib/arm-linux-gnueabihf/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf
echo "/usr/lib/arm-linux-gnueabihf/tegra-egl" > /usr/lib/arm-linux-gnueabihf/tegra-egl/ld.so.conf
update-alternatives --install /etc/ld.so.conf.d/arm-linux-gnueabihf_EGL.conf arm-linux-gnueabihf_egl_conf /usr/lib/arm-linux-gnueabihf/tegra-egl/ld.so.conf 1000
cd /usr/lib/xorg/modules/drivers/
ln -s tegra_drv.abi13.so tegra_drv.so
