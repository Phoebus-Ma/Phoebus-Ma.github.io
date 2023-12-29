
# u-boot启动详细的代码调用流程

```bash
u-boot.lds:(arch/arm/cpu/u-boot.lds)
    |-->_start:(arch/arm/lib/vectors.S)
        |-->reset(arch/arm/cpu/armv7/start.S)    
            |-->save_boot_params(arch/arm/cpu/armv7/start.S)        /* 将引导参数保存到内存中 */
                |-->save_boot_params_ret(arch/arm/cpu/armv7/start.S)
                    |-->cpu_init_cp15(arch/arm/cpu/armv7/start.S)   /* 初始化 */
                    |-->cpu_init_crit(arch/arm/cpu/armv7/start.S)
                        |-->lowlevel_init(arch/arm/cpu/armv7/lowlevel_init.S)
                    |-->_main(arch/arm/lib/crt0.S)
                        |-->board_init_f_alloc_reserve(common/init/board_init.c)/* 为u-boot的gd结构体分配空间 */
                        |-->board_init_f_init_reserve(common/init/board_init.c) /* 将gd结构体清零 */
                        |-->board_init_f(common/board_f.c)
                            |-->initcall_run_list(include/initcall.h)           /* 初始化序列函数 */
                                |-->init_sequence_f[](common/board_f.c)         /* 初始化序列函数数组 */
                                    |-->board_early_init_f(board/freescale/mx6ull_toto/mx6ull_toto.c)   /*初始化串口的IO配置 */
                                    |-->timer_init(arch/arm/imx-common/timer.c) /* 初始化内核定时器，为uboot提供时钟节拍 */
                                    |-->init_baud_rate(common/board_f.c)        /* 初始化波特率 */
                                    |-->serial_init(drivers/serial/serial.c)    /* 初始化串口通信设置 */
                                    |-->console_init_f(common/console.c)        /* 初始化控制台 */
                                    |-->...
                        |-->relocate_code(arch/arm/lib/relocate.S)      /* 主要完成镜像拷贝和重定位 */
                        |-->relocate_vectors(arch/arm/lib/relocate.S)   /* 重定位向量表 */
                        |-->board_init_r(common/board_r.c)              /* 板级初始化 */
                            |-->initcall_run_list(include/initcall.h)   /* 初始化序列函数 */
                                |-->init_sequence_r[](common/board_f.c) /* 序列函数 */
                                    |-->initr_reloc(common/board_r.c)   /* 设置 gd->flags,标记重定位完成 */
                                    |-->serial_initialize(drivers/serial/serial-uclass.c)   /* 初始化串口 */
                                        |-->serial_init(drivers/serial/serial-uclass.c)     /* 初始化串口 */
                                    |-->initr_mmc(common/board_r.c)                         /* 初始化emmc */
                                        |-->mmc_initialize(drivers/mmc/mmc.c)
                                            |-->mmc_do_preinit(drivers/mmc/mmc.c)
                                                |-->mmc_start_init(drivers/mmc/mmc.c)
                                    |-->console_init_r(common/console.c)                    /* 初始化控制台*/
                                    |-->interrupt_init(arch/arm/lib/interrupts.c)           /* 初始化中断*/
                                    |-->initr_net(common/board_r.c)                         /* 初始化网络设备*/
                                        |-->eth_initialize(net/eth-uclass.c)
                                            |-->eth_common_init(net/eth_common.c)
                                                |-->phy_init(drivers/net/phy/phy.c)
                                            |-->uclass_first_device_check(drivers/core/uclass.c)
                                                |-->uclass_find_first_device(drivers/core/uclass.c)
                                                |-->device_probe(drivers/core/device.c)
                                                    |-->device_of_to_plat(drivers/core/device.c)
                                                        |-->drv->of_to_plat
                                                            |-->fecmxc_of_to_plat(drivers/net/fec_mxc.c)    /* 解析设备树信息 */
                                                    |-->device_get_uclass_id(drivers/core/device.c)
                                                    |-->uclass_pre_probe_device(drivers/core/uclass.c)
                                                    |-->drv->probe(dev)
                                                        /* drivers/net/fec_mxc.c */
                                                        U_BOOT_DRIVER(fecmxc_gem) = {
                                                            .name       = "fecmxc",
                                                            .id         = UCLASS_ETH,
                                                            .of_match   = fecmxc_ids,
                                                            .of_to_plat = fecmxc_of_to_plat,
                                                            .probe      = fecmxc_probe,
                                                            .remove     = fecmxc_remove,
                                                            .ops        = &fecmxc_ops,
                                                            .priv_auto  = sizeof(struct fec_priv),
                                                            .plat_auto  = sizeof(struct eth_pdata),
                                                        };
                                                        |-->fecmxc_probe(drivers/net/fec_mxc.c) /* 探测和初始化 */
                                                            |-->fec_get_miibus(drivers/net/fec_mxc.c)
                                                                |-->mdio_alloc(drivers/net/fec_mxc.c)
                                                                |-->bus->read = fec_phy_read;
                                                                |-->bus->write = fec_phy_write;
                                                                |-->mdio_register(common/miiphyutil.c)
                                                                |-->fec_mii_setspeed(drivers/net/fec_mxc.c)
                                                            |-->fec_phy_init(drivers/net/fec_mxc.c)
                                                                |-->device_get_phy_addr(drivers/net/fec_mxc.c)
                                                                |-->phy_connect(drivers/net/phy/phy.c)
                                                                    |-->phy_find_by_mask(drivers/net/phy/phy.c)
                                                                        |-->bus->reset(bus)
                                                                        |-->get_phy_device_by_mask(drivers/net/phy/phy.c)
                                                                            |-->create_phy_by_mask(drivers/net/phy/phy.c)
                                                                                |-->phy_device_create(drivers/net/phy/phy.c)
                                                                                    |-->phy_probe(drivers/net/phy/phy.c)
                                                                    |-->phy_connect_dev(drivers/net/phy/phy.c)
                                                                        |-->phy_reset(drivers/net/phy/phy.c)
                                                                |-->phy_config(drivers/net/phy/phy.c)
                                                                    |-->board_phy_config(drivers/net/phy/phy.c)
                                                                        |-->phydev->drv->config(phydev)
                                                                            /*drivers/net/phy/smsc.c*/
                                                                            static struct phy_driver lan8710_driver = {
                                                                                .name       = "SMSC LAN8710/LAN8720",
                                                                                .uid        = 0x0007c0f0,
                                                                                .mask       = 0xffff0,
                                                                                .features   = PHY_BASIC_FEATURES,
                                                                                .config     = &genphy_config_aneg,
                                                                                .startup    = &genphy_startup,
                                                                                .shutdown   = &genphy_shutdown,
                                                                            };
                                                                            |-->genphy_config_aneg(drivers/net/phy/phy.c)
                                                                                |-->phy_reset(需要手动调用)(drivers/net/phy/phy.c)
                                                                                |-->genphy_setup_forced(drivers/net/phy/phy.c)
                                                                                |-->genphy_config_advert(drivers/net/phy/phy.c)
                                                                                |-->genphy_restart_aneg(drivers/net/phy/phy.c)
                                                    |-->uclass_post_probe_device(drivers/core/uclass.c)
                                                        |-->uc_drv->post_probe(drivers/core/uclass.c)
                                                            /*net/eth-uclass.c*/
                                                            UCLASS_DRIVER(ethernet) = {
                                                                .name       = "ethernet",
                                                                .id         = UCLASS_ETH,
                                                                .post_bind  = eth_post_bind,
                                                                .pre_unbind = eth_pre_unbind,
                                                                .post_probe = eth_post_probe,
                                                                .pre_remove = eth_pre_remove,
                                                                .priv_auto  = sizeof(struct eth_uclass_priv),
                                                                .per_device_auto    = sizeof(struct eth_device_priv),
                                                                .flags      = DM_UC_FLAG_SEQ_ALIAS,
                                                            };
                                                            |-->eth_post_probe(net/eth-uclass.c)
                                                                |-->eth_write_hwaddr(drivers/core/uclass.c)
                                    |-->...
                                    |-->run_main_loop(common/board_r.c)                 /* 主循环，处理命令 */
                                        |-->main_loop(common/main.c)
                                            |-->bootdelay_process(common/autoboot.c)    /* 读取环境变量bootdelay和bootcmd的内容 */
                                            |-->autoboot_command(common/autoboot.c)     /* 倒计时按下执行，没有操作执行bootcmd的参数 */
                                                |-->abortboot(common/autoboot.c)
                                                    |-->printf("Hit any key to stop autoboot: %2d ", bootdelay);
                                                    /* 到这里就是我们看到uboot延时3s启动内核的地方 */
                                            |-->cli_loop(common/cli.c)                  /* 倒计时按下space键,执行用户输入命令 */
```
