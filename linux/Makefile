#
# Files that need to be bundled with the building system
#

-include ../v4l/.version

MAINDIRS += drivers firmware include sound

# Drivers source code
TARDIR += drivers/media/
TARDIR += drivers/staging/media/
TARDIR += drivers/misc/altera-stapl/
TARFILES += sound/pci/bt87x.c

# Includes

# Pure kernel API
TARDIR += include/media/
TARFILES += include/linux/mmc/sdio_ids.h
TARFILES += include/sound/aci.h
TARFILES += include/uapi/linux/usb/video.h
TARFILES += include/linux/via-core.h
TARFILES += include/linux/ti_wilink_st.h
TARFILES += include/linux/dma-buf.h
TARFILES += include/linux/kconfig.h
TARFILES += include/linux/platform_data/vsp1.h
TARFILES += include/linux/platform_data/camera-rcar.h
TARFILES += include/trace/events/v4l2.h

TARFILES += include/misc/altera.h

# Userspace API
TARDIR += include/uapi/linux/dvb/
TARFILES += include/uapi/linux/videodev2.h
TARFILES += include/uapi/linux/meye.h
TARFILES += include/uapi/linux/ivtv.h
TARFILES += include/uapi/linux/ivtvfb.h
TARFILES += include/uapi/linux/media.h
TARFILES += include/uapi/linux/v4l2-dv-timings.h
TARFILES += include/uapi/linux/v4l2-controls.h
TARFILES += include/uapi/linux/uvcvideo.h
TARFILES += include/uapi/linux/vsp1.h
TARFILES += include/uapi/linux/v4l2-subdev.h
TARFILES += include/uapi/linux/v4l2-common.h
TARFILES += include/uapi/linux/v4l2-mediabus.h

# Other random files
TARFILES += include/linux/fixp-arith.h

# Firmware
TARFILES += firmware/av7110/bootcode.bin.ihex
TARFILES += firmware/av7110/Boot.S
TARFILES += firmware/cpia2/stv0672_vp4.bin.ihex
TARFILES += firmware/ihex2fw.c
TARFILES += firmware/vicam/firmware.H16
TARFILES += firmware/ttusb-budget/dspbootcode.bin.ihex

TODAY_TAR := linux-media-`date "+%Y-%m-%d"`.tar
TODAY_TAR_MD5 := linux-media-`date "+%Y-%m-%d"`.tar.bz2.md5
LATEST_TAR := http://linuxtv.org/downloads/drivers/linux-media-LATEST.tar.bz2
LATEST_TAR_MD5 := http://linuxtv.org/downloads/drivers/linux-media-LATEST.tar.bz2.md5

default:
	make -C ..

help:
	@echo "Use: make tar DIR=<directory>"
	@echo "          untar|clean|distclean"

todaytar:
	@if [ "$(DIR)" = "" ]; then echo "make $@ DIR=<version>"; exit -1; fi
	-rm -f $(PWD)/$(TODAY_TAR).bz2
	tar cf $(PWD)/$(TODAY_TAR) -C $(DIR) $(TARFILES)
	git --git-dir $(DIR)/.git log --pretty=oneline -n3 |sed -r 's,([\x22]),,g; s,([\x25\x5c]),\x5c\\1,g' >git_log
	perl -e 'while (<>) { $$a=$$1 if (m/^\s*VERSION\s*=\s*(\d+)/); $$b=$$1 if (m/^\s*PATCHLEVEL\s*=\s*(\d+)/); $$c=$$1 if (m/^\s*SUBLEVEL\s*=\s*(\d+)/); } printf "#define V4L2_VERSION %d\n", ((($$a) << 16) + (($$b) << 8) + ($$c))' $(DIR)/Makefile > kernel_version.h
	tar rvf $(PWD)/$(TODAY_TAR) git_log kernel_version.h

	for i in $(TARDIR); do \
		if [ "`echo $$i|grep Documentation`" = "" ]; then \
			dir="`(cd $(DIR); find $$i -type f -name '*.[ch]')`"; \
			dir="$$dir `(cd $(DIR); find $$i -type f -name Makefile)`"; \
			dir="$$dir `(cd $(DIR); find $$i -type f -name Kconfig)`"; \
			tar rvf $(PWD)/$(TODAY_TAR) -C $(DIR) $$dir; \
		else \
			tar rvf $(PWD)/$(TODAY_TAR) -C $(DIR) $$i; \
		fi; done; bzip2 $(PWD)/$(TODAY_TAR)
	md5sum $(PWD)/$(TODAY_TAR).bz2 > $(PWD)/$(TODAY_TAR_MD5)

