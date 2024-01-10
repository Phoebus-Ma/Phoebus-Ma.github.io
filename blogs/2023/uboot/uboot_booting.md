
# 1. Introduction

U-Boot 是一个主要用于嵌入式系统的引导加载程序，可以支持多种不同的计算机系统结构，包括arm、mips、powerpc、risc-v与x86等。

bootloader table

| Bootloader | Description   | x86 | arm |
| ---------- | ------------- | --- | --- |
| U-boot     | 通用引导程序   |  Y  |  Y  |
| GRUB       | GNU的LILO替代 |  Y  |  N  |
| SysLinux   | 启动加载器集合 |  Y  |  Y  |
| CoreBoot   | 原名LinuxBIOS |  Y  |  Y  |

SoC booting flow:

rom code --> u-boot --> kernel --> rootfs.

- rom code：存在于SoC内部的rom中，是bl0阶段，负责初始化内部ram和外部rom，并把外部rom(如emmc)中的bl1阶段代码加载进内部ram中；
- u-boot:   有3个文件，bl1阶段镜像和bl2阶段镜像，和设备树；
- kernel：  这里指linux kernel；
- rootfs：  文件系统；

bl1阶段(u-boot称为spl，Secondary program loader，如u-boot-sunxi-spl.bin)由汇编编写，主要是初始化外部ram(如ddr)，把bl2阶段代码加载进外部ram，重定向并转移控制权给bl2代码；
bl2阶段(u-boot.bin)由汇编和C编写，加载设备树，对必要外部设备进行初始化和引导内核启动，并转移控制权给内核。

详细过程：

- **bl0**

bootstrap，引导程序，SoC厂家提供，位于SoC的 iROM 中(容量如64 KB)，起始地址是：0x00000000，上电运行，此时CPU的频率一般为外部晶振的频率，它会配置启动模式，初始化外部存储器，以MMC为例，会在 MMC 控制器的SD卡的第16个扇区 (1个扇区512 byte，即第一个8 KB处，一些新的SoC也会在第16扇区查找不到时，查询第256扇区)，查找bl1阶段的镜像，并把它加载到SoC的 iRAM 中(三星某款SoC的iRAM起始地址是0x02060000，容量256 KB)，并运行bl1的二进制文件。

以瑞芯微RK3588 ARM SoC为例，它支持以下设备启动：

1. Nor flash，1 bit或4 bit宽度；
2. Nand flash，1 bit宽度；
3. eMMC，8 bit宽度；
4. SDMMC，4 bit宽度；

- **bl1**

u-boot-spl.bin (Secondary program loader)，二级程序加载器，由u-boot提供，它会做以下任务：设置异常向量表，切换CPU到SVC模式(管理模式)，关闭中断，关闭TLB、cache、MMU。初始化timer、dram，加载u-boot.bin到ram的顶部(高地址)区域(假设有256 MB RAM, u-boot.bin大小为3.4 MB，那么u-boot.bin会被加载到252 MB的地址处)，还有一些其它的数据结构也会加载到内存中，如malloc，gd，stack等(参考global_data.h，这些数据一般都是从RAM顶部区域顺延往下的)。

- **bl2**

u-boot.bin，加载设备树，对必要外部设备进行初始化，加载文件系统(如FAT)，引导内核启动，并转移控制权给内核。

初始化的设备包括：

- init_sequence_f @ u-boot/common/board_f.c
- init_sequence_r @ u-boot/common/board_r.c


# 2. Build

1. Download u-boot source code:

```bash
$ wget https://ftp.denx.de/pub/u-boot/u-boot-2023.10.tar.bz2
```

2. Build:

x86_64 平台模拟 uboot：

```bash
$ tar -xf u-boot-2023.10.tar.bz2
$ cd u-boot-2023.10/
$ make sandbox_defconfig
$ make -j4
```

一个真实的例子：

```bash
$ cd $(TOP)
$ git clone https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git
$ cd trusted-firmware-a/
$ make CROSS_COMPILE=aarch64-linux-gnu- PLAT=sun50i_h616 DEBUG=1 bl31
$ export BL31=$(pwd)/build/sun50i_h616/debug/bl31.bin
$ cd $(TOP)/u-boot-2023.10/
$ make orangepi_zero2_defconfig
$ make ARCH=arm CROSS_COMPILE=aarch64-linux-gnu- -j4
```

3. Generate file:

