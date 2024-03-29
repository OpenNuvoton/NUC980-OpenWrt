#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=dnsmasq
PKG_VERSION:=2.78
PKG_RELEASE:=6

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://thekelleys.org.uk/dnsmasq/
PKG_HASH:=89949f438c74b0c7543f06689c319484bd126cc4b1f8c745c742ab397681252b

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_CONFIG_DEPENDS:=CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_dhcpv6 \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_dnssec \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_auth \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_ipset \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_conntrack \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_noid \
	CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_broken_rtc

include $(INCLUDE_DIR)/package.mk

define Package/dnsmasq/Default
  SECTION:=net
  CATEGORY:=Base system
  TITLE:=DNS and DHCP server
  URL:=http://www.thekelleys.org.uk/dnsmasq/
  USERID:=dnsmasq=453:dnsmasq=453
endef

define Package/dnsmasq
$(call Package/dnsmasq/Default)
  VARIANT:=nodhcpv6
endef

define Package/dnsmasq-dhcpv6
$(call Package/dnsmasq/Default)
  TITLE += (with DHCPv6 support)
  DEPENDS:=@IPV6
  VARIANT:=dhcpv6
endef

define Package/dnsmasq-full
$(call Package/dnsmasq/Default)
  TITLE += (with DNSSEC, DHCPv6, Auth DNS, IPset, Conntrack, NO_ID enabled by default)
  DEPENDS:=+PACKAGE_dnsmasq_full_dnssec:libnettle \
	+PACKAGE_dnsmasq_full_ipset:kmod-ipt-ipset \
	+PACKAGE_dnsmasq_full_conntrack:libnetfilter-conntrack
  VARIANT:=full
endef

define Package/dnsmasq/description
  It is intended to provide coupled DNS and DHCP service to a LAN.
endef

define Package/dnsmasq-dhcpv6/description
$(call Package/dnsmasq/description)

This is a variant with DHCPv6 support
endef

define Package/dnsmasq-full/description
$(call Package/dnsmasq/description)

This is a fully configurable variant with DHCPv6, DNSSEC, Authoritative DNS and
IPset, Conntrack support & NO_ID enabled by default.
endef

define Package/dnsmasq/conffiles
/etc/config/dhcp
/etc/dnsmasq.conf
endef

define Package/dnsmasq-full/config
	if PACKAGE_dnsmasq-full
	config PACKAGE_dnsmasq_full_dhcpv6
		bool "Build with DHCPv6 support."
		depends on IPV6
		default y
	config PACKAGE_dnsmasq_full_dnssec
		bool "Build with DNSSEC support."
		default y
	config PACKAGE_dnsmasq_full_auth
		bool "Build with the facility to act as an authoritative DNS server."
		default y
	config PACKAGE_dnsmasq_full_ipset
		bool "Build with IPset support."
		default y
	config PACKAGE_dnsmasq_full_conntrack
		bool "Build with Conntrack support."
		default y
	config PACKAGE_dnsmasq_full_noid
		bool "Build with NO_ID. (hide *.bind pseudo domain)"
		default y
	config PACKAGE_dnsmasq_full_broken_rtc
		bool "Build with HAVE_BROKEN_RTC."
		default n
	endif
endef

Package/dnsmasq-dhcpv6/conffiles = $(Package/dnsmasq/conffiles)
Package/dnsmasq-full/conffiles = $(Package/dnsmasq/conffiles)

TARGET_CFLAGS += -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

COPTS = $(if $(CONFIG_IPV6),,-DNO_IPV6) -DNO_INOTIFY

ifeq ($(BUILD_VARIANT),nodhcpv6)
	COPTS += -DNO_DHCP6
endif

ifeq ($(BUILD_VARIANT),full)
	COPTS += $(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_dhcpv6),,-DNO_DHCP6) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_dnssec),-DHAVE_DNSSEC) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_auth),,-DNO_AUTH) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_ipset),,-DNO_IPSET) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_conntrack),-DHAVE_CONNTRACK,) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_noid),-DNO_ID,) \
		$(if $(CONFIG_PACKAGE_dnsmasq_$(BUILD_VARIANT)_broken_rtc),-DHAVE_BROKEN_RTC)
	COPTS += $(if $(CONFIG_LIBNETTLE_MINI),-DNO_GMP,)
else
	COPTS += -DNO_AUTH -DNO_IPSET -DNO_ID
endif

MAKE_FLAGS := \
	$(TARGET_CONFIGURE_OPTS) \
	CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	COPTS="$(COPTS)" \
	PREFIX="/usr"

define Package/dnsmasq/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) $(PKG_INSTALL_DIR)/usr/sbin/dnsmasq $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/dhcp.conf $(1)/etc/config/dhcp
	$(INSTALL_DATA) ./files/dnsmasq.conf $(1)/etc/dnsmasq.conf
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/dnsmasq.init $(1)/etc/init.d/dnsmasq
	$(INSTALL_DIR) $(1)/etc/hotplug.d/ntp
	$(INSTALL_DATA) ./files/dnsmasqsec.hotplug $(1)/etc/hotplug.d/ntp/25-dnsmasqsec
endef

Package/dnsmasq-dhcpv6/install = $(Package/dnsmasq/install)

define Package/dnsmasq-full/install
$(call Package/dnsmasq/install,$(1))
ifneq ($(CONFIG_PACKAGE_dnsmasq_full_dnssec),)
	$(INSTALL_DIR) $(1)/usr/share/dnsmasq
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/trust-anchors.conf $(1)/usr/share/dnsmasq
endif
endef

$(eval $(call BuildPackage,dnsmasq))
$(eval $(call BuildPackage,dnsmasq-dhcpv6))
$(eval $(call BuildPackage,dnsmasq-full))