tar:
	@if [ "$(DIR)" = "" ]; then echo "make $@ DIR=<version>"; exit -1; fi
	-rm -f $(PWD)/linux-media.tar.bz2
	tar cf $(PWD)/linux-media.tar -C $(DIR) $(TARFILES)
	git --git-dir $(DIR)/.git log --pretty=oneline -n3 |sed -r 's,([\x22]),,g; s,([\x25\x5c]),\x5c\\1,g' >git_log
	perl -e 'while (<>) { $$a=$$1 if (m/^\s*VERSION\s*=\s*(\d+)/); $$b=$$1 if (m/^\s*PATCHLEVEL\s*=\s*(\d+)/); $$c=$$1 if (m/^\s*SUBLEVEL\s*=\s*(\d+)/); } printf "#define V4L2_VERSION %d\n", ((($$a) << 16) + (($$b) << 8) + ($$c))' $(DIR)/Makefile > kernel_version.h
	tar rvf $(PWD)/linux-media.tar git_log kernel_version.h
	for i in $(TARDIR); do \
		if [ "`echo $$i|grep Documentation`" = "" ]; then \
			dir="`(cd $(DIR); find $$i -type f -name '*.[ch]')`"; \
			dir="$$dir `(cd $(DIR); find $$i -type f -name Makefile)`"; \
			dir="$$dir `(cd $(DIR); find $$i -type f -name Kconfig)`"; \
			tar rvf $(PWD)/linux-media.tar -C $(DIR) $$dir; \
		else \
			tar rvf $(PWD)/linux-media.tar -C $(DIR) $$i; \
		fi; done; bzip2 $(PWD)/linux-media.tar

untar: linux-media.tar.bz2
	tar xfj linux-media.tar.bz2
	-rm -f .patches_applied .linked_dir .git_log.md5

clean:
	-rm -rf $(MAINDIRS) .patches_applied .linked_dir .git_log.md5 git_log kernel_version.h

dir: clean
	@lsdiff --version >/dev/null # Make 'make dir' fail if lsdiff is not installed
	@if [ "$(DIR)" = "" ]; then echo "make $@ DIR=<version>"; exit -1; fi
	@if [ ! -f "$(DIR)/include/uapi/linux/videodev2.h" ]; then echo "$(DIR) does not contain kernel sources"; exit -1; fi
	@echo "Searching in $(DIR)/Makefile for kernel version."
	./use_dir.pl $(DIR)

distclean: clean
	-rm -f linux-media.tar.bz2 linux-media.tar.bz2.md5

apply_patches apply-patches:
	@if [ -e .linked_dir ]; then ./use_dir.pl --recheck --silent; fi
	@if [ "$(VER)" != "" ]; then \
		dir=$(VER); \
	elif [ "$(KERNELRELEASE)" != "" ]; then \
		dir=$(KERNELRELEASE); \
	fi; \
	PATCHES="`./patches_for_kernel.pl $$dir`"; \
	if [ "$$PATCHES" = "" ]; then echo "Version $$dir not supported"; exit -1; fi; \
	if [ -e .patches_applied ]; then \
		if [ "`cat .patches_applied|grep ^#`" = "#$$dir" ]; then \
			echo "Patches for $$dir already applied."; exit; \
		else \
			$(MAKE) unapply_patches; \
		fi; \
	fi; \
	echo "Applying patches for kernel $$dir"; \
	touch .patches_applied; \
	for i in $$PATCHES; do \
		echo patch -s -f -N -p1 -i ../backports/$$i; \
		patch -s -f -N -p1 -i ../backports/$$i --dry-run || exit 1; \
		patch -s -f -N -p1 -i ../backports/$$i; \
		mv .patches_applied .patches_applied.old; \
		echo $$i > .patches_applied; \
		cat .patches_applied.old >> .patches_applied; \
	done; \
	mv .patches_applied .patches_applied.old; \
	echo "#$$dir" > .patches_applied; \
	cat .patches_applied.old >> .patches_applied; \
	rm -f .patches_applied.old; \
	./version_patch.pl; \
	if [ -e .linked_dir ]; then ./use_dir.pl --get_patched; fi

unapply_patches unapply-patches:
	@if [ -e .patches_applied ]; then \
	echo "Unapplying patches"; \
	for i in `cat .patches_applied|grep -v '^#'`; do \
		echo patch -s -f -R -p1 -i ../backports/$$i; \
		patch -s -f -R -p1 -i ../backports/$$i || break; \
	done; \
	rm -f .patches_applied; fi

download:
	wget $(LATEST_TAR_MD5) -O linux-media.tar.bz2.md5.tmp

	@if [ "`cat linux-media.tar.bz2.md5.tmp`" != "`cat linux-media.tar.bz2.md5`" ]; then \
		wget $(LATEST_TAR) -O linux-media.tar.bz2; \
		mv linux-media.tar.bz2.md5.tmp linux-media.tar.bz2.md5; \
	fi