- u-boot-spl.bin
- u-boot.bin

所有 Allwinner SoC 都会尝试在连接到第一个 MMC 控制器的 SD 卡的第 16 扇区 (8KB) 处查找启动映像。
较新的 SoC（从 2014 年的 H3 开始，包括所有 ARM64 SoC）也会查看扇区 256 (128KB) 的签名（在检查了 8KB 位置之后）。

```bash
$ cd u-boot-2023.10/

# 查看大小
$ du -sh ./spl/u-boot-spl.bin
36K     ./spl/u-boot-spl.bin
$ du -sh u-boot.bin
740K    ./u-boot.bin

# 烧写bl1到sd卡
$ sudo dd if=./spl/u-boot-spl.bin of=/dev/sdb bs=1K seek=8

# 烧写bl2到sd卡
$ sudo dd if=./u-boot.bin of=/dev/sdb bs=1K seek=80

# 烧写u-boot-dtb到sd卡
$ sudo dd if=./u0boot-dtb of=/dev/sdb bs=1K seek=900
```


# 3. Image

u-boot编译后，生成u-boot-spl.bin是bl1阶段的二进制文件，它生成在u-boot/spl目录下。

u-boot编译后，会生成u-boot，这是一个elf格式的可执行文件，可以在操作系统下运行，但不是烧录对象，u-boot通过aarch64-linux-gnu-objcopy生成u-boot.bin，这个二进制文件是烧录对象。

linux内核也有类似的情况，编译linux内核会生成vmlinuz，这个是原始的elf内核，经过objcopy加工后，生成Image文件(如从vmlinuz的78 MB精简到Image的7.5 MB)，然后Image再经过压缩生成zImage，一般烧录的就是这个压缩后的镜像文件。


# 4. Source code

以arm v7架构为例：

## 4.1 BL1

1. 设置异常向量

代码位置：u-boot/arch/arm/lib/vectors.S

```c
/*
 * 一个宏，允许在非boot0情况下或通过boot0头插入一个ARM异常向量。
 */
        .macro ARM_VECTORS
#ifdef CONFIG_ARCH_K3
	ldr     pc, _reset
#else
	b	reset
#endif
#if !CONFIG_IS_ENABLED(SYS_NO_VECTOR_TABLE)
	ldr	pc, _undefined_instruction
	ldr	pc, _software_interrupt
	ldr	pc, _prefetch_abort
	ldr	pc, _data_abort
	ldr	pc, _not_used
	ldr	pc, _irq
	ldr	pc, _fiq
#endif
	.endm
```

2. 设置CPU为SVC模式，禁用FIQ和IRQ

代码位置：u-boot/arch/arm/cpu/armv7/start.S

```c
#ifdef CONFIG_ARMV7_LPAE
/*
 * 检查 Hypervisor 支持
 */
	mrc	p15, 0, r0, c0, c1, 1		@ read ID_PFR1
	and	r0, r0, #CPUID_ARM_VIRT_MASK	@ mask virtualization bits
	cmp	r0, #(1 << CPUID_ARM_VIRT_SHIFT)
	beq	switch_to_hypervisor
switch_to_hypervisor_ret:
#endif
	/*
	 * 禁用中断(FIQ和IRQ)，也将cpu设置为SVC32模式，除非已经处于HYP模式。
	 */
	mrs	r0, cpsr
	and	r1, r0, #0x1f		@ 掩码模式位
	teq	r1, #0x1a		@ 测试 HYP 模式
	bicne	r0, r0, #0x1f		@ 清除所有模式位
	orrne	r0, r0, #0x13		@ 设置 SVC 模式
	orr	r0, r0, #0xc0		@ 禁用 FIQ 和 IRQ
	msr	cpsr,r0
```

3. 设置 CP15 寄存器 (cache, MMU, TLBs)

代码位置：u-boot/arch/arm/cpu/armv7/start.S

