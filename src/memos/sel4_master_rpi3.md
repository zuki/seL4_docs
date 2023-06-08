# master 64bit QEMU

- テスト成功。[ログファイル](simulate.log)
- `CONFIG_HAVE_TIMER`が定義されないので `init_timer()｀が実行されず、
  実機でのエラーが発生しない (`apps/sel4test-driver/gen_config/sel4test-driver/gen_config.h`)

# master 64bit raspi3b+

- `CONFIG_HAVE_TIMER`が定義される
- `init_timer()`でエラー発生

実行時に以下のエラーが発生する。これはrpi3にはない割り込みコントローラの
設定がrpi3でも行われているためである。この変更は、pull request [RPi3/4 libplatsupport fixes and updates](https://github.com/seL4/util_libs/pull/134)で
提案され、commit [libplatsupport,rpi3/4: use only system timer](https://github.com/seL4/util_libs/commit/7733230693740ca2a7d7d770718b95a2e6263375)で
マージされたもの。メーリングリストには
[このエラーの報告はある](https://sel4.discourse.group/t/latest-sel4-tests-fail-on-raspberry-pi-3/662)。

```
bcm_system_timer_init@system_timer.c:203 path: /soc/timer@7e003000, reg_choice: 0, irq_choice: 0
helper_fdt_alloc_simple@ltimer.h:76 vmap: 0x10064000, pmem(type: 2, base_addr: 0x1056976896, len: 0x4096)
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: arm,gic-400
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: arm,cortex-a9-gic
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: arm,cortex-a15-gic
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: arm,gic-v3
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: ti,omap3-intc
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: ti,am33xx-intc
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: nvidia,tegra210-ictlr
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: nvidia,tegra124-ictlr
fdt_node_check_compatible@fdt_ro.c:834 prop: brcm,bcm2836-l1-intc, compatible: nvidia,tegra30-ictlr

ps_fdt_walk_irqs@fdt.c:229 Could not find a parser for this particular interrupr    # [5]
helper_fdt_alloc_simple@ltimer.h:78 Simple FDT helper failed to register irqs ()    # [4]
bcm_system_timer_init@system_timer.c:214 Failed to allocate with fdt                # [3]
ltimer_default_init@ltimer.c:112 system timer initialisation failed                 # [2]
init_timer@main.c:181 [Cond failed: error]                                          # [1]
        Failed to setup the timers
