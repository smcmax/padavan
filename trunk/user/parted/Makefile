SRC_NAME=parted-3.6
SRC_URL=https://ftp.gnu.org/gnu/parted/$(SRC_NAME).tar.xz 

CFLAGS += -ffunction-sections -fdata-sections
LDFLAGS += -Wl,--gc-sections

THISDIR = $(shell pwd)

all: config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
	autoreconf -fvi; \
	PKG_CONFIG_PATH="$(STAGEDIR)/lib/pkgconfig" \
	LIBS="-liconv" \
	./configure \
		--prefix=/usr \
		--without-readline \
		--disable-shared \
		--disable-device-mapper \
		--disable-nls \
		--disable-debug \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done

romfs:
	$(ROMFSINST) $(THISDIR)/$(SRC_NAME)/parted/parted /sbin/parted

