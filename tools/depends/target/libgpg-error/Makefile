include ../../Makefile.include
DEPS= ../../Makefile.include Makefile tvos_remove-fork.patch libgpg-error-1.36-gawk5-support.patch

# lib name, version
LIBNAME=libgpg-error
VERSION=1.36
SOURCE=$(LIBNAME)-$(VERSION)
ARCHIVE=$(SOURCE).tar.bz2

# configuration settings
CONFIGURE=./configure --prefix=$(PREFIX) \
  --disable-doc \
  --disable-tests \
  --disable-languages \
  --disable-nls --disable-shared

LIBDYLIB=$(PLATFORM)/src/.libs/$(LIBNAME).a

all: .installed-$(PLATFORM)

$(TARBALLS_LOCATION)/$(ARCHIVE):
	cd $(TARBALLS_LOCATION); $(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(PLATFORM): $(TARBALLS_LOCATION)/$(ARCHIVE) $(DEPS)
	rm -rf $(PLATFORM)/*; mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); patch -p1 -i ../libgpg-error-1.36-gawk5-support.patch
ifeq ($(TARGET_PLATFORM),appletvos)
	cd $(PLATFORM); patch -p1 -i ../tvos_remove-fork.patch
endif
	cd $(PLATFORM); $(AUTORECONF) -vif
	cd $(PLATFORM); $(CONFIGURE)

$(LIBDYLIB): $(PLATFORM)
ifeq ($(OS),darwin_embedded)
ifeq ($(CPU),arm64)
	cp $(PLATFORM)/src/syscfg/lock-obj-pub.aarch64-apple-darwin.h $(PLATFORM)/src/syscfg/lock-obj-pub.arm-apple-darwin.h
endif
endif
ifeq ($(OS),android)
ifeq ($(CPU),arm64-v8a)
	cp lock-obj-pub.aarch64-unknown-linux-android.h $(PLATFORM)/src/syscfg/lock-obj-pub.linux-android.h
else
	cp $(PLATFORM)/src/syscfg/lock-obj-pub.arm-unknown-linux-androideabi.h $(PLATFORM)/src/syscfg/lock-obj-pub.linux-android.h
endif
endif
	$(MAKE) -C $(PLATFORM)

.installed-$(PLATFORM): $(LIBDYLIB)
	$(MAKE) -C $(PLATFORM) install
	touch $@

clean:
	$(MAKE) -C $(PLATFORM) clean
	rm -f .installed-$(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM)
