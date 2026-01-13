ROOT_DIR := $(notdir $(CURDIR))
ifndef QCONFIG
QCONFIG=qconfig.mk
endif
include $(QCONFIG)

HOST_MKIFS := mkifs
HOST_SED := sed
SUFFIXES := .build .bin .raw

# NOTE: This value must match the '[image=<start_addr>]' value in the build file.
IMAGE_LOAD_ADDR = 0x80000

PROCESSOR = aarch64
BUILD_TEMPLATE = $(CURDIR)/../../Templates_build_sdp800
BOARD=rpi5
INSTALL=../install

.PHONY: all clean hypervisor

# Default target - build standard IFS
all: ifs-$(BOARD).bin

# Build hypervisor IFS
hypervisor: ifs-$(BOARD)-hypervisor.bin

# to boot QNX ifs-rpi5.bin IFS image, add "kernel=ifs-rpi5.bin" to bootable microSD card config.txt file.
ifs-$(BOARD).bin: $(BOARD).build
	$(HOST_MKIFS) -v -r$(INSTALL) -r$(QNX_TARGET)/aarch64le -r$(QNX_TARGET) $(MKIFSFLAGS) $^ $@

# to boot QNX ifs-rpi5-hypervisor.bin IFS image, add "kernel=ifs-rpi5-hypervisor.bin" to bootable microSD card config.txt file.
ifs-$(BOARD)-hypervisor.bin: $(BOARD)-hypervisor.build
	$(HOST_MKIFS) -v -r$(INSTALL) -r$(QNX_TARGET)/aarch64le -r$(QNX_TARGET) $(MKIFSFLAGS) $^ $@

# To boot from U-boot "go" command:
#  - load ifs-rpi5.raw IFS image to ${IMAGE_LOAD_ADDR}
#  - run "go ${IMAGE_LOAD_ADDR} ${fdtcontroladdr}"
ifs-$(BOARD).raw: $(BOARD).build
	$(CP_HOST) $(BOARD).build $(BOARD)-go.build
	$(HOST_SED) -i 's/u reg/u arg/' $(BOARD)-go.build
	$(HOST_MKIFS) -v -r$(INSTALL) $(MKIFSFLAGS)  $(BOARD)-go.build $@
	$(RM_HOST) $(BOARD)-go.build

# To boot hypervisor from U-boot "go" command:
#  - load ifs-rpi5-hypervisor.raw IFS image to ${IMAGE_LOAD_ADDR}
#  - run "go ${IMAGE_LOAD_ADDR} ${fdtcontroladdr}"
ifs-$(BOARD)-hypervisor.raw: $(BOARD)-hypervisor.build
	$(CP_HOST) $(BOARD)-hypervisor.build $(BOARD)-hypervisor-go.build
	$(HOST_SED) -i 's/u reg/u arg/' $(BOARD)-hypervisor-go.build
	$(HOST_MKIFS) -v -r$(INSTALL) $(MKIFSFLAGS)  $(BOARD)-hypervisor-go.build $@
	$(RM_HOST) $(BOARD)-hypervisor-go.build

clean:
	$(RM_HOST) ifs-$(BOARD).* ifs-$(BOARD)-hypervisor.* *.sym

-include $(BUILD_TEMPLATE)/template.mk
