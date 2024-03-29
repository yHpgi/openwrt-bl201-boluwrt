#
# Copyright (C) 2006-2010 BoluWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

curdir:=package

include $(INCLUDE_DIR)/rootfs.mk

-include $(TMP_DIR)/.packagedeps
$(curdir)/builddirs:=$(sort $(package-) $(package-y) $(package-m))
$(curdir)/builddirs-install:=.
$(curdir)/builddirs-default:=. $(sort $(package-y) $(package-m))
$(curdir)/builddirs-prereq:=. $(sort $(prereq-y) $(prereq-m))
ifdef CHECK_ALL
$(curdir)/builddirs-check:=$($(curdir)/builddirs)
$(curdir)/builddirs-download:=$($(curdir)/builddirs)
endif
ifneq ($(IGNORE_ERRORS),)
  package-y-filter := $(package-y)
  package-m-filter := $(filter-out $(package-y),$(package-m))
  package-n-filter := $(filter-out $(package-y) $(package-m),$(package-))
  package-ignore-errors := $(filter n m y,$(IGNORE_ERRORS))
  package-ignore-errors := $(if $(package-ignore-errors),$(package-ignore-errors),n m)
  package-ignore-subdirs := $(sort $(foreach m,$(package-ignore-errors),$(package-$(m)-filter)))
  $(curdir)/builddirs-ignore-download := $(package-ignore-subdirs)
  $(curdir)/builddirs-ignore-compile := $(package-ignore-subdirs)
  $(curdir)/builddirs-ignore-host-download := $(package-ignore-subdirs)
  $(curdir)/builddirs-ignore-host-compile := $(package-ignore-subdirs)
endif

PACKAGE_INSTALL_FILES:= \
	$(foreach pkg,$(sort $(package-y)), \
		$(foreach variant, \
			$(if $(strip $(package/$(pkg)/variants)), \
				$(package/$(pkg)/variants), \
				$(if $(package/$(pkg)/default-variant), \
					$(package/$(pkg)/default-variant), \
					default \
				) \
			), \
			$(PKG_INFO_DIR)/$(lastword $(subst /,$(space),$(pkg))).$(variant).install \
		) \
	)

$(curdir)/cleanup: $(TMP_DIR)/.build
	rm -rf $(STAGING_DIR_ROOT)

$(curdir)/merge:
	rm -rf $(PACKAGE_DIR_ALL)
	mkdir -p $(PACKAGE_DIR_ALL)
	-$(foreach pdir,$(PACKAGE_SUBDIRS),$(if $(wildcard $(pdir)/*.ipk),ln -s $(pdir)/*.ipk $(PACKAGE_DIR_ALL);))

$(curdir)/merge-index: $(curdir)/merge
	(cd $(PACKAGE_DIR_ALL) && $(SCRIPT_DIR)/ipkg-make-index.sh . 2>&1 > Packages; )

$(curdir)/install: $(TMP_DIR)/.build $(curdir)/system/opkg/host/compile $(curdir)/merge $(if $(CONFIG_TARGET_PER_DEVICE_ROOTFS),$(curdir)/merge-index)
	- find $(STAGING_DIR_ROOT) -type d | $(XARGS) chmod 0755
	rm -rf $(TARGET_DIR) $(TARGET_DIR_ORIG)
	[ -d $(TARGET_DIR)/tmp ] || mkdir -p $(TARGET_DIR)/tmp
	$(call opkg,$(TARGET_DIR)) install \
		$(call opkg_package_files,$(shell cat $(PACKAGE_INSTALL_FILES) 2>/dev/null))
	@for file in $(PACKAGE_INSTALL_FILES); do \
		[ -s $$file.flags ] || continue; \
		for flag in `cat $$file.flags`; do \
			$(call opkg,$(TARGET_DIR)) flag $$flag `cat $$file`; \
		done; \
	done || true
	@-$(MAKE) package/preconfig

	$(CP) $(TARGET_DIR) $(TARGET_DIR_ORIG)

	$(call prepare_rootfs,$(TARGET_DIR))

$(curdir)/index: FORCE
	@echo Generating package index...
	@for d in $(PACKAGE_SUBDIRS); do ( \
		mkdir -p $$d; \
		cd $$d || continue; \
		$(SCRIPT_DIR)/ipkg-make-index.sh . 2>&1 > Packages.manifest; \
		grep -vE '^(Maintainer|LicenseFiles|Source|Require)' Packages.manifest > Packages && \
			gzip -9nc Packages > Packages.gz; \
	); done
ifdef CONFIG_SIGNED_PACKAGES
	@echo Signing package index...
	@for d in $(PACKAGE_SUBDIRS); do ( \
		[ -d $$d ] && \
			cd $$d || continue; \
		$(STAGING_DIR_HOST)/bin/usign -S -m Packages -s $(BUILD_KEY); \
	); done
endif

$(curdir)/preconfig:

$(curdir)/flags-install:= -j1

$(eval $(call stampfile,$(curdir),package,prereq,.config))
$(eval $(call stampfile,$(curdir),package,cleanup,$(TMP_DIR)/.build))
$(eval $(call stampfile,$(curdir),package,compile,$(TMP_DIR)/.build))
$(eval $(call stampfile,$(curdir),package,install,$(TMP_DIR)/.build))
$(eval $(call stampfile,$(curdir),package,check,$(TMP_DIR)/.build))

$(eval $(call subdir,$(curdir)))
