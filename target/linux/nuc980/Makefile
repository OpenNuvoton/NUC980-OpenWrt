#
# Copyright (C) 2013-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

ARCH:=arm
BOARD:=nuc980
BOARDNAME:=Nuvoton NUC980
FEATURES:=gpio usb targz nand ubifs boot-part rootfs-part jffs2
CPU_TYPE:=arm926ej-s
SUBTARGETS:=nuc980-evb nuc980-iot nuc980-chili

KERNEL_PATCHVER:=4.4

include $(INCLUDE_DIR)/target.mk

KERNELNAME:=uImage dtbs

DEFAULT_PACKAGES += uboot-envtools mkf2fs e2fsprogs blkid

$(eval $(call BuildTarget))
