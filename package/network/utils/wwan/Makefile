include $(TOPDIR)/rules.mk

PKG_NAME:=wwan
PKG_VERSION:=2014-07-17
PKG_RELEASE=1

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=

PKG_MAINTAINER:=John Crispin <john@phrozen.org>

include $(INCLUDE_DIR)/package.mk

define Package/wwan
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Generic BoluWrt 3G/4G proto handler
endef

define Build/Compile
	true
endef

define Package/wwan/install
	$(INSTALL_DIR) $(1)/lib/netifd/proto/
	$(CP) ./files/wwan.sh $(1)/lib/netifd/proto/
	$(INSTALL_DIR) $(1)/etc/hotplug.d/usb
	$(INSTALL_BIN) ./files/wwan.usb $(1)/etc/hotplug.d/usb/00_wwan.sh
	$(INSTALL_DIR) $(1)/etc/hotplug.d/usbmisc
	$(INSTALL_BIN) ./files/wwan.usbmisc $(1)/etc/hotplug.d/usbmisc/00_wwan.sh
	$(INSTALL_DIR) $(1)/lib/network/wwan/
	$(INSTALL_DATA) ./files/data/* $(1)/lib/network/wwan/
	#in order to keep the Lede GIT repo free of filenames with colons,
	#we name the files xxxx-yyyy
	# and rename here after copying to the build directory
	shopt -s nullglob ; \
	for filevar in $(1)/lib/network/wwan/*-* ; \
	do \
		FILENAME=$$$$(basename $$$$filevar) ; \
		NEWNAME=$$$${FILENAME//-/:} ; \
		mv "$(1)/lib/network/wwan/$$$$FILENAME" "$(1)/lib/network/wwan/$$$$NEWNAME" ; \
	done
endef

$(eval $(call BuildPackage,wwan))