```c
/*************************************************************************
 *
 * cpu_init_cp15
 *
 * 设置 CP15 寄存器 (cache, MMU, TLBs). 除非定义了CONFIG_SYS_ICACHE_OFF，
 * 否则I-cache是打开的。
 *
 *************************************************************************/
ENTRY(cpu_init_cp15)

#if CONFIG_IS_ENABLED(ARMV7_SET_CORTEX_SMPEN)
	/*
	 * Arm Cortex-A7 TRM表示，在“执行任何缓存或TLB维护操作”之前，必须启用此位。
	 */
	mrc	p15, 0, r0, c1, c0, 1	@ read auxilary control register
	orr	r0, r0, #1 << 6		@ set SMP bit to enable coherency
	mcr	p15, 0, r0, c1, c0, 1	@ write auxilary control register
#endif

	/*
	 * 无效 L1 I/D
	 */
	mov	r0, #0			@ set up for MCR
	mcr	p15, 0, r0, c8, c7, 0	@ invalidate TLBs
	mcr	p15, 0, r0, c7, c5, 0	@ invalidate icache
	mcr	p15, 0, r0, c7, c5, 6	@ invalidate BP array
	dsb
	isb

	/*
	 * 禁用MMU的东西和缓存
	 */
	mrc	p15, 0, r0, c1, c0, 0
	bic	r0, r0, #0x00002000	@ clear bits 13 (--V-)
	bic	r0, r0, #0x00000007	@ clear bits 2:0 (-CAM)
	orr	r0, r0, #0x00000002	@ set bit 1 (--A-) Align
	orr	r0, r0, #0x00000800	@ set bit 11 (Z---) BTB
#if CONFIG_IS_ENABLED(SYS_ICACHE_OFF)
	bic	r0, r0, #0x00001000	@ clear bit 12 (I) I-cache
#else
	orr	r0, r0, #0x00001000	@ set bit 12 (I) I-cache
#endif
	...
ENDPROC(cpu_init_cp15)
```

4. 设置重要的寄存器和内存时序

代码位置：u-boot/arch/arm/cpu/armv7/lowlevel_init.S

```c
WEAK(lowlevel_init)
    ...
	/*
	 * Call the very early init function. This should do only the
	 * absolute bare minimum to get started. It should not:
	 *
	 * - set up DRAM
	 * - use global_data
	 * - clear BSS
	 * - try to start a console
	 *
	 * For boards with SPL this should be empty since SPL can do all of
	 * this init in the SPL board_init_f() function which is called
	 * immediately after this.
	 */
	bl	s_init
	pop	{ip, pc}
ENDPROC(lowlevel_init)
```


## 4.2 BL2

1. 设置调用board_init_f()的初始环境

堆栈、GD结构和不可用的BSS

代码位置：u-boot/arch/arm/lib/crt0.S


2. 调用 board_init_f() 准备硬件以从系统 RAM 执行

arch_cpu_init, board_early_init_f, timer_init, env_init, init_baud_rate, serial_init等。

代码位置：u-boot/common/board_f.c


3. 设置中间环境，其中堆栈和GD是由board_init_f()在系统RAM中分配的

代码位置：u-boot/arch/arm/lib/crt0.S

```c
/*
 * crt0序列的入口点
 */

ENTRY(_main)

/* 在初始化C运行环境之前调用arch_very_early_init。 */
#if CONFIG_IS_ENABLED(ARCH_VERY_EARLY_INIT)
	bl	arch_very_early_init
#endif

/*
 * 设置初始C运行环境并调用board_init_f(0).
 */

#if defined(CONFIG_TPL_BUILD) && defined(CONFIG_TPL_NEEDS_SEPARATE_STACK)
	ldr	r0, =(CONFIG_TPL_STACK)
#elif defined(CONFIG_SPL_BUILD) && defined(CONFIG_SPL_STACK)
	ldr	r0, =(CONFIG_SPL_STACK)
#else
	ldr	r0, =(SYS_INIT_SP_ADDR)
#endif
	bic	r0, r0, #7	/* 8 字节对齐以实现 ABI 合规性 */
	mov	sp, r0
	bl	board_init_f_alloc_reserve
	mov	sp, r0
	/* 在这里设置 gd，在任何 C 代码之外 */
	mov	r9, r0
	bl	board_init_f_init_reserve

#if defined(CONFIG_DEBUG_UART) && CONFIG_IS_ENABLED(SERIAL)
	bl	debug_uart_init
#endif

#if defined(CONFIG_SPL_BUILD) && defined(CONFIG_SPL_EARLY_BSS)
	CLEAR_BSS
#endif

	mov	r0, #0
	bl	board_init_f
    ...
ENDPROC(_main)
```

调用 board_init_f() 准备硬件以从系统 RAM 执行:

arch_cpu_init, board_early_init_f, timer_init, env_init, init_baud_rate, serial_init等。

代码位置：u-boot/common/board_f.c

