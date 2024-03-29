#
# Copyright (C) 2006-2015 BoluWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=grub
PKG_VERSION:=2.02~rc1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://alpha.gnu.org/gnu/grub \
	http://gnualpha.uib.no/grub/ \
	http://mirrors.fe.up.pt/pub/gnu-alpha/grub/ \
	http://www.nic.funet.fi/pub/gnu/alpha/gnu/grub/
PKG_HASH:=445239e9b96d1143c194c1d37851cf4196b83701c60172e49665e9d453d80278

PKG_FIXUP:=autoreconf
HOST_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=grub2/host

PKG_SSP:=0

PKG_FLAGS:=nonshared

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/grub2
  CATEGORY:=Boot Loaders
  SECTION:=boot
  TITLE:=GRand Unified Bootloader
  URL:=http://www.gnu.org/software/grub/
  DEPENDS:=@TARGET_x86||TARGET_x86_64
endef

define Package/grub2-editenv
  CATEGORY:=Utilities
  SECTION:=utils
  SUBMENU:=Boot Loaders
  TITLE:=Grub2 Environment editor
  URL:=http://www.gnu.org/software/grub/
  DEPENDS:=@TARGET_x86||TARGET_x86_64
endef

define Package/grub2-editenv/description
	Edit grub2 environment files.
endef

HOST_BUILD_PREFIX := $(STAGING_DIR_HOST)

CONFIGURE_VARS += \
	grub_build_mkfont_excuse="don't want fonts"

CONFIGURE_ARGS += \
	--target=$(REAL_GNU_TARGET_NAME) \
	--disable-werror \
	--disable-nls \
	--disable-device-mapper \
	--disable-libzfs \
	--disable-grub-mkfont

HOST_CONFIGURE_VARS += \
	grub_build_mkfont_excuse="don't want fonts"

HOST_CONFIGURE_ARGS += \
	--disable-grub-mkfont \
	--target=$(REAL_GNU_TARGET_NAME) \
	--sbindir="$(STAGING_DIR_HOST)/bin" \
	--disable-werror \
	--disable-libzfs \
	--disable-nls

HOST_MAKE_FLAGS += \
	TARGET_RANLIB=$(TARGET_RANLIB) \
	LIBLZMA=$(STAGING_DIR_HOST)/lib/liblzma.a

define Host/Configure
	$(SED) 's,(RANLIB),(TARGET_RANLIB),' $(HOST_BUILD_DIR)/grub-core/Makefile.in
	$(Host/Configure/Default)
endef

define Package/grub2-editenv/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/grub-editenv $(1)/usr/sbin/
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,grub2))
$(eval $(call BuildPackage,grub2-editenv))
