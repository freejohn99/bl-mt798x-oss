# ATF and u-boot for mt798x

## About bl-mt798x
- https://cmi.hanwckf.top/p/mt798x-uboot-usage

![](/u-boot.gif)

## Prepare

```
sudo apt install gcc-aarch64-linux-gnu build-essential flex bison libssl-dev device-tree-compiler qemu-user-static
```

## Build
```
Usage: SOC=[mt7981|mt7986] BOARD=<board name> MULTI_LAYOUT=[0|1] ./build.sh
eg: SOC=mt7981 BOARD=360t7 ./build.sh
eg: SOC=mt7981 BOARD=wr30u MULTI_LAYOUT=1 ./build.sh
eg: SOC=mt7981 BOARD=cmcc_rax3000m-emmc ./build.sh
eg: SOC=mt7986 BOARD=redmi_ax6000 MULTI_LAYOUT=1 ./build.sh
eg: SOC=mt7986 BOARD=jdcloud_re-cp-03 ./build.sh
```

### CMCC RAX3000Me (fitblk + WebUI)

This fork adds RAX3000Me board support on top of `fit-example`:

| BOARD | Hardware | Output |
|---|---|---|
| `cmcc_rax3000me-nand-ddr3` | SPI-NAND, DDR3-1866 | `*-fip-fixed-parts.bin`, `*-bl2.bin` |
| `cmcc_rax3000me-nand-ddr4` | SPI-NAND, DDR4 | `*-fip-fixed-parts.bin`, `*-bl2.bin` |
| `cmcc_rax3000me-emmc-ddr3` | eMMC, DDR3-1866 | `*-fip.bin`, `*-bl2.bin` |
| `cmcc_rax3000me-emmc-ddr4` | eMMC, DDR4 | `*-fip.bin`, `*-bl2.bin` |

Build examples:

```sh
SOC=mt7981 BOARD=cmcc_rax3000me-nand-ddr3 ./build.sh
SOC=mt7981 BOARD=cmcc_rax3000me-nand-ddr4 ./build.sh
SOC=mt7981 BOARD=cmcc_rax3000me-emmc-ddr3 ./build.sh
SOC=mt7981 BOARD=cmcc_rax3000me-emmc-ddr4 ./build.sh
```

These images use:

```text
official OpenWrt partition layout
root=/dev/fit0 rootwait
U-Boot WebUI failsafe
SPI-NAND UBI for NAND variants
eMMC production/recovery partitions for eMMC variants
```

The U-Boot defaults to the WebUI failsafe on reset (192.168.1.1). Use the
variant that matches the actual hardware: NAND vs eMMC and DDR3 vs DDR4 are
not interchangeable. Flashing the wrong variant can prevent the router from
booting, and recovery may require UART or a programmer.

For NAND variants the UBI size is `116736k` (114 MiB); for eMMC variants the
root filesystem is on the GPT `production` partition. The FIP contains BL31 +
U-Boot; BL2 is produced separately as `*-bl2.bin`.

---

### xiaomi-wr30u multi-layout uboot firmware compatibility
|Firmware type|uboot (default)|uboot (immortalwrt-112m)|uboot (qwrt)|
|:----:|:----:|:----:|:----:|
|[xiaomi stock mtd8/mtd9](https://github.com/hanwckf/xiaomi-router-stock-ubi-bin/tree/main/xiaomi-wr30u)|√|×|×|
|[immortalwrt-mt798x stock](https://github.com/hanwckf/immortalwrt-mt798x/blob/openwrt-21.02/target/linux/mediatek/files-5.4/arch/arm64/boot/dts/mediatek/mt7981-xiaomi-mi-router-wr30u-stock.dts)|√|×|×|
|[OpenWrt stock](https://github.com/openwrt/openwrt/blob/main/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-stock.dts)|√|×|×|
|[immortalwrt stock](https://github.com/immortalwrt/immortalwrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-stock.dts)|√|×|×|
|[X-Wrt stock](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-stock.dts)|√|×|×|
|[immortalwrt-mt798x 112m](https://github.com/hanwckf/immortalwrt-mt798x/blob/openwrt-21.02/target/linux/mediatek/files-5.4/arch/arm64/boot/dts/mediatek/mt7981-xiaomi-mi-router-wr30u-112m.dts)|×|√|×|
|[GL.iNet by 237176253](https://www.right.com.cn/forum/thread-8297881-1-1.html)|×|√|×|
|[X-Wrt 112m nmbm](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-112m-nmbm.dts)|×|√|×|
|[OpenWrt 112m nmbm](https://github.com/openwrt/openwrt/blob/main/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-112m-nmbm.dts)|×|√|×|
|[immortalwrt 112m nmbm](https://github.com/immortalwrt/immortalwrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-112m-nmbm.dts)|×|√|×|
|[X-Wrt 112m nmbm](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-112m-nmbm.dts)|×|√|×|
|[QWRT](https://www.right.com.cn/forum/thread-8284824-1-1.html)|×|×|√|
|[OpenWrt ubootmod](https://github.com/openwrt/openwrt/blob/main/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-ubootmod.dts)|×|×|×|
|[immortalwrt ubootmod](https://github.com/immortalwrt/immortalwrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-ubootmod.dts)|×|×|×|
|[X-Wrt ubootmod](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7981b-xiaomi-mi-router-wr30u-ubootmod.dts)|×|×|×|

### redmi-ax6000 multi-layout uboot firmware compatibility
|Firmware type|uboot (default)|uboot (immortalwrt-110m)|
|:----:|:----:|:----:|
|[xiaomi stock mtd8/mtd9](https://github.com/hanwckf/xiaomi-router-stock-ubi-bin/tree/main/redmi-ax6000)|√|×|
|[immortalwrt-mt798x stock](https://github.com/hanwckf/immortalwrt-mt798x/blob/openwrt-21.02/target/linux/mediatek/files-5.4/arch/arm64/boot/dts/mediatek/mt7986a-xiaomi-redmi-router-ax6000-stock.dts)|√|×|
|[OpenWrt stock](https://github.com/openwrt/openwrt/blob/main/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-stock.dts)|√|×|
|[immortalwrt stock](https://github.com/immortalwrt/immortalwrt/blob/master/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-stock.dts)|√|×|
|[X-Wrt stock](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-stock.dts)|√|×|
|[immortalwrt-mt798x](https://github.com/hanwckf/immortalwrt-mt798x/blob/openwrt-21.02/target/linux/mediatek/files-5.4/arch/arm64/boot/dts/mediatek/mt7986a-xiaomi-redmi-router-ax6000.dts)|×|√|
|[GL.iNet by 237176253](https://www.right.com.cn/forum/thread-8297881-1-1.html)|×|√|
|[X-Wrt ubootlayout](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-ubootlayout.dts)|×|√|
|[OpenWrt ubootmod](https://github.com/openwrt/openwrt/blob/main/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-ubootmod.dts)|×|×|
|[immortalwrt ubootmod](https://github.com/immortalwrt/immortalwrt/blob/master/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-ubootmod.dts)|×|×|
|[X-Wrt ubootmod](https://github.com/x-wrt/x-wrt/blob/master/target/linux/mediatek/dts/mt7986a-xiaomi-redmi-router-ax6000-ubootmod.dts)|×|×|
