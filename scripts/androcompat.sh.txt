#!/bin/sh
#

# *** 2014-02-07 by Geometry based on package andromize by Sven-Ola

set -e

usergroupadd() {
	grep -q ^$2: /etc/group || groupadd -r -g $1 $2
	grep -q ^$2: /etc/passwd || useradd -r -g $1 -u $1 -d /system $2
}

	# Add UID/GID values statically defined in Bionic Libc
	usergroupadd 1000 a_system       # system server
	usergroupadd 1001 a_radio        # telephony subsystem, RIL
	usergroupadd 1002 a_bluetooth    # bluetooth subsystem
	usergroupadd 1003 a_graphics     # graphics devices
	usergroupadd 1004 a_input        # input devices
	usergroupadd 1005 a_audio      # Android audio devices (audio GID exists on Debian)
	usergroupadd 1006 a_camera       # camera devices
	usergroupadd 1007 a_log          # log devices
	usergroupadd 1008 a_compass      # compass device
	usergroupadd 1009 a_mount        # mountd socket
	usergroupadd 1010 a_wifi         # wifi subsystem
	usergroupadd 1011 a_adb          # android debug bridge (adbd)
	usergroupadd 1012 a_install      # group for installing packages
	usergroupadd 1013 a_media        # mediaserver process
	usergroupadd 1014 a_dhcp         # dhcp client
	usergroupadd 1015 a_sdcard_rw    # external storage write access
	usergroupadd 1016 a_vpn          # vpn system
	usergroupadd 1017 a_keystore     # keystore subsystem
	usergroupadd 1018 a_usb          # USB devices
	usergroupadd 1019 a_drm
	usergroupadd 1020 a_mdnsr
	usergroupadd 1021 a_gps          # GPS daemon
	# End Gingerbread specific
	usergroupadd 1023 a_media_rw     # read-write access to media
	usergroupadd 1024 a_mtp

	usergroupadd 1026 a_drmrpc
	usergroupadd 1027 a_nfc
	usergroupadd 1028 a_sdcard_r     # read-only access to sdcard
	usergroupadd 1029 a_clat
	usergroupadd 1030 a_loop_radio
	usergroupadd 1031 a_media_drm
	usergroupadd 1032 a_package_info
	usergroupadd 1033 a_scdard_pics
	usergroupadd 1034 a_sdcard_av
	usergroupadd 1035 a_sdcard_all

	usergroupadd 2000 a_shell        # adb and debug shell user
	usergroupadd 2001 a_cache        # cache access
	usergroupadd 2002 a_diag         # access to diagnostic resources

	usergroupadd 3001 a_net_bt_admin # bluetooth: create any socket
	usergroupadd 3002 a_net_bt       # bluetooth: create sco, rfcomm or l2cap sockets
	usergroupadd 3003 a_inet         # can create AF_INET and AF_INET6 sockets
	usergroupadd 3004 a_net_raw      # can create raw INET sockets
	usergroupadd 3005 a_net_admin    # can configure interfaces and routing tables.
	usergroupadd 3006 a_net_bw_stats
	usergroupadd 3007 a_net_bw_acct
	usergroupadd 3008 a_net_bt_stack

	usergroupadd 9998 a_misc         # access to misc storage
	usergroupadd 9999 a_nobody     # Android nobody (nobody UID exists on Debian)

	# Create new users/groups with 5000-9999. Note, that interactive UID/GID on Android is 10000+random
	sed -i -e '
		s/^[[:space:]]*\([GU]ID_MIN[[:space:]]\+\)[[:digit:]]\+/\15000/
		s/^[[:space:]]*\([GU]ID_MAX[[:space:]]\+\)[[:digit:]]\+/\18999/
	' /etc/login.defs

	sed -i -e '
		s/^[[:space:]]*\(FIRST_[GU]ID[[:space:]]*=[[:space:]]*\)[[:digit:]]\+/\15000/
		s/^[[:space:]]*\(LAST_[GU]ID[[:space:]]*=[[:space:]]*\)[[:digit:]]\+/\18999/
		s/^[[:space:]]*#\?[[:space:]]*\(ADD_EXTRA_GROUPS[[:space:]]*=[[:space:]]*\)[[:digit:]]\+/\11/
		s/^[[:space:]]*#\?[[:space:]]*\(EXTRA_GROUPS[[:space:]]*=[[:space:]]*\).*/\1"a_system a_shell a_graphics a_input a_log a_adb a_sdcard_rw a_net_bt_admin a_net_bt a_inet a_media_rw a_sdcard_r"/
	' /etc/adduser.conf

