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
	$(CP) $(KDIR)/linux-4.4.182/arch/arm/boot/dts/$(SUBTARGET)-v1.0.dtb $(BIN_DIR)/
	$(CP) $(KDIR)/../../../Nuvoton/uboot_env/$(SUBTARGET)-u-boot-env.txt $(BIN_DIR)/
endef

define Image/Build
	echo "Image/Build" $(1)
	$(call Image/Build/$(1),$(1))
endef


#################################################
# Devices
#################################################

KERNEL_LOADADDR=0x00008000

define Device/Default
  PROFILES := Generic
  KERNEL_INSTALL := 1
  KERNEL_SUFFIX := -uImage
  KERNEL_NAME := uImage
  KERNEL_PREFIX := $$(IMAGE_PREFIX)
  KERNEL := kernel-bin | uImage none
endef

ifeq ($(SUBTARGET),nuc980-evb)
define Device/nuc980-evb
  BOARDNAME := nuc980-evb
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  IMAGE_SIZE := 14336k
  PAGESIZE := 2048
  BLOCKSIZE := 128k
  FILESYSTEMS := squashfs ubifs
  SUPPORTED_DEVICES += nuc980-evb
  IMAGES := firmware.bin sysupgrade.bin
  IMAGE/firmware.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | check-size $$$$(IMAGE_SIZE)
  IMAGE/sysupgrade.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | append-metadata | check-size $$$$(IMAGE_SIZE)
endef
TARGET_DEVICES += nuc980-evb
endif

ifeq ($(SUBTARGET),nuc980-chili)
define Device/nuc980-chili
  BOARDNAME := nuc980-chili
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  IMAGE_SIZE := 15360k
  BLOCKSIZE := 64k
  FILESYSTEMS := squashfs jffs2-64k
  SUPPORTED_DEVICES += nuc980-chili
  IMAGES := firmware.bin sysupgrade.bin
  IMAGE/firmware.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-rootfs | check-size $$$$(IMAGE_SIZE)
  IMAGE/sysupgrade.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-rootfs | check-size $$$$(IMAGE_SIZE)
endef
TARGET_DEVICES += nuc980-chili
endif

ifeq ($(SUBTARGET),nuc980-iot)
define Device/nuc980-iot
  BOARDNAME := nuc980-iot
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  IMAGE_SIZE := 14336k
  PAGESIZE := 2048
  BLOCKSIZE := 128k
  FILESYSTEMS := squashfs ubifs
  SUPPORTED_DEVICES += nuc980-iot
  IMAGES := firmware.bin sysupgrade.bin
  IMAGE/firmware.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | check-size $$$$(IMAGE_SIZE)
  IMAGE/sysupgrade.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | append-metadata | check-size $$$$(IMAGE_SIZE)
endef
TARGET_DEVICES += nuc980-iot
endif

ifeq ($(SUBTARGET),nuc980-iotg2)
define Device/nuc980-iotg2
  BOARDNAME := nuc980-iotg2
  DEVICE_PROFILE = $$(BOARDNAME)
  PROFILES = Default Minimal $$(DEVICE_PROFILE)
  IMAGE_SIZE := 14336k
  PAGESIZE := 2048
  BLOCKSIZE := 128k
  FILESYSTEMS := squashfs ubifs
  SUPPORTED_DEVICES += nuc980-iotg2
  IMAGES := firmware.bin sysupgrade.bin
  IMAGE/firmware.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | check-size $$$$(IMAGE_SIZE)
  IMAGE/sysupgrade.bin := append-kernel | pad-to $$$$(BLOCKSIZE) | append-ubi | append-metadata | check-size $$$$(IMAGE_SIZE)
endef
TARGET_DEVICES += nuc980-iotg2
endif

$(eval $(call BuildImage))
