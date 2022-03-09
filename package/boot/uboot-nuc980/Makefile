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
PKG_SOURCE_DATE:=2022-01-17
PKG_SOURCE_VERSION:=ca47203cd21107174ee2d16a68b394e0f99516bd
PKG_MIRROR_HASH:=

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define uboot/Default
  TITLE:=
  CONFIG:=
  IMAGE:=
endef

define uboot/iot
  TITLE:=U-Boot for IoT board
endef

define uboot/evb
  TITLE:=U-Boot for EVB board
endef

define uboot/chili
  TITLE:=U-Boot for chili board
endef

UBOOTS := \
	iot \
	evb \
	chili

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
else
uboot_defconfig:=nuc980_$(UBOOT_CONFIG)_defconfig
endif

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
