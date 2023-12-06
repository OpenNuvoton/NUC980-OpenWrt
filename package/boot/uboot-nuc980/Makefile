#
# Copyright (C) 2013-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=u-boot
PKG_VERSION:=2016.11
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/OpenNuvoton/NUC970_U-Boot_v2016.11.git
PKG_SOURCE_DATE:=2022-08-26
PKG_SOURCE_VERSION:=2f0c3aaff9b7b0279420736b93990211f4504e44
PKG_MIRROR_HASH:=f3f5fe7772ed4c79253bf385df3aeb7d61605ec54b632031417a8a23cab95d32

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define uboot/Default
  TITLE:=
  CONFIG:=
  IMAGE:=
endef

define uboot/evb
  TITLE:=U-Boot for EVB board
endef

define uboot/chili
  TITLE:=U-Boot for Chili board
endef

define uboot/iot
  TITLE:=U-Boot for IoT board
endef

define uboot/iotg2
  TITLE:=U-Boot for IoT-G2 board
endef

define uboot/server
  TITLE:=U-Boot for Server board
endef

UBOOTS := \
	evb \
	chili \
	iot \
	iotg2 \
	server

define Package/uboot/template
define Package/uboot-nuc980-$(1)
  SECTION:=boot
  CATEGORY:=Boot Loaders
  DEPENDS:=@TARGET_nuc980
  TITLE:=$(2)
  URL:=http://www.denx.de/wiki/U-Boot
  VARIANT:=$(1)
  MAINTAINER:=Luka Perkov <luka@openwrt.org>
endef
endef

define BuildUBootPackage
	$(eval $(uboot/Default))
	$(eval $(uboot/$(1)))
	$(call Package/uboot/template,$(1),$(TITLE))
endef

ifdef BUILD_VARIANT
$(eval $(call uboot/$(BUILD_VARIANT)))
UBOOT_CONFIG:=$(if $(CONFIG),$(CONFIG),$(BUILD_VARIANT))
UBOOT_IMAGE:=$(if $(IMAGE),$(IMAGE),openwrt-$(BOARD)-$(BUILD_VARIANT)-u-boot.bin)
endif

ifeq ($(subst evb,,$(UBOOT_CONFIG)),)
uboot_defconfig:=nuc980_defconfig
else ifeq ($(subst iotg2,,$(UBOOT_CONFIG)),)
uboot_defconfig:=nuc980_iot_defconfig
else ifeq ($(subst server,,$(UBOOT_CONFIG)),)
uboot_defconfig:=nuc980_eth2uart_defconfig
else
uboot_defconfig:=nuc980_$(UBOOT_CONFIG)_defconfig
endif

define Build/Prepare
	$(call Build/Prepare/Default)
#	$(CP) ./files/* $(PKG_BUILD_DIR)
endef

define Build/Configure
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		$(uboot_defconfig)
endef

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		CROSS_COMPILE=$(TARGET_CROSS)
endef

define Package/uboot/install/default
	$(CP) \
		$(PKG_BUILD_DIR)/u-boot.bin \
		$(BIN_DIR)/$(BOARD)-$(1)-u-boot.bin
	
#ifneq (,$(wildcard $(PKG_BUILD_DIR)/spl/u-boot-spl.bin))
ifneq ($(1), chili)
	$(CP) \
		$(PKG_BUILD_DIR)/spl/u-boot-spl.bin \
		$(BIN_DIR)/$(BOARD)-$(1)-u-boot-spl.bin
endif
endef

define Package/uboot/install/template
define Package/uboot-nuc980-$(1)/install
	$(call Package/uboot/install/default,$(2))
endef
endef

$(foreach u,$(UBOOTS), \
	$(eval $(call Package/uboot/install/template,$(u),$(u))) \
)

$(foreach u,$(UBOOTS), \
	$(eval $(call BuildUBootPackage,$(u))) \
	$(eval $(call BuildPackage,uboot-nuc980-$(u))) \
)