```c
void board_init_f(ulong boot_flags)
{
	gd->flags = boot_flags;
	gd->have_console = 0;

	if (initcall_run_list(init_sequence_f))
		hang();

#if !defined(CONFIG_ARM) && !defined(CONFIG_SANDBOX) && \
		!defined(CONFIG_EFI_APP) && !CONFIG_IS_ENABLED(X86_64) && \
		!defined(CONFIG_ARC)
	/* NOTREACHED - jump_to_copy() does not return */
	hang();
#endif
}
```

代码位置：u-boot/include/initcall.h

```c
/*
 * 打开调试开关。在包含文件的顶部添加#define DEBUG。
 *
 * 要查找符号，可在u-boot.map上使用grep
 */
static inline int initcall_run_list(const init_fnc_t init_sequence[])
{
	const init_fnc_t *init_fnc_ptr;

	for (init_fnc_ptr = init_sequence; *init_fnc_ptr; ++init_fnc_ptr) {
		unsigned long reloc_ofs = 0;
		int ret;

		/*
		 * 沙盒是由操作系统重新定位的，因此重新定位的地址总会出现符号。
		 */
		if (IS_ENABLED(CONFIG_SANDBOX) || (gd->flags & GD_FLG_RELOC))
			reloc_ofs = gd->reloc_off;
#ifdef CONFIG_EFI_APP
		reloc_ofs = (unsigned long)image_base;
#endif
		if (reloc_ofs)
			debug("initcall: %p (relocated to %p)\n",
					(char *)*init_fnc_ptr - reloc_ofs,
					(char *)*init_fnc_ptr);
		else
			debug("initcall: %p\n", (char *)*init_fnc_ptr - reloc_ofs);

		ret = (*init_fnc_ptr)();
		if (ret) {
			printf("initcall sequence %p failed at call %p (err=%d)\n",
			       init_sequence,
			       (char *)*init_fnc_ptr - reloc_ofs, ret);
			return -1;
		}
	}
	return 0;
}
```

4. 调用 relocate_code() 将 U-Boot 重新定位到 board_init_f() 计算出的目标位置

代码位置：u-boot/arch/arm/lib/crt0.S

```c
/*
 * 设置中间环境（new sp 和 gd）并调用 relocate_code(addr_moni)。 
 * 这里的技巧是，我们将返回“这里”，但会搬迁。
 */

	ldr	r0, [r9, #GD_START_ADDR_SP]	/* sp = gd->start_addr_sp */
	bic	r0, r0, #7	/* 8 字节对齐以实现 ABI 合规性 */
	mov	sp, r0
	ldr	r9, [r9, #GD_NEW_GD]		/* r9 <- gd->new_gd */

	adr	lr, here
    ...
	ldr	r0, [r9, #GD_RELOC_OFF]		/* r0 = gd->reloc_off */
	add	lr, lr, r0
    ...
	ldr	r0, [r9, #GD_RELOCADDR]		/* r0 = gd->relocaddr */
	b	relocate_code
```

u-boot/arch/arm/lib/relocate.S

```c
ENTRY(relocate_code)
relocate_base:
	adr	r3, relocate_base
	ldr	r1, _image_copy_start_ofs
	add	r1, r3			/* r1 <- Run &__image_copy_start */
	subs	r4, r0, r1		/* r4 <- Run to copy offset      */
	beq	relocate_done		/* skip relocation               */
	ldr	r1, _image_copy_start_ofs
	add	r1, r3			/* r1 <- Run &__image_copy_start */
	ldr	r2, _image_copy_end_ofs
	add	r2, r3			/* r2 <- Run &__image_copy_end   */
copy_loop:
	ldmia	r1!, {r10-r11}		/* copy from source address [r1] */
	stmia	r0!, {r10-r11}		/* copy to   target address [r0] */
	cmp	r1, r2			/* until source end address [r2] */
	blo	copy_loop

	/*
	 * fix .rel.dyn relocations
	 */
	ldr	r1, _rel_dyn_start_ofs
	add	r2, r1, r3		/* r2 <- Run &__rel_dyn_start */
	ldr	r1, _rel_dyn_end_ofs
	add	r3, r1, r3		/* r3 <- Run &__rel_dyn_end */
fixloop:
	ldmia	r2!, {r0-r1}		/* (r0,r1) <- (SRC location,fixup) */
	and	r1, r1, #0xff
	cmp	r1, #R_ARM_RELATIVE
	bne	fixnext

	/* relative fix: increase location by offset */
	add	r0, r0, r4
	ldr	r1, [r0]
	add	r1, r1, r4
	str	r1, [r0]
fixnext:
	cmp	r2, r3
	blo	fixloop

relocate_done:
    ...
ENDPROC(relocate_code)
```