seL4 root server abort()ed
Debug halt syscall from user thread 0xffffff8007fcc400 "sel4test-driver"
halting...
Kernel entry via Unknown syscall, word: 1
```

[1]	`sel4test/apps/sel4test-driver/src/main.c`

```c
    174 static void init_timer(void)
    175 {
...
    180         error = ltimer_default_init(&env.ltimer, env.ops, NULL, NULL);
    181         ZF_LOGF_IF(error, "Failed to setup the timers");
```

[2]	`util_libs/libplatsupport/src/mach/bcm/ltimer.c`

```c
     79 int ltimer_default_init(ltimer_t *ltimer,
     80                         ps_io_ops_t ops,
     81                         ltimer_callback_fn_t callback,
     82                         void *callback_token)
     83 {
...
    106     rc = bcm_system_timer_init(&timer->system_timer,
    107                                ops,
    108                                callback,
    109                                callback_token,
    110                                config);
    111     if (rc) {
    112         ZF_LOGE("system timer initialisation failed");
    113         ps_free(&timer->ops.malloc_ops, sizeof(bcm_system_timer_t), timer);
    114     }
```

[3] `util_libs/libplatsupport/src/mach/bcm/system_timer.c`

```c
    187 int bcm_system_timer_init(bcm_system_timer_t *timer,
    188                           ps_io_ops_t ops,
    189                           ltimer_callback_fn_t callback,
    190                           void *callback_token,
    191                           bcm_system_timer_config_t config)
    192 {
...
    203     int rc = helper_fdt_alloc_simple(&timer->ops,
    204                                      config.fdt_path,
    205                                      config.fdt_reg_choice,
    206                                      config.fdt_irq_choice,
    207                                      (void *) &timer->registers,
    208                                      &timer->pmem,
    209                                      &timer->irq,
    210                                      bcm_system_timer_handle_irq,
    211                                      timer);
    212
    213     if (rc) {
    214         ZF_LOGE("Failed to allocate with fdt");
    215         bcm_system_timer_destroy(timer);
    216         return rc;
    217     }
```

[4] `util_libs/libplatsupport/src/ltimer.h`

```c
     44 static int helper_fdt_alloc_simple(
     45     ps_io_ops_t *ops, char *fdt_path,
     46     unsigned reg_choice, unsigned irq_choice,
     47     void **vmap, pmem_region_t *pmem, irq_id_t *irq_id,
     48     irq_callback_fn_t handler, void *handler_token
     49 )
...
     76     temp_irq_id = ps_fdt_index_register_irq(ops, cookie, irq_choice, handler, handler_token);
     77     if (temp_irq_id <= PS_INVALID_IRQ_ID) {
     78         ZF_LOGE("Simple FDT helper failed to register irqs (%d)", temp_irq_id);
     79         return temp_irq_id;
     80     }
```

[5]	`util_libs/libplatsupport/src/fdt.c`

```c
    315 irq_id_t ps_fdt_index_register_irq(ps_io_ops_t *io_ops, ps_fdt_cookie_t *cookie, unsigned offset,
    316                                    irq_callback_fn_t irq_callback, void *irq_callback_data)
    317 {
...
    333     int error = ps_fdt_walk_irqs(&io_ops->io_fdt, cookie, irq_index_help    333 er_walker, &token);
    334     if (error) {
    335         assert(error <= PS_INVALID_IRQ_ID);
    336         return error;
    337     }
...
    153 int ps_fdt_walk_irqs(ps_io_fdt_t *io_fdt, ps_fdt_cookie_t *cookie, irq_walk_cb_fn_t callback, void *token)
    154 {
...
            // ここではgicを探しているが、rpi3では登録されていないため、これがNULLになる
    227     ps_irqchip_t **irqchip = find_compatible_irq_controller(dtb_blob, root_intr_controller_offset);
    228     if (irqchip == NULL) {
    229         ZF_LOGE("Could not find a parser for this particular interrupt controller");
    230         return -ENOENT;
    231     }
...
    140 static inline ps_irqchip_t **find_compatible_irq_controller(char *dtb_blob, int intr_controller_offset)
    141 {
    142     for (ps_irqchip_t **irqchip = __start__ps_irqchips; irqchip < __stop__ps_irqchips; irqchip++) {
    143         for (char **compatible_str = (*irqchip)->compatible_list; *compatible_str != NULL; compatible_str++) {
    144             if (fdt_node_check_compatible(dtb_blob, intr_controller_offset, *compatible_str) == 0) {
    145                 return irqchip;
    146             }
    147         }
    148     }
    149
    150     return NULL;
    151 }
```

- v12.1.0では`ltimer_default_init()`が2つ定義されている。
	1. `plat/bcm2837/ltimer.c`
	2. `arch/arm/generic_ltimer.c`
- 実際に使用されているのは`plat/bcm2837/ltimer.c`の関数
- `util_libs/libplatsupport/src/mach/bcm/ltimer.c`は存在しない

## v12.1.0の`plat/bcm2837/ltimer.c`の`ltimer_default_init()`

```c
int ltimer_default_init(ltimer_t *ltimer, ps_io_ops_t ops, ltimer_callback_fn_t callback, void *callback_token)
{
// これはmasterのint create_ltimer_simple()の後半部分に相当する（ただし、masterではNULLを設定している。bcm2837固有のデータか？）
    int error = ltimer_default_describe(ltimer, ops);
    if (error) {
        return error;
    }
// ここから masterの int create_ltimer_simple() の前半部分に相当する
    ltimer->get_time = get_time;
    ltimer->get_resolution = get_resolution;
    ltimer->set_timeout = set_timeout;
    ltimer->reset = reset;
    ltimer->destroy = destroy;

    error = ps_calloc(&ops.malloc_ops, 1, sizeof(spt_ltimer_t), &ltimer->data);
    if (error) {
        return error;
    }
    assert(ltimer->data != NULL);
// ここまで

    spt_ltimer_t *spt_ltimer = ltimer->data;
    spt_ltimer->ops = ops;

    spt_ltimer->user_callback = callback;
    spt_ltimer->user_callback_token = callback_token;
    for (int i = 0; i < NUM_TIMERS; i++) {
        spt_ltimer->timer_irq_ids[i] = PS_INVALID_IRQ_ID;
    }

    for (int i = 0; i < NUM_TIMERS; i++) {
        /* map the registers we need */
        spt_ltimer->timer_vaddrs[i] = ps_pmem_map(&ops, pmems[i], false, PS_MEM_NORMAL);
        if (spt_ltimer->timer_vaddrs[i] == NULL) {
            destroy(ltimer->data);
            return ENOMEM;
        }

        /* register the IRQs we need */
        error = ps_calloc(&ops.malloc_ops, 1, sizeof(ps_irq_t), (void **) &spt_ltimer->callback_datas[i].irq);
        if (error) {
            destroy(ltimer->data);
            return error;
        }
        spt_ltimer->callback_datas[i].ltimer = ltimer;
        spt_ltimer->callback_datas[i].irq_handler = handle_irq;
        error = get_nth_irq(ltimer->data, i, spt_ltimer->callback_datas[i].irq);
        if (error) {
            destroy(ltimer->data);
            return error;
        }
        spt_ltimer->timer_irq_ids[i] = ps_irq_register(&ops.irq_ops, *spt_ltimer->callback_datas[i].irq,
                                                       handle_irq_wrapper, &spt_ltimer->callback_datas[i]);
        if (spt_ltimer->timer_irq_ids[i] < 0) {
            destroy(ltimer->data);
            return EIO;
        }
    }
    /* setup spt */
    spt_config_t spt_config = {
        .vaddr = spt_ltimer->timer_vaddrs[SP804_TIMER],
    };

    spt_init(&spt_ltimer->spt, spt_config);
    spt_start(&spt_ltimer->spt);

    /* Setup system timer */
    system_timer_config_t system_config = {
        .vaddr = spt_ltimer->timer_vaddrs[SYSTEM_TIMER],
    };

    system_timer_init(&spt_ltimer->system, system_config);

    return 0;
}
```


## v12.1.0の`arch/arm/generic_ltimer.c`の`ltimer_default_init()`

```c
int ltimer_default_init(ltimer_t *ltimer, ps_io_ops_t ops, ltimer_callback_fn_t callback, void *callback_data)
{
    if (ltimer == NULL) {
        ZF_LOGE("ltimer cannot be NULL");
        return EINVAL;
    }

    if (!config_set(CONFIG_EXPORT_PCNT_USER)) {
        ZF_LOGE("Generic timer not exported!");
        return ENXIO;
    }

    ltimer_default_describe(ltimer, ops);
    ltimer->get_time = get_time;
    ltimer->get_resolution = get_resolution;
    ltimer->set_timeout = set_timeout;
    ltimer->reset = reset;
    ltimer->destroy = destroy;

    int error = ps_calloc(&ops.malloc_ops, 1, sizeof(generic_ltimer_t), &ltimer->data);
    if (error) {
        return error;
    }
    assert(ltimer->data != NULL);
    generic_ltimer_t *generic_ltimer = ltimer->data;

    generic_ltimer->ops = ops;
    generic_ltimer->user_callback = callback;
    generic_ltimer->user_callback_token = callback_data;

    generic_ltimer->freq = generic_timer_get_freq();
    if (generic_ltimer->freq == 0) {
        ZF_LOGE("Couldn't read timer frequency");
        return ENXIO;
    }

    generic_timer_set_compare(UINT64_MAX);
    generic_timer_enable();

    /* register the IRQ we need */
    error = ps_calloc(&ops.malloc_ops, 1, sizeof(ps_irq_t), (void **) &generic_ltimer->callback_data.irq);
    if (error) {
        destroy(ltimer->data);
        return error;
    }
    generic_ltimer->callback_data.ltimer = ltimer;
    generic_ltimer->callback_data.irq_handler = handle_irq;
    error = get_nth_irq(ltimer->data, 0, generic_ltimer->callback_data.irq);
    if (error) {
        destroy(ltimer->data);
        return error;
    }
    generic_ltimer->timer_irq_id = ps_irq_register(&ops.irq_ops, *generic_ltimer->callback_data.irq,
                                                   handle_irq_wrapper, &generic_ltimer->callback_data);
    if (generic_ltimer->timer_irq_id < 0) {
        destroy(ltimer->data);
        return EIO;
    }

    return 0;
}
```


## masterの`sel4/projects/util_libs/libplatsupport/src`ファイル

```bash
$ ls arch/arm/
clock.c  delay.c   generic_ltimer.c  gpio_utils.c   i2c.c
clock.h  dma330.c  generic_timer.c   i2c_bitbang.c  irqchip/
$ ls mach/bcm
bcm_uart.c  ltimer.c   mailbox_util.c  serial.c  system_timer.c
chardev.c   mailbox.c  pl011_uart.c    serial.h
$ ls plat/bcm2837
clock.c  gpio.c
$ ls kernel/src/drivers/serial/
bcm2835-aux-uart.c  imx.c            msm-uartdm.c         xuartps.c
config.cmake        imx-lpuart.c     pl011.c
exynos4210-uart.c   meson-gx-uart.c  tegra_omap3_dwapb.c
```

## v12.1.0の`sel4/projects/util_libs/libplatsupport/src`ファイル

```bash
$ ls arch/arm/
clock.c  delay.c   generic_ltimer.c  gpio_utils.c   i2c.c
clock.h  dma330.c  generic_timer.c   i2c_bitbang.c  irqchip/
$ ls mach
exynos  imx  nvidia  omap  zynq
$ ls plat/bcm2837
chardev.c  gpio.c    mailbox.c       serial.c  spt.c
clock.c    ltimer.c  mailbox_util.c  serial.h  system_timer.c
$ ls kernel/src/drivers/serial
bcm2835-aux-uart.c  imx.c            msm-uartdm.c         xuartps.c
config.cmake        imx-lpuart.c     pl011.c
exynos4210-uart.c   meson-gx-uart.c  tegra_omap3_dwapb.c
```

# 解決策

- bcm2837/ltimer.cを何処かに置き、rpi3の場合はこれを使うようにする
- util_libs/libplatsupport/CMakeLists.txt を変更する

# masterの関連部分

## `util_libs/libplatsupport/CMakeLists.txt`の関連部分

- KernelArch = `arm`
- KernelSel4Arch = `aarch64`
- KernelPlatformRpi3 = `ON`
- KernelPlatform = `bcm2837`
- LibPlatSupportMach = `bcm`

```
set(LibPlatSupportMach "")
if(KernelPlatformRpi3 OR KernelPlatformRpi4)
    set(LibPlatSupportMach "bcm")
elseif(NOT ${KernelArmMach} STREQUAL "")
    # falling back to kernel settings is done to keep legacy compatibility
    set(LibPlatSupportMach "${KernelArmMach}")
endif()

// file(GLOB <variable> [RELATIVE <path>] [<globbing_expressions>]...)
file(
    GLOB
        deps
        src/plat/${KernelPlatform}/*.c          // clock.c  gpio.c
        src/*.c                                 // fdt.c  io.c  local_time_manager.c  serial.c
                                                // tqueue.c
        src/plat/${KernelPlatform}/acpi/*.c     // なし
)

if(NOT ${LibPlatSupportMach} STREQUAL "")
    file(GLOB lib_deps "src/mach/${LibPlatSupportMach}/*.c")
                                                // bcm_uart.c  ltimer.c mailbox_util.c serial.c
                                                // system_timer.c chardev.c   mailbox.c
                                                // pl011_uart.c serial.h
    list(APPEND deps ${lib_deps})
endif()

if(${KernelArch} STREQUAL "arm")
    list(APPEND deps src/arch/arm/clock.c)
    list(APPEND deps src/arch/arm/delay.c)
    list(APPEND deps src/arch/arm/dma330.c)
    list(APPEND deps src/arch/arm/i2c.c)
    list(APPEND deps src/arch/arm/i2c_bitbang.c)
    list(APPEND deps src/arch/arm/generic_timer.c)
    list(APPEND deps src/arch/arm/irqchip/gic.c)
    list(APPEND deps src/arch/arm/irqchip/tegra.c)
    list(APPEND deps src/arch/arm/irqchip/gicv3.c)
    list(APPEND deps src/arch/arm/irqchip/omap3.c)
    # Link the IRQ chip parser modules
    list(
        APPEND
            irqchip_modules
            "-Wl,--undefined=arm_gic_ptr,--undefined=tegra_ictlr_ptr,--undefined=arm_gicv3_ptr,\
--undefined=fsl_avic_ptr,--undefined=ti_omap3_ptr"
    )
...
if(NOT "${LibPlatSupportMach}" STREQUAL "")
    target_include_directories(platsupport PUBLIC mach_include/${LibPlatSupportMach})
                                                            // mach_include/bcm
endif()

set(_inc_folder_KernelSel4Arch "${KernelSel4Arch}")

target_include_directories(
    platsupport
    PUBLIC
        include
        plat_include/${KernelPlatform}                      // plat_include/bcm2837
        sel4_arch_include/${_inc_folder_KernelSel4Arch}     // sel4_arch_include/aarch64
        arch_include/${KernelArch}                          // arch_include/arm
)

target_link_libraries(
    platsupport
    muslc
    utils
    fdt
    sel4_autoconf
    platsupport_Config
    ${irqchip_modules}
)
```

## `buildbuild.ninja`の関連部分

```bash
build apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/mach/bcm/ltimer.c.obj: C_COMPILER__platsupport_Debug /home/vagrant/sel4/projects/util_libs/libplatsupport/src/mach/bcm/ltimer.c || cmake_object_order_depends_target_platsupport
  DEP_FILE = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/mach/bcm/ltimer.c.obj.d
  FLAGS = -march=armv8-a+crc  -D__KERNEL_64__ -g -nostdinc -fno-pic -fno-pie -fno-stack-protector -fno-asynchronous-unwind-tables -ftls-model=local-exec -mstrict-align -mno-outline-atomics -std=gnu11
  INCLUDES = -I/home/vagrant/sel4/projects/util_libs/libplatsupport/mach_include/bcm -I/home/vagrant/sel4/projects/util_libs/libplatsupport/include -I/home/vagrant/sel4/projects/util_libs/libplatsupport/plat_include/bcm2837 -I/home/vagrant/sel4/projects/util_libs/libplatsupport/sel4_arch_include/aarch64 -I/home/vagrant/sel4/projects/util_libs/libplatsupport/arch_include/arm -I/home/vagrant/sel4/build-rpi3-64/apps/sel4test-driver/musllibc/build-temp/stage/include -I/home/vagrant/sel4/projects/util_libs/libutils/include -I/home/vagrant/sel4/projects/util_libs/libutils/arch_include/arm -I/home/vagrant/sel4/build-rpi3-64/apps/sel4test-driver/util_libs/libutils/gen_config -I/home/vagrant/sel4/build-rpi3-64/libsel4/autoconf -I/home/vagrant/sel4/build-rpi3-64/kernel/gen_config -I/home/vagrant/sel4/build-rpi3-64/libsel4/gen_config -I/home/vagrant/sel4/projects/util_libs/libfdt/include -I/home/vagrant/sel4/projects/util_libs/libfdt/. -I/home/vagrant/sel4/build-rpi3-64/apps/sel4test-driver/util_libs/libplatsupport/gen_config
  OBJECT_DIR = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir
  OBJECT_FILE_DIR = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/mach/bcm
  TARGET_COMPILE_PDB = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/platsupport.pdb
  TARGET_PDB = apps/sel4test-driver/util_libs/libplatsupport/libplatsupport.pdb
```

## `build/kernel/gen_config/kernel/gen_config.h`

```c
#pragma once

/* disabled: CONFIG_ARCH_AARCH32 */
#define CONFIG_ARCH_AARCH64  1
/* disabled: CONFIG_ARCH_ARM_HYP */
#define CONFIG_SEL4_ARCH  aarch64
#define CONFIG_ARCH_ARM  1
#define CONFIG_ARCH  arm
#define CONFIG_WORD_SIZE  64
#define CONFIG_ARM_PLAT  rpi3
#define CONFIG_ARM_HIKEY_OUTSTANDING_PREFETCHERS  0
#define CONFIG_ARM_HIKEY_PREFETCHER_STRIDE  0
#define CONFIG_ARM_HIKEY_PREFETCHER_NPFSTRM  0
#define CONFIG_USER_TOP  0xa0000000
#define CONFIG_PLAT_BCM2837  1
#define CONFIG_PLAT  bcm2837
#define CONFIG_ARM_CORTEX_A53  1
#define CONFIG_ARCH_ARM_V8A  1
#define CONFIG_ARM_MACH  /* empty */
#define CONFIG_ARM_PA_SIZE_BITS_40  1
#define CONFIG_ARM_ICACHE_VIPT  1
#define CONFIG_AARCH64_USER_CACHE_ENABLE  1
#define CONFIG_L1_CACHE_LINE_SIZE_BITS  6
#define CONFIG_VTIMER_UPDATE_VOFFSET  1
#define CONFIG_HAVE_FPU  1
#define CONFIG_PADDR_USER_DEVICE_TOP  1099511627776
#define CONFIG_ROOT_CNODE_SIZE_BITS  13
#define CONFIG_TIMER_TICK_MS  2
#define CONFIG_TIME_SLICE  5
#define CONFIG_RETYPE_FAN_OUT_LIMIT  256
#define CONFIG_MAX_NUM_WORK_UNITS_PER_PREEMPTION  100
#define CONFIG_RESET_CHUNK_BITS  8
#define CONFIG_MAX_NUM_BOOTINFO_UNTYPED_CAPS  230
#define CONFIG_FASTPATH  1
#define CONFIG_NUM_DOMAINS  1
#define CONFIG_NUM_PRIORITIES  256
#define CONFIG_MAX_NUM_NODES  1
#define CONFIG_KERNEL_STACK_BITS  12
#define CONFIG_FPU_MAX_RESTORES_SINCE_SWITCH  64
#define CONFIG_DEBUG_BUILD  1
#define CONFIG_PRINTING  1
#define CONFIG_NO_BENCHMARKS  1
#define CONFIG_KERNEL_BENCHMARK  none
#define CONFIG_MAX_NUM_TRACE_POINTS  0
#define CONFIG_IRQ_REPORTING  1
#define CONFIG_COLOUR_PRINTING  1
#define CONFIG_USER_STACK_TRACE_LENGTH  16
#define CONFIG_KERNEL_OPT_LEVEL_O2  1
#define CONFIG_KERNEL_OPT_LEVEL  -O2
#define CONFIG_KERNEL_OPTIMISATION_CLONE_FUNCTIONS  1
```

# v12.1.0の関連部分

## `util_libs/libplatsupport/CMakeLists.txt`の関連部分

- KernelArch = `arm`
- KernelArmMach = ``
- KernelPlatform = `bcm2837`
- KernelSel4Arch = `aarch64`

```
file(
    GLOB
        deps
        src/mach/${KernelArmMach}/*.c               // mach/                なし
        src/plat/${KernelPlatform}/*.c              // plat/bc2837/         chardev.c  gpio.c    mailbox.c
                                                    //                      serial.c  spt.c clock.c ltimer.c
                                                    //                      mailbox_util.c  serial.h  system_timer.c
        src/*.c                                     // src/                 fdt.c local_time_manager.c  tqueue.c
                                                    //                      io.c serial.c
        src/plat/${KernelPlatform}/acpi/*.c         // plat/bcm2837/acpi/   なし
)

if(${KernelArch} STREQUAL "arm")
    list(APPEND deps src/arch/arm/clock.c)
    list(APPEND deps src/arch/arm/delay.c)
    list(APPEND deps src/arch/arm/dma330.c)
    list(APPEND deps src/arch/arm/i2c.c)
    list(APPEND deps src/arch/arm/i2c_bitbang.c)
    list(APPEND deps src/arch/arm/generic_timer.c)
    list(APPEND deps src/arch/arm/irqchip/gic.c)
    list(APPEND deps src/arch/arm/irqchip/tegra.c)
    list(APPEND deps src/arch/arm/irqchip/gicv3.c)
    list(APPEND deps src/arch/arm/irqchip/avic.c)
    list(APPEND deps src/arch/arm/irqchip/omap3.c)
    # Link the IRQ chip parser modules
    list(
        APPEND
            irqchip_modules
            "-Wl,--undefined=arm_gic_ptr,--undefined=tegra_ictlr_ptr,--undefined=arm_gicv3_ptr,\
--undefined=fsl_avic_ptr,--undefined=ti_omap3_ptr"
    )
...
target_include_directories(
    platsupport
    PUBLIC
        include
        plat_include/${KernelPlatform}                      // plat_include/bcm2837
        sel4_arch_include/${_inc_folder_KernelSel4Arch}     // sel4_arch_include/aarch64
        arch_include/${KernelArch}                          // arch_include/arm
)

target_link_libraries(
    platsupport
    muslc
    utils
    fdt
    sel4_autoconf
    platsupport_Config
    ${irqchip_modules}
)
```

## `build/build.ninja`の関連部分

```bash
build apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/plat/bcm2837/ltimer.c.obj: C_COMPILER__platsupport_Debug /home/vagrant/sel4_12.1/projects/util_libs/libplatsupport/src/plat/bcm2837/ltimer.c || cmake_object_order_depends_target_platsupport
  DEP_FILE = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/plat/bcm2837/ltimer.c.obj.d
  FLAGS = -march=armv8-a+crc   -D__KERNEL_64__ -g -nostdinc -fno-pic -fno-pie -fno-stack-protector -fno-asynchronous-unwind-tables -ftls-model=local-exec -mstrict-align -mno-outline-atomics -std=gnu11
  INCLUDES = -I/home/vagrant/sel4_12.1/projects/util_libs/libplatsupport/include -I/home/vagrant/sel4_12.1/projects/util_libs/libplatsupport/plat_include/bcm2837 -I/home/vagrant/sel4_12.1/projects/util_libs/libplatsupport/sel4_arch_include/aarch64 -I/home/vagrant/sel4_12.1/projects/util_libs/libplatsupport/arch_include/arm -I/home/vagrant/sel4_12.1/build-rpi3-64/apps/sel4test-driver/musllibc/build-temp/stage/include -I/home/vagrant/sel4_12.1/projects/util_libs/libutils/include -I/home/vagrant/sel4_12.1/projects/util_libs/libutils/arch_include/arm -I/home/vagrant/sel4_12.1/build-rpi3-64/apps/sel4test-driver/util_libs/libutils/gen_config -I/home/vagrant/sel4_12.1/build-rpi3-64/libsel4/autoconf -I/home/vagrant/sel4_12.1/build-rpi3-64/kernel/gen_config -I/home/vagrant/sel4_12.1/build-rpi3-64/libsel4/gen_config -I/home/vagrant/sel4_12.1/projects/util_libs/libfdt/include -I/home/vagrant/sel4_12.1/projects/util_libs/libfdt/. -I/home/vagrant/sel4_12.1/build-rpi3-64/apps/sel4test-driver/util_libs/libplatsupport/gen_config
  OBJECT_DIR = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir
  OBJECT_FILE_DIR = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/src/plat/bcm2837
  TARGET_COMPILE_PDB = apps/sel4test-driver/util_libs/libplatsupport/CMakeFiles/platsupport.dir/platsupport.pdb
  TARGET_PDB = apps/sel4test-driver/util_libs/libplatsupport/libplatsupport.pdb
```

## `build/kernel/gen_config/kernel$ cat gen_config.h`

```c
#pragma once

#define CONFIG_ARCH_AARCH64 1
#define CONFIG_SEL4_ARCH aarch64
#define CONFIG_ARCH_ARM 1
#define CONFIG_ARCH arm
#define CONFIG_WORD_SIZE 64
#define CONFIG_ARM_PLAT rpi3
#define CONFIG_ARM_HIKEY_OUTSTANDING_PREFETCHERS 0
#define CONFIG_ARM_HIKEY_PREFETCHER_STRIDE 0
#define CONFIG_ARM_HIKEY_PREFETCHER_NPFSTRM 0
#define CONFIG_PLAT_BCM2837 1
#define CONFIG_PLAT bcm2837
#define CONFIG_ARM_CORTEX_A53 1
#define CONFIG_ARCH_ARM_V8A 1
#define CONFIG_ARM_MACH
#define CONFIG_ARM_PA_SIZE_BITS_40 1
#define CONFIG_ARM_ICACHE_VIPT 1
#define CONFIG_L1_CACHE_LINE_SIZE_BITS 6
#define CONFIG_VTIMER_UPDATE_VOFFSET 1
#define CONFIG_HAVE_FPU 1
#define CONFIG_PADDR_USER_DEVICE_TOP 1099511627776
#define CONFIG_ROOT_CNODE_SIZE_BITS 13
#define CONFIG_TIMER_TICK_MS 2
#define CONFIG_TIME_SLICE 5
#define CONFIG_RETYPE_FAN_OUT_LIMIT 256
#define CONFIG_MAX_NUM_WORK_UNITS_PER_PREEMPTION 100
#define CONFIG_RESET_CHUNK_BITS 8
#define CONFIG_MAX_NUM_BOOTINFO_UNTYPED_CAPS 230
#define CONFIG_FASTPATH 1
#define CONFIG_NUM_DOMAINS 1
#define CONFIG_NUM_PRIORITIES 256
#define CONFIG_MAX_NUM_NODES 1
#define CONFIG_KERNEL_STACK_BITS 12
#define CONFIG_FPU_MAX_RESTORES_SINCE_SWITCH 64
#define CONFIG_DEBUG_BUILD 1
#define CONFIG_PRINTING 1
#define CONFIG_KERNEL_BENCHMARK none
#define CONFIG_NO_BENCHMARKS 1
#define CONFIG_MAX_NUM_TRACE_POINTS 0
#define CONFIG_IRQ_REPORTING 1
#define CONFIG_COLOUR_PRINTING 1
#define CONFIG_USER_STACK_TRACE_LENGTH 16
#define CONFIG_KERNEL_OPT_LEVEL -O2
#define CONFIG_KERNEL_OPT_LEVEL_O2 1
```

## rpi3bp.dts

```
        interrupt-controller@7e00b200 {
            compatible = "brcm,bcm2836-armctrl-ic";
            reg = <0x7e00b200 0x200>;
            interrupt-controller;
            #interrupt-cells = <0x02>;
            interrupt-parent = <0x18>;
            interrupts = <0x08 0x04>;
            phandle = <0x01>;
        };

        local_intc@40000000 {
            compatible = "brcm,bcm2836-l1-intc";
            reg = <0x40000000 0x100>;
            interrupt-controller;
            #interrupt-cells = <0x02>;
            interrupt-parent = <0x18>;
            phandle = <0x18>;
        };

/ {
    compatible = "raspberrypi,3-model-b-plus\0brcm,bcm2837";
    model = "Raspberry Pi 3 Model B+";
    #address-cells = <0x01>;
    #size-cells = <0x01>;
    interrupt-parent = <0x01>;

    arm-pmu {
        compatible = "arm,cortex-a53-pmu\0arm,cortex-a7-pmu";
        interrupt-parent = <0x18>;
        interrupts = <0x09 0x04>;
    };

    timer {
        compatible = "arm,armv7-timer";
        interrupt-parent = <0x18>;
        interrupts = <0x00 0x04 0x01 0x04 0x03 0x04 0x02 0x04>;
        always-on;
    };

```

## rpi4.dts

```
        interrupt-controller@40041000 {
            interrupt-controller;
            #interrupt-cells = <0x03>;
            compatible = "arm,gic-400";
            reg = <0x40041000 0x1000 0x40042000 0x2000 0x40044000 0x2000 0x40046000 0x2000>;
            interrupts = <0x01 0x09 0xf04>;
            phandle = <0x01>;
        };

/ {
    compatible = "raspberrypi,4-model-b\0brcm,bcm2711";
    model = "Raspberry Pi 4 Model B";
    #address-cells = <0x02>;
    #size-cells = <0x01>;
    interrupt-parent = <0x01>;

```
