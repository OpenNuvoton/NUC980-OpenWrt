#
# Copyright (C) 2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/image.mk

UBIFS_OPTS = -F -m 2048 -e 124KiB -c 4096 -U
UBI_OPTS = -m 2048 -p 128KiB -s 2048 -O 2048

#################################################
# Images
#################################################

DEVICE_VARS += MKUBIFS_OPTS UBOOT BOOT_SCRIPT

define Image/BuildKernel
	$(CP) $(KDIR)/uImage $(BIN_DIR)/$(SUBTARGET)-uImage
endef

define Image/Build
	$(call Image/Build/$(1),$(1))
	$(CP) $(KDIR)/root.$(1) $(BIN_DIR)/$(SUBTARGET)-root.$(1)
endef

define Image/Build/ubifs
	echo "Building ubifs...@@"
	#echo $(PWD)
	mv $(KDIR)/root.ubifs root.ubifs_raw
	ubinize -o root.ubifs $(UBI_OPTS) ubinize.cfg
	rm -f root.ubifs_raw
	mv root.ubifs $(KDIR)/
endef

define Image/Build/jffs2-64k
	echo "Building jffs2-64k..."
endef

define Image/Build/jffs2-128k
	echo "Building jffs2-128k..."
endef

#################################################
# Devices
#################################################

KERNEL_LOADADDR=0x00008000

define Device/Default
  PROFILES := Generic
  FILESYSTEMS := squashfs
  KERNEL_INSTALL := 1
  KERNEL_SUFFIX := -uImage
  KERNEL_NAME := uImage
  KERNEL_PREFIX := $$(IMAGE_PREFIX)
  KERNEL := kernel-bin | uImage none
  IMAGES := sysupgrade.bin
endef

define Device/nuc980-evb
  BOARDNAME := nuc980-evb
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  KERNEL := kernel-bin | uImage none
  KERNEL_SIZE := 4096k
  BLOCKSIZE := 64k
  FILESYSTEMS := jffs2-64k
  IMAGES := sysupgrade.bin
  IMAGE/sysupgrade.bin = append-kernel | pad-to $$$$(KERNEL_SIZE) | append-rootfs | pad-to $$$$(BLOCKSIZE)
endef
TARGET_DEVICES += nuc980-evb

define Device/nuc980-iot
  BOARDNAME := nuc980-iot
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  KERNEL := kernel-bin | uImage none
  KERNEL_SIZE := 10240k
  FILESYSTEMS := ubifs
  PAGESIZE := 2048
  BLOCKSIZE := 128k
  SUBPAGESIZE := 2048
  VID_HDR_OFFSET := 2048
  IMAGES := factory.bin
  IMAGE/factory.bin = append-kernel
endef
TARGET_DEVICES += nuc980-iot

define Device/nuc980-chili
  BOARDNAME := nuc980-chili
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  KERNEL := kernel-bin | uImage none
  KERNEL_SIZE := 4096k
  BLOCKSIZE := 64k
  FILESYSTEMS := jffs2-64k
  IMAGES := sysupgrade.bin
  IMAGE/sysupgrade.bin = append-kernel | pad-to $$$$(KERNEL_SIZE) | append-rootfs | pad-to $$$$(BLOCKSIZE)
endef
TARGET_DEVICES += nuc980-chili

$(eval $(call BuildImage))
