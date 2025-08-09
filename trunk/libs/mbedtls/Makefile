SRC_NAME=mbedtls-2.28.10
SRC_URL=https://github.com/Mbed-TLS/mbedtls/archive/v2.28.10.tar.gz
SRC_DIR=mbedtls-mbedtls-2.28.10 # <-- 这是 GitHub 归档解压后可能的真实目录名

all: download_test extract_test config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)/build

download_test:
	( if [ ! -f $(SRC_NAME).tar.gz ]; then \
		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME).tar.gz $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar -xf $(SRC_NAME).tar.gz; \
		# 关键修复：检查解压后的真实目录名，并将其重命名为我们期望的名称
		if [ -d $(SRC_DIR) ] && [ ! -d $(SRC_NAME) ]; then \
			mv $(SRC_DIR) $(SRC_NAME); \
		fi; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	# 确保源码目录存在
	( [ ! -d $(SRC_NAME) ] && echo "Source directory $(SRC_NAME) not found!" && exit 1 );
	( mkdir -p $(SRC_NAME)/build && \
	  cd $(SRC_NAME)/build && \
	  cmake \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX=$(STAGEDIR) \
		-DCMAKE_TOOLCHAIN_FILE=$(CONFIG_CMAKE_TOOLCHAIN_FILE) \
		-DUSE_SHARED_MBEDTLS_LIBRARY=ON \
		-DUSE_STATIC_MBEDTLS_LIBRARY=OFF \
		-DENABLE_TESTING=OFF \
		-DENABLE_PROGRAMS=OFF \
		-DLINK_WITH_PTHREAD=ON \
		-DENABLE_ZLIB_SUPPORT=ON \
		.. \
	)

clean:
	rm -rf $(SRC_NAME) $(SRC_DIR) # 清理所有可能的目录
	rm -rf config_done

install:
	$(MAKE) -C $(SRC_NAME)/build install DESTDIR=""

romfs:
	cp -fP $(DESTDIR)/lib/libmbedcrypto.so* $(ROMFSDIR)/lib
	cp -fP $(DESTDIR)/lib/libmbedtls.so* $(ROMFSDIR)/lib
	cp -fP $(DESTDIR)/lib/libmbedx509.so* $(ROMFSDIR)/lib
