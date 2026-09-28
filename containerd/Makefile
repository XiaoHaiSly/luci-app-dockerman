include $(TOPDIR)/rules.mk

PKG_NAME:=containerd
PKG_VERSION:=2.3.4
PKG_RELEASE:=1
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:linuxfoundation:containerd

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/containerd/containerd/tar.gz/v${PKG_VERSION}?
PKG_HASH:=175bbf57d637c987fa742f846b43b1b8ba2c61af6a9eaec619c625e4a8a19b69

PKG_MAINTAINER:=Gerard Ryan <G.M0N3Y.2503@gmail.com>

PKG_BUILD_DEPENDS:=golang/host dockerd
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/containerd/containerd

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/containerd
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=containerd container runtime
  URL:=https://containerd.io/
  DEPENDS:=$(GO_ARCH_DEPENDS) +btrfs-progs +runc
  MENU:=1
endef

define Package/containerd/description
An industry-standard container runtime with an emphasis on simplicity, robustness and portability
endef

GO_PKG_INSTALL_EXTRA:=\
	vendor/google.golang.org/protobuf/internal/editiondefaults/editions_defaults.binpb \
	Makefile \
	vendor/modules.txt

MAKE_PATH:=$(GO_PKG_WORK_DIR_NAME)/build/src/$(GO_PKG)
MAKE_VARS += $(GO_PKG_VARS)
MAKE_FLAGS += \
	VERSION=$(PKG_VERSION) \
	REVISION=$(PKG_SOURCE_VERSION) \
	PREFIX=""

ifeq ($(CONFIG_SELINUX),y)
MAKE_FLAGS += BUILDTAGS='selinux'
else
MAKE_FLAGS += BUILDTAGS=''
endif

# Reset golang-package.mk overrides so we can use the Makefile
Build/Compile=$(call Build/Compile/Default)

# Fix build error caused by missing info/git file required by //go:embed in nri vendor dependency
define Build/Compile/Pre
	mkdir -p $(PKG_BUILD_DIR)/$(MAKE_PATH)/vendor/github.com/containerd/nri/pkg/version/info
	echo "v$(PKG_VERSION)" > $(PKG_BUILD_DIR)/$(MAKE_PATH)/vendor/github.com/containerd/nri/pkg/version/info/git
endef
Hooks/Compile/Pre += Build/Compile/Pre

define Package/containerd/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/{ctr,containerd,containerd-stress,containerd-shim-runc-v2} $(1)/usr/bin/
endef

$(eval $(call BuildPackage,containerd))
