include $(TOPDIR)/rules.mk

PKG_NAME:=usbmode
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL=$(BoluWrt_GIT)/project/usbmode.git
PKG_SOURCE_DATE:=2017-05-24
PKG_SOURCE_VERSION:=453da8e540b1c53d357b897d6c70372cd4633390
PKG_MIRROR_HASH:=b4a7132b940192f2e9a74bd890bd7008ede701e1d52284c3ade0cdc7d663929f
CMAKE_INSTALL:=1

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=

PKG_MAINTAINER:=Felix Fietkau <nbd@nbd.name>

PKG_DATA_VERSION:=20170205
PKG_DATA_URL:=http://www.draisberghof.de/usb_modeswitch
PKG_DATA_PATH:=usb-modeswitch-data-$(PKG_DATA_VERSION)
PKG_DATA_FILENAME:=$(PKG_DATA_PATH).tar.bz2

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Download/data
  FILE:=$(PKG_DATA_FILENAME)
  URL:=$(PKG_DATA_URL)
  HASH:=e2dcfd9d28928d8d8f03381571a23442b3c50d48d343bc40a1a07d01662738d1
endef
$(eval $(call Download,data))

define Package/usb-modeswitch
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libubox +libblobmsg-json +libusb-1.0
  TITLE:=USB mode switching utility
endef

define Build/Prepare
	$(Build/Prepare/Default)
	tar xvfj $(DL_DIR)/$(PKG_DATA_FILENAME) -C $(PKG_BUILD_DIR)
	rm -f \
		$(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/05c6:1000:sVe=GT
	cp ./data/* $(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/
	#in order to keep the Lede GIT repo free of filenames with colons,
	#we name the files xxxx-yyyy
	# and rename here after copying to the build directory
	for filevar in $(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/*-* ; \
	do \
		[ -f "$$$$filevar" ] || continue ; \
		FILENAME=$$$$(basename $$$$filevar) ; \
		NEWNAME=$$$${FILENAME//-/:} ; \
		rm "$(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/$$$$NEWNAME" ; \
		mv "$(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/$$$$FILENAME" "$(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/$$$$NEWNAME" ; \
	done
endef

define Package/usb-modeswitch/install
	$(INSTALL_DIR) $(1)/etc/hotplug.d/usb $(1)/etc/init.d $(1)/sbin
	perl $(PKG_BUILD_DIR)/convert-modeswitch.pl \
		$(PKG_BUILD_DIR)/$(PKG_DATA_PATH)/usb_modeswitch.d/* \
		> $(1)/etc/usb-mode.json
	$(INSTALL_DATA) ./files/usbmode.hotplug $(1)/etc/hotplug.d/usb/20-usb_mode
	$(INSTALL_BIN) ./files/usbmode.init $(1)/etc/init.d/usbmode
	$(CP) $(PKG_INSTALL_DIR)/usr/sbin/usbmode $(1)/sbin/
endef

$(eval $(call BuildPackage,usb-modeswitch))
