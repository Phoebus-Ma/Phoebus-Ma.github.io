
# Rockchip DDR Configure

U-Boot 为 Rockchip Socs 提供了开源 DDR 驱动, 如 RK3036、RK3288 等.您可以访问 arch/arm/rock-chips/soc_name/sdram_xxx.c 来获取更多 hack.


# 1. Change the DDR frequency on RK3288

U-Boot 通过 U_BOOT_DRIVER 初始化 DDR 设备, 并从 devicetree 读取设备 U_BOOT_DRIVER.

例如:

```bash
&dmc {
    rockchip,num-channels = <2>;
    rockchip,pctl-timing = <0x215 0xc8 0x0 0x35 0x26 0x2 0x70 0x2000d
        0x6 0x0 0x8 0x4 0x17 0x24 0xd 0x6
        0x4 0x8 0x4 0x76 0x4 0x0 0x30 0x0
        0x1 0x2 0x2 0x4 0x0 0x0 0xc0 0x4
        0x8 0x1f4>;
    rockchip,phy-timing = <0x48d7dd93 0x187008d8 0x121076
        0x0 0xc3 0x6 0x2>;
    /* Add a dummy value to cause of-platdata think this is bytes */
    rockchip,sdram-channel = /bits/ 8 <0x2 0xa 0x3 0x2 0x2 0x0 0xe 0xe>;
    rockchip,sdram-params = <0x20d266a4 0x5b6 2 533000000 6 9 0>;
};
```

我们需要根据DDR类型配置不同的参数. RK3288支持DDR3和LDDR3. "rockchip,sdram-params"表示struct rk3288_base_params.

```bash
struct rk3288_base_params {
    u32 noc_timing;
    u32 noc_activate;
    u32 ddrconfig;
    u32 ddr_freq;
    u32 dramtype;
    u32 stride;
    u32 odt;
};
```

第5个参数表示ddr类型. 3表示DDR3, 6表示LPDDR3.
第4个参数表示ddr频率. 请注意, RK3288 支持高达 800MHZ 的 DDR, 以及高达 533 的 LPDDR3.


# 2. Use Rockchip's SPL binary

Rockchip提供了一个SPL二进制文件来配置DDR设备, 而不是开源的u-boot-spl二进制文件, 它可以自动检测DDR设备类型并初始化.
Maskrom加载加载流程：

1. Maskrom从mmc设备偏移32K加载DDR.bin;
2. DDR.bin设置SDRAM设备, 然后跳回maskrom;
3. Maskrom 将 U-Boot.bin 加载到内存 0x0.

这样, 在板的配置文件中定义 CONFIG_ROCKCHIP_SPL.

例如:

```bash
diff --git a/configs/miniarm-rk3288_defconfig b/configs/miniarm-rk3288_defconfig
index 33a4a56..63a0293 100644
--- a/configs/miniarm-rk3288_defconfig
+++ b/configs/miniarm-rk3288_defconfig
@@ -8,6 +8,7 @@ CONFIG_DEFAULT_DEVICE_TREE="rk3288-miniarm"
 CONFIG_SPL_STACK_R=y
 CONFIG_SPL_STACK_R_MALLOC_SIMPLE_LEN=0x2000
 CONFIG_HUSH_PARSER=y
+CONFIG_ROCKCHIP_SPL=y
 CONFIG_CMD_BOOTZ=y
 # CONFIG_CMD_IMLS is not set
 CONFIG_CMD_MMC=y
```

注意, 打包镜像的方式和前者不一样:

```bash
$ dd if=32_LPDDR2_533MHz_LPDDR3_533MHz_DDR3_533MHz_20160803.bin of=rk_spl.bin bs=4 skip=1
$ tools/mkimage -n rk3288 -T rksd -d rk_spl.bin rk_spl.img
$ cat u-boot-dtb.bin >> rk_spl.img
```