5. 设置调用 board_init_r() 的最终环境

代码位置：u-boot/arch/arm/lib/crt0.S

```c
#if ! defined(CONFIG_SPL_BUILD)
    ...
/* Set up final (full) environment */

	bl	c_runtime_cpu_setup	/* we still call old routine here */
#endif
#if !defined(CONFIG_SPL_BUILD) || CONFIG_IS_ENABLED(FRAMEWORK)

#if !defined(CONFIG_SPL_BUILD) || !defined(CONFIG_SPL_EARLY_BSS)
	CLEAR_BSS
#endif

# ifdef CONFIG_SPL_BUILD
	/* Use a DRAM stack for the rest of SPL, if requested */
	bl	spl_relocate_stack_gd
	cmp	r0, #0
	movne	sp, r0
	movne	r9, r0
# endif

#if ! defined(CONFIG_SPL_BUILD)
	bl coloured_LED_init
	bl red_led_on
#endif
	/* call board_init_r(gd_t *id, ulong dest_addr) */
	mov     r0, r9                  /* gd_t */
	ldr	r1, [r9, #GD_RELOCADDR]	/* dest_addr */
	/* call board_init_r */
#if CONFIG_IS_ENABLED(SYS_THUMB_BUILD)
	ldr	lr, =board_init_r	/* this is auto-relocated! */
	bx	lr
#else
	ldr	pc, =board_init_r	/* this is auto-relocated! */
#endif
	/* we should not return here. */
#endif
```

6. 分支到board_init_r()初始化GD结构和各种外设，执行无限的main_loop()函数

initr_env, audio_add_devices, console_init_r, interrupt_init, initr_net, main_loop等。

代码位置：u-boot/common/board_r.c

u-boot/common/board_r.c

```c
void board_init_r(gd_t *new_gd, ulong dest_addr)
{
	/*
	 * The pre-relocation drivers may be using memory that has now gone
	 * away. Mark serial as unavailable - this will fall back to the debug
	 * UART if available.
	 *
	 * Do the same with log drivers since the memory may not be available.
	 */
	gd->flags &= ~(GD_FLG_SERIAL_READY | GD_FLG_LOG_READY);

	/*
	 * Set up the new global data pointer. So far only x86 does this
	 * here.
	 * TODO(sjg@chromium.org): Consider doing this for all archs, or
	 * dropping the new_gd parameter.
	 */
	if (CONFIG_IS_ENABLED(X86_64) && !IS_ENABLED(CONFIG_EFI_APP))
		arch_setup_gd(new_gd);

#if !defined(CONFIG_X86) && !defined(CONFIG_ARM) && !defined(CONFIG_ARM64)
	gd = new_gd;
#endif
	gd->flags &= ~GD_FLG_LOG_READY;

	if (IS_ENABLED(CONFIG_NEEDS_MANUAL_RELOC)) {
		for (int i = 0; i < ARRAY_SIZE(init_sequence_r); i++)
			MANUAL_RELOC(init_sequence_r[i]);
	}

	if (initcall_run_list(init_sequence_r))
		hang();

	/* NOTREACHED - run_main_loop() does not return */
	hang();
}
```

u-boot/include/initcall.h

```c
static inline int initcall_run_list(const init_fnc_t init_sequence[])
{
	const init_fnc_t *init_fnc_ptr;

	for (init_fnc_ptr = init_sequence; *init_fnc_ptr; ++init_fnc_ptr) {
		unsigned long reloc_ofs = 0;
		int ret;

		/*
		 * Sandbox is relocated by the OS, so symbols always appear at
		 * the relocated address.
		 */
		if (IS_ENABLED(CONFIG_SANDBOX) || (gd->flags & GD_FLG_RELOC))
			reloc_ofs = gd->reloc_off;
#ifdef CONFIG_EFI_APP
		reloc_ofs = (unsigned long)image_base;
#endif
		if (reloc_ofs)
			debug("initcall: %p (relocated to %p)\n",
					(char *)*init_fnc_ptr - reloc_ofs,
					(char *)*init_fnc_ptr);
		else
			debug("initcall: %p\n", (char *)*init_fnc_ptr - reloc_ofs);

		ret = (*init_fnc_ptr)();
		if (ret) {
			printf("initcall sequence %p failed at call %p (err=%d)\n",
			       init_sequence,
			       (char *)*init_fnc_ptr - reloc_ofs, ret);
			return -1;
		}
	}
	return 0;
}
```

