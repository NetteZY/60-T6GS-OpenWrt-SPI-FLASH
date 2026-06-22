# OpenWrt for Qihoo 360 T6GS (SPI NOR version)

This is a custom OpenWrt firmware built specifically for the **16MB SPI NOR flash** on the Qihoo 360 T6GS. 

for when your nand is dead and router is bootlooping

## The Bootloop Problem
Most 360 T6GS firmwares are built for the 128MB NAND flash. When you try to flash a standard NAND firmware through Breed, your router will bootloop because Breed writes to the NOR chip while the kernel expects the OS to be on the NAND chip.

This release fixes the issue by bypassing the NAND completely. It uses the `jedec,spi-nor` driver so you can flash OpenWrt directly from Breed.

## Installation
1. Boot into Breed (Hold reset while plugging in power).
2. Go to **Firmware (固件更新)**.
3. Check **Firmware (固件)** and select `openwrt-ramips-mt7621-qihoo_360t6gs-NOR-sysupgrade.bin`.
4. Set the Flash Layout to **public 0x50000** (公版 0x50000).
5. Click upload and flash.
6. Wait 2-3 minutes for the red LED to stop flashing. 
7. Access LuCI at `192.168.1.1`.

## Storage Note
Because this runs on the 16MB SPI NOR chip, you will have about ~6.8MB of free space for packages. It's fully stable and includes LuCI and SQM out of the box.

## Build it yourself
If compiling from source, you need to modify two files:

1. **`target/linux/ramips/image/mt7621.mk`** (Switch from NAND to standard SPI NOR):
```makefile
define Device/qihoo_360t6gs
  $(Device/dsa-migration)
  $(Device/uimage-lzma-loader)
  IMAGE_SIZE := 16064k
  DEVICE_VENDOR := Qihoo
  DEVICE_MODEL := 360T6GS
  DEVICE_PACKAGES += kmod-mt7915-firmware luci luci-app-sqm sqm-scripts ttyd
endef
TARGET_DEVICES += qihoo_360t6gs
```

2. **`target/linux/ramips/dts/mt7621_qihoo_360t6gs.dts`**: Replace the `&nand` node with `&spi0` and standard `fixed-partitions`. See the provided `.dts` file for the exact layout.

## Credits
Special thanks to **善良的坏boy** from the Right.com.cn forum for creating the original hardware device tree configuration that made this possible!