u-boot/common/main.c

```c
/* 在U-Boot初始化并准备好处理命令后，我们来到这里。 */
void main_loop(void)
{
	const char *s;

	bootstage_mark_name(BOOTSTAGE_ID_MAIN_LOOP, "main_loop");

	if (IS_ENABLED(CONFIG_VERSION_VARIABLE))
		env_set("ver", version_string);  /* set version variable */

	cli_init();

	if (IS_ENABLED(CONFIG_USE_PREBOOT))
		run_preboot_environment_command();

	if (IS_ENABLED(CONFIG_UPDATE_TFTP))
		update_tftp(0UL, NULL, NULL);

	if (IS_ENABLED(CONFIG_EFI_CAPSULE_ON_DISK_EARLY)) {
		/* efi_init_early() already called */
		if (efi_init_obj_list() == EFI_SUCCESS)
			efi_launch_capsules();
	}

	s = bootdelay_process();
	if (cli_process_fdt(&s))
		cli_secure_boot_cmd(s);

	autoboot_command(s);

	cli_loop();
	panic("No CLI available");
}
```

u-boot/common/autoboot.c

```c
static int abortboot(int bootdelay)
{
	int abort = 0;

	if (bootdelay >= 0) {
		if (autoboot_keyed())
			abort = abortboot_key_sequence(bootdelay);
		else
			abort = abortboot_single_key(bootdelay);
	}

	if (IS_ENABLED(CONFIG_SILENT_CONSOLE) && abort)
		gd->flags &= ~GD_FLG_SILENT;

	return abort;
}

void autoboot_command(const char *s)
{
	debug("### main_loop: bootcmd=\"%s\"\n", s ? s : "<UNDEFINED>");

	if (s && (stored_bootdelay == -2 ||
		 (stored_bootdelay != -1 && !abortboot(stored_bootdelay)))) {
		bool lock;
		int prev;

		lock = autoboot_keyed() &&
			!IS_ENABLED(CONFIG_AUTOBOOT_KEYED_CTRLC);
		if (lock)
			prev = disable_ctrlc(1); /* disable Ctrl-C checking */

		run_command_list(s, -1, 0);

		if (lock)
			disable_ctrlc(prev);	/* restore Ctrl-C checking */
	}

	if (IS_ENABLED(CONFIG_AUTOBOOT_USE_MENUKEY) &&
	    menukey == AUTOBOOT_MENUKEY) {
		s = env_get("menucmd");
		if (s)
			run_command_list(s, -1, 0);
	}
}
```


# 5. Loading kernel

## 5.1 BL2 overview

- 初始化本阶段要使用到的硬件设备；
- 检测系统内存映射；
- 将内核映象从Flash上读到SDRAM空间中；
- 为内核设置启动参数；

Bootloader与内核的交互是单向的， Bootloader将各类参数传给内核。由于它们不能同时行，传递办法只有一个：Bootloader将参数放在某个约定的地方之后，再启动内核，内核启动后从这个地方获得参数。

除了约定好参数存放的地址外，还要规定参数的结构。Linu2.4x以后的内核都期望以标记列表（ tagged_list）的形式来传递启动参数。标记，就是一种数据结构；标记列表，就是挨着存放的多个标记。标记列表以标记 ATAG CORE开始，以标记 ATAG NONE结束。

标记的数据结构为tag，它由一个 tag_header结构和一个联合(union)组成。 tag_ header结构表小标记的类型及长度，比如是表示内存还是表示命令行参数等。对于不同类型的标记使用不同的联合(union)，比如表示内存时使用 tag_mem32，表示命令行时使用 tag_cmdline。

bootloader与内核约定的参数地址，设置内存的起始地址和大小，指定根文件系统在那个分区，系统启动后执行的第一个程序linuxrc，控制台ttySAC0等。


## 5.2 u-boot args to kernel

R0=0；
R1=机器类型ID；对于ARM结构的CPU，其机器类型ID可以参见 linux/arch/arm/tools/mach-types；
R2=启动参数标记列表在RAM中起始基地址。这些参数有内存的起始地址、内存大小、Linux内核启动后挂载文件系统的方式等信息。

标记如下：

- 设置开始标记ATAG_CORE；
- 设置内存标记；
- 设置命令行参数标记；
- 设置结束标记；


## 5.3 jump to kernel

在uboot中可以使用go和bootm来跳转到内核，两者的区别是：

- go命令仅仅修改pc的值到指定地址；
- 它在修改pc的值到指定地址之前，会设置传递给Linux内核的参数；

这里介绍以下bootm：

- 把内核拷贝到合适的地方；
- 把参数给内核准备好；
- 引导内核；

它会调用 do_bootm_linux @ u-boot/arch/arm/lib/bootm.c

```c
int do_bootm_linux(int flag, int argc, char *const argv[],
		   struct bootm_headers *images)
{
	/* No need for those on ARM */
	if (flag & BOOTM_STATE_OS_BD_T || flag & BOOTM_STATE_OS_CMDLINE)
		return -1;

	if (flag & BOOTM_STATE_OS_PREP) {
		boot_prep_linux(images);
		return 0;
	}

	if (flag & (BOOTM_STATE_OS_GO | BOOTM_STATE_OS_FAKE_GO)) {
		boot_jump_linux(images, flag);
		return 0;
	}

	boot_prep_linux(images);
	boot_jump_linux(images, flag);
	return 0;
}
```

会先后执行boot_prep_linux和boot_jump_linux，前者获取bootargs的参数，保存成tag到内存的指定位置，后者则是启动linux内核，它调用的是：

```c
kernel_entry(0, machid, r2);
```

Note: arm64使用booti。


## 5.4 Example

```bash
# 设置uboot启动后自动引导系统
setenv bootcmd "ext4load mmc 0:1 0x46000000 zImage; ext4load mmc 0:1 0x47000000 orangepi_lite.dtb; bootz 0x46000000 0:0 0x47000000"

# 设置内核启动参数.这里需要注意sd卡在uboot上是第0个设备，但在linux内核里变成了第2个设备(可在内核的log看到).
setenv bootargs "root=/dev/mmcblk2p1 rw console=ttyS0,115200n8"
```

以三星一款 Armv7 的 SoC 为例，外部 DRAM 的起始地址一般是0x40000000，所以这里的参数都是以这个为基础，通过计算可以得知从0x00000000到0x40000000是1GB的地址空间，这个空间里是以下对象占用：

iROM - 0x00000000 ~ 0x00010000
iRAM - 0x02020000 ~ 0x02060000

还有SRP、SFR、SMC等对象占用。


## 5.5 DTB

1.  这个设备树和内核里面的设备树一样的，是一个东西。
2.  扁平设备树的出现，让一个二进制文件支持多种设备成为了可能。
3.  uboot里面，在设备的deconfig里通过CONFIG_DEFAULT_DEVICE_TREE="xxxxx"来指定。
4.  如果设置CONFIG_OF_EMBED，那么uboot会把设备树编译进镜像内。
5.  如果设置CONFIG_OF_SEPARATE，则直接用   cat u-boot.bin u-boot.dtb >image.bin这样的方式把设备树放在uboot镜像后面。
6.  fdt 源文件是dts,通过dtc编译后为dtb文件，对设备数的二进制和文件源文件两种格式可以用fdtdump或者ftdget/fdtput进行读写。
7.  实际使用过程中，一般都是拷贝已经有的模板（相同MCU）的dts来编辑生成自己的dts。
8.  uboot预配置文件路径 configs里  ，dts路径 arch/<arch>/dts/
9.  添加自己的dts文件可以拷贝已经有的相同MCU 板子的dts文件，修改内容后，并在 arch/<arch>/dts/Makefile里添加上，一般搜索到你拷贝的那个dts文件名的下一行就行。

参考了 buildroot 中一些开发板的 u-boot 使用 u-boot.dtb 的解决方案：

orangepi-r1 使用 u-boot-sunxi-with-spl.bin，这个u-boot文件把spl、u-boot、dtb封装到了一起；
rock64 使用 u-boot-tpl-spl.img 和 u-boot.itb，其中 u-boot.itb 则是把 u-boot.bin 和 u-boot.dtb 封装到了一起(还有一些其它的，可以参考its文件)。


# Reference

[u-boot docs](https://docs.u-boot.org/en/latest/board/allwinner/sunxi.html)

