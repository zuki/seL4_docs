# rpi3 32版の実行メモ

```
$ mkdir build-rpi && c build-rpi
$ ../init-build.sh -DPLATFORM=rpi3 -DAARCH32=1
loading initial cache file /home/vagrant/sel4/projects/sel4test/settings.cmake
-- Set platform details from PLATFORM=rpi3
--   KernelPlatform: bcm2837
--   KernelARMPlatform: rpi3
-- Setting from flags KernelSel4Arch: aarch32
-- Found seL4: /home/vagrant/sel4/kernel
-- Found GCC with prefix arm-linux-gnueabi-
-- The C compiler identification is GNU 11.3.0
-- The CXX compiler identification is GNU 11.3.0
-- The ASM compiler identification is GNU
-- Found assembler: /usr/bin/arm-linux-gnueabi-gcc
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/arm-linux-gnueabi-gcc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/arm-linux-gnueabi-g++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found elfloader-tool: /home/vagrant/sel4/tools/seL4/elfloader-tool
-- /home/vagrant/sel4/build-rpi/kernel/gen_headers/plat/machine/devices_gen.h is out of date. Regenerating from DTB...
-- CPIO test cpio_reproducible_flag PASSED
-- Found musllibc: /home/vagrant/sel4/projects/musllibc
-- Found util_libs: /home/vagrant/sel4/projects/util_libs
-- Found seL4_libs: /home/vagrant/sel4/projects/seL4_libs
-- Found sel4_projects_libs: /home/vagrant/sel4/projects/sel4_projects_libs
-- Found sel4runtime: /home/vagrant/sel4/projects/sel4runtime
-- Performing Test compiler_arch_test
-- Performing Test compiler_arch_test - Success
-- libmuslc architecture: 'arm' (from KernelSel4Arch 'aarch32')
-- Detecting cached version of: musllibc
-- Found Git: /usr/bin/git (found version "2.34.1")
--   Found valid cache entry for musllibc
-- Found Nanopb: /home/vagrant/sel4/tools/nanopb
-- Configuring done
-- Generating done
-- Build files have been written to: /home/vagrant/sel4/build-rpi

$ ninja
[179/322] Building C object apps/sel4t...eFiles/sel4utils.dir/src/process.c.obj
/home/vagrant/sel4/projects/seL4_libs/libsel4utils/src/process.c: In function ‘sel4utils_spawn_process_v’:
/home/vagrant/sel4/projects/seL4_libs/libsel4utils/src/process.c:320:13: warning: ‘sel4utils_stack_copy_args’ accessing 4 bytes in a region of size 0 [-Wstringop-overflow=]
  320 |     error = sel4utils_stack_copy_args(vspace, &process->vspace, vka, envc, envp, dest_envp, &initial_stack_pointer);
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/vagrant/sel4/projects/seL4_libs/libsel4utils/src/process.c:320:13: note: referencing argument 5 of type ‘char **’
/home/vagrant/sel4/projects/seL4_libs/libsel4utils/src/process.c:209:12: note: in a call to function ‘sel4utils_stack_copy_args’
  209 | static int sel4utils_stack_copy_args(vspace_t *current_vspace, vspace_t *target_vspace,
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~
[218/322] Running C++ protocol buffer ...el4_projects_libs/libsel4rpc/rpc.proto
/home/vagrant/sel4/build-rpi/nanopb/generator/nanopb_generator.py:21: DeprecationWarning: The distutils package is deprecated and slated for removal in Python 3.12. Use setuptools or check PEP 632 for potential alternatives
  import google, distutils.util # bbfreeze seems to need these
[322/322] Generating ../../images/sel4test-driver-image-arm-bcm2837
$ ls kernel/
autoconf                 kernel_all.i
circular_includes_valid  kernel_all_pp_prune.c
CMakeFiles               kernel_all_pp_prune_wrapper_temp.c
cmake_install.cmake      kernel_bf_gen_target_11_pbf_temp.c
dts.cmd                  kernel_bf_gen_target_1_pbf_temp.c
gen_config               kernel_compat.txt
generated                kernel.dtb
generated_prune          kernel.dts
gen_header.cmd           kernel.elf
gen_headers              linker.lds_pp
kernel_all.c             linker_ld_wrapper_temp.c
kernel_all_copy.c
vagrant@ubuntu-bionic:~/sel4/seL4test/build-rpi$ ls images/
sel4test-driver-image-arm-bcm2837
```

# 実行

```
$ minicom


Welcome to minicom 2.8

OPTIONS:
Compiled on Jan  4 2021, 00:04:46.
Port /dev/cu.usbserial-AI057C9L, 08:38:54
Using character set conversion

Press Meta-Z for help on special keys



U-Boot 2017.11-rc2-00044-g8e6a94ecb0 (Oct 26 2017 - 11:16:28 +1100)

DRAM:  948 MiB
RPI: Board rev 0xd outside known range
RPI Unknown model (0xa020d3)
MMC:   sdhci@7e300000: 0
*** Warning - bad CRC, using default environment

In:    serial
Out:   vidconsole
Err:   vidconsole
Net:   No ethernet found.
starting USB...
USB0:   Core Release: 2.80a
scanning bus 0 for devices... 4 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
Hit any key to stop autoboot:  0
U-Boot> fatls mmc 0
    18693   COPYING.linux
     1594   LICENCE.broadcom
    29231   bcm2708-rpi-b-plus.dtb
    28579   bcm2708-rpi-b-rev1.dtb
    28912   bcm2708-rpi-b.dtb
    28655   bcm2708-rpi-cm.dtb
    30408   bcm2708-rpi-zero-w.dtb
    28537   bcm2708-rpi-zero.dtb
    30879   bcm2709-rpi-2-b.dtb
    30786   bcm2709-rpi-cm2.dtb
    31028   bcm2710-rpi-2-b.dtb
    33851   bcm2710-rpi-3-b-plus.dtb
    33224   bcm2710-rpi-3-b.dtb
    30923   bcm2710-rpi-cm3.dtb
    32416   bcm2710-rpi-zero-2-w.dtb
    32416   bcm2710-rpi-zero-2.dtb
    54388   bcm2711-rpi-4-b.dtb
    54477   bcm2711-rpi-400.dtb
    38182   bcm2711-rpi-cm4-io.dtb
    54997   bcm2711-rpi-cm4.dtb
    51839   bcm2711-rpi-cm4s.dtb
    52476   bootcode.bin
     7266   fixup.dat
     5397   fixup4.dat
     3171   fixup4cd.dat
     8381   fixup4db.dat
     8385   fixup4x.dat
     3171   fixup_cd.dat
    10230   fixup_db.dat
    10228   fixup_x.dat
   393408   u-boot.bin
     4096   ._u-boot.bin
            overlays/
  2977280   start.elf
  2253088   start4.elf
   806492   start4cd.elf
  3749544   start4db.elf
  3000552   start4x.elf
   806492   start_cd.elf
  4821448   start_db.elf
  3724200   start_x.elf
  5183020   sel4test-driver-image-arm-bcm2837
       33   config.txt

42 file(s), 1 dir(s)

U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
reading sel4test-driver-image-arm-bcm2837
5183020 bytes read in 386 ms (12.8 MiB/s)
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
reading sel4test-driver-image-arm-bcm2837
5183020 bytes read in 379 ms (13 MiB/s)
U-Boot> bootelf 0x10000000
CACHE: Misaligned operation at range [01853000, 01853034]
CACHE: Misaligned operation at range [01854000, 0185eb0c]
CACHE: Misaligned operation at range [0185eb0c, 0185fa7f]
CACHE: Misaligned operation at range [0185fa80, 0185fa88]
CACHE: Misaligned operation at range [01860000, 0186d048]
CACHE: Misaligned operation at range [0186d048, 01d19a48]
CACHE: Misaligned operation at range [01d29a48, 01d29aa4]
CACHE: Misaligned operation at range [01d29aa4, 01d29ad4]
## Starting application at 0x01853000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[1853000..1d29ad3]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at 19569dc.
Loaded DTB from 19569dc.
   paddr=[1040000..1043fff]
ELF-loading image 'kernel' to 1000000
  paddr=[1000000..103ffff]
  vaddr=[e0000000..e003ffff]
  virt_entry=e0000000
ELF-loading image 'sel4test-driver' to 1044000
  paddr=[1044000..1459fff]
  vaddr=[10000..425fff]
  virt_entry=1e200
Enabling MMU and paging
Jumping to kernel-image entry point...

DIDRv: 6, armv 80, coproc baseline only? no.
Bootstrapping kernel
available phys memory regions: 1
  [1000000..8000000]
reserved virt address space regions: 4
  [e0000000..e0040000]
  [e0040000..e0043593]
  [e0044000..e045a000]
  [ff000000..ff100000]
Booting all finished, dropped to user space
ps_fdt_walk_irqs@fdt.c:229 Could not find a parser for this particular interrupr
helper_fdt_alloc_simple@ltimer.h:78 Simple FDT helper failed to register irqs ()
bcm_system_timer_init@system_timer.c:214 Failed to allocate with fdt
ltimer_default_init@ltimer.c:112 system timer initialisation failed
init_timer@main.c:181 [Cond failed: error]
        Failed to setup the timers
seL4 root server abort()ed
Debug halt syscall from user thread 0xe6feb400 "sel4test-driver"
halting...
Kernel entry via Unknown syscall, word: 1
```

## u-bootをコンパイル

- ubuntu2004上で作業
- `multiple definitions`対策のコード修正
- キャッシュを無効にするためのパッチ適用

```
$ git clone https://github.com/u-boot/u-boot.git u-boot
$ cd u-boot
$ vi cmd/elf.c
$ vi boot/bootm_os.c
$ git diff
diff --git a/boot/bootm_os.c b/boot/bootm_os.c
index 99ff0e6c..ec361d4c 100644
--- a/boot/bootm_os.c
+++ b/boot/bootm_os.c
@@ -370,7 +370,6 @@ static int do_bootm_qnxelf(int flag, int argc, char *const argv[],
 {
 	char *local_args[2];
 	char str[16];
-	int dcache;

 	if (flag != BOOTM_STATE_OS_GO)
 		return 0;
@@ -386,18 +385,8 @@ static int do_bootm_qnxelf(int flag, int argc, char *const argv[],
 	local_args[0] = argv[0];
 	local_args[1] = str;	/* and provide it via the arguments */

-	/*
-	 * QNX images require the data cache is disabled.
-	 */
-	dcache = dcache_status();
-	if (dcache)
-		dcache_disable();
-
 	do_bootelf(NULL, 0, 2, local_args);

-	if (dcache)
-		dcache_enable();
-
 	return 1;
 }
 #endif
diff --git a/cmd/elf.c b/cmd/elf.c
index b7b9f506..d1dc98ed 100644
--- a/cmd/elf.c
+++ b/cmd/elf.c
@@ -26,12 +26,23 @@ static unsigned long do_bootelf_exec(ulong (*entry)(int, char * const[]),
 {
 	unsigned long ret;

+    /*
+	 * QNX images require the data cache is disabled.
+	 * Data cache is already flushed, so just turn it off.
+	 */
+	int dcache = dcache_status();
+	if (dcache)
+        dcache_disable();
+
 	/*
 	 * pass address parameter as argv[0] (aka command name),
 	 * and all remaining args
 	 */
 	ret = entry(argc, argv);

+	if (dcache)
+	    dcache_enable();
+
 	return ret;
 }

$ make CROSS_COMPILE=arm-linux-gnueabi- rpi_3_32b_defconfig
$ vi .config
CONFIG_DEFAULT_DEVICE_TREE="bcm2837-rpi-3-b-plus"
$ make CROSS_COMPILE=arm-linux-gnueabi-
$ make CROSS_COMPILE=arm-linux-gnueabi-
$ ls
api     config.mk  examples  MAINTAINERS  System.map  u-boot.map
arch    configs    fs        Makefile     test        u-boot-nodtb.bin
bin     disk       include   net          tools       u-boot.srec
board   doc        Kbuild    post         u-boot      u-boot.sym
boot    drivers    Kconfig   py           u-boot.bin
cmd     dts        lib       README       u-boot.cfg
common  env        Licenses  scripts      u-boot.lds
```

## u-bootを置き換えて実行

```
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
5183020 bytes read in 227 ms (21.8 MiB/s)
U-Boot> bootelf 0x10000000
U-Boot>                       # 何も起きない
U-Boot> md 0x10000000
10000000: 464c457f 00010101 00000000 00000000  .ELF............
10000010: 00280002 00000001 01853000 00000034  ..(......0..4...
10000020: 004f12bc 05000200 00200034 00280004  ..O.....4. ...(.
10000030: 00150016 70000001 0000fa80 0185fa80  .......p........
10000040: 0185fa80 00000008 00000008 00000004  ................
10000050: 00000004 00000001 00000000 01850000  ................
10000060: 01850000 0000fa88 0000fa88 00000005  ................
10000070: 00010000 00000001 00010000 01860000  ................
10000080: 01860000 004c9ad4 004c9ad4 00000006  ......L...L.....
10000090: 00010000 6474e551 00000000 00000000  ....Q.td........
100000a0: 00000000 00000000 00000000 00000007  ................
100000b0: 00000010 00000000 00000000 00000000  ................
100000c0: 00000000 00000000 00000000 00000000  ................
100000d0: 00000000 00000000 00000000 00000000  ................
100000e0: 00000000 00000000 00000000 00000000  ................
100000f0: 00000000 00000000 00000000 00000000  ................
U-Boot> [Return]
10000100: 00000000 00000000 00000000 00000000  ................
10000110: 00000000 00000000 00000000 00000000  ................
10000120: 00000000 00000000 00000000 00000000  ................
10000130: 00000000 00000000 00000000 00000000  ................
10000140: 00000000 00000000 00000000 00000000  ................
10000150: 00000000 00000000 00000000 00000000  ................
10000160: 00000000 00000000 00000000 00000000  ................
10000170: 00000000 00000000 00000000 00000000  ................
10000180: 00000000 00000000 00000000 00000000  ................
10000190: 00000000 00000000 00000000 00000000  ................
100001a0: 00000000 00000000 00000000 00000000  ................
100001b0: 00000000 00000000 00000000 00000000  ................
100001c0: 00000000 00000000 00000000 00000000  ................
100001d0: 00000000 00000000 00000000 00000000  ................
100001e0: 00000000 00000000 00000000 00000000  ................
100001f0: 00000000 00000000 00000000 00000000  ................
```

## u-bootのv2019.10版を試す

`bcm2837-rpi-3-b-plus`が`arch/arm/dts`に追加されたのはv2019からのようなので、
その最新版をビルドしてみた。

```
$ vi cmd/elf.c
$ vi common/bootm_os.c
$ make CROSS_COMPILE=arm-linux-gnueabi- rpi_3_32b_defconfig
$ vi .config
$ make CROSS_COMPILE=arm-linux-gnueabi-
  LEX     scripts/dtc/dtc-lexer.lex.c
  YACC    scripts/dtc/dtc-parser.tab.h
  HOSTCC  scripts/dtc/dtc-lexer.lex.o
  YACC    scripts/dtc/dtc-parser.tab.c
  HOSTCC  scripts/dtc/dtc-parser.tab.o
  HOSTLD  scripts/dtc/dtc
/usr/bin/ld: scripts/dtc/dtc-parser.tab.o:(.bss+0x10): multiple definition of `yylloc'; scripts/dtc/dtc-lexer.lex.o:(.bss+0x0): first defined here
collect2: error: ld returned 1 exit status
make[2]: *** [scripts/Makefile.host:106: scripts/dtc/dtc] Error 1
make[1]: *** [scripts/Makefile.build:432: scripts/dtc] Error 2
make: *** [Makefile:528: scripts] Error 2
$ vi scripts/dtc/dtc-lexer.lex.c
# L618にexternを追加
# YYLTYPE yylloc;
extern YYLTYPE yylloc;
$ make CROSS_COMPILE=arm-linux-gnueabi-
...
  LDS     u-boot.lds
  LD      u-boot
  OBJCOPY u-boot.srec
  OBJCOPY u-boot-nodtb.bin
  COPY    u-boot.bin
  SYM     u-boot.sym
===================== WARNING ======================
CONFIG_OF_EMBED is enabled. This option should only
be used for debugging purposes. Please use
CONFIG_OF_SEPARATE for boards in mainline.
See doc/README.fdt-control for more info.
====================================================
  CFGCHK  u-boot.cfg
$ ls
api        doc            Kbuild       README      u-boot.cfg.configs
arch       Documentation  Kconfig      scripts     u-boot.lds
board      drivers        lib          System.map  u-boot.map
cmd        dts            Licenses     test        u-boot-nodtb.bin
common     env            MAINTAINERS  tools       u-boot.srec
config.mk  examples       Makefile     u-boot      u-boot.sym
configs    fs             net          u-boot.bin
disk       include        post         u-boot.cfg
```

## 実行

結果は同じ。

```
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
5183020 bytes read in 227 ms (21.8 MiB/s)
U-Boot> bootelf 0x10000000
## Starting application at 0x01853000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[1853000..1d29ad3]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at 19569dc.
Loaded DTB from 19569dc.
   paddr=[1040000..1043fff]
ELF-loading image 'kernel' to 1000000
  paddr=[1000000..103ffff]
  vaddr=[e0000000..e003ffff]
  virt_entry=e0000000
ELF-loading image 'sel4test-driver' to 1044000
  paddr=[1044000..1459fff]
  vaddr=[10000..425fff]
  virt_entry=1e200
Enabling MMU and paging
Jumping to kernel-image entry point...

DIDRv: 6, armv 80, coproc baseline only? no.
Bootstrapping kernel
available phys memory regions: 1
  [1000000..8000000]
reserved virt address space regions: 4
  [e0000000..e0040000]
  [e0040000..e0043593]
  [e0044000..e045a000]
  [ff000000..ff100000]
Booting all finished, dropped to user space
// debug print: これらはいずれもutil_libs/libplatsupport/src/arch/*.cで登録されている.
//   raspi3は仕組みが違うので、別に作成する必要がある
find_compatible_irq_controller@fdt.c:142 dtb_blob: ?
find_compatible_irq_controller@fdt.c:145 comp_str: arm,gic-400
find_compatible_irq_controller@fdt.c:145 comp_str: arm,cortex-a9-gic
find_compatible_irq_controller@fdt.c:145 comp_str: arm,cortex-a15-gic
find_compatible_irq_controller@fdt.c:145 comp_str: arm,gic-v3
find_compatible_irq_controller@fdt.c:145 comp_str: ti,omap3-intc
find_compatible_irq_controller@fdt.c:145 comp_str: ti,am33xx-intc
find_compatible_irq_controller@fdt.c:145 comp_str: nvidia,tegra210-ictlr
find_compatible_irq_controller@fdt.c:145 comp_str: nvidia,tegra124-ictlr
find_compatible_irq_controller@fdt.c:145 comp_str: nvidia,tegra30-ictlr
// ここまで
# CURRENT: util_libs/libplatsupport/src/fdt.c
ps_fdt_walk_irqs@fdt.c:229 Could not find a parser for this particular interrupr
# CURRENT: util_libs/libplatsupport/src/ltimer.h
helper_fdt_alloc_simple@ltimer.h:78 Simple FDT helper failed to register irqs ()
# CURRENT : util_libs/libplatsupport/src/mach/bcm/system_timer.c
bcm_system_timer_init@system_timer.c:214 Failed to allocate with fdt
# CURRENT: util_libs/libplatsupport/src/mach/bcm/ltimer.c
# 10.0.x-compatible: src/plat/bcm2837/ltimer.c
ltimer_default_init@ltimer.c:112 system timer initialisation failed
# CURRENT : sel4test/apps/sel4test-driver/src/main.c
init_timer@main.c:181 [Cond failed: error]
        Failed to setup the timers
seL4 root server abort()ed
Debug halt syscall from user thread 0xe6feb400 "sel4test-driver"
halting...
Kernel entry via Unknown syscall, word: 1
```

## 最新版のseL4はraspi3に対応していない模様

[情報源](https://sel4.discourse.group/t/latest-sel4-tests-fail-on-raspberry-pi-3/662)

ここで指摘されている[修正パッチ](https://github.com/seL4/util_libs/pull/140/files)は
最新版では適用済みであった。

# 10.1.1 を試す

[seL4 releases](https://docs.sel4.systems/releases/sel4)によるとraspi3が初めて
サポートされたバージョンは4.0.0, raspi3の64ビット版がサポートされたのは10.0.0。
4.0.0はsel4test-minifestが対応していない（tagはあるがrepoでインストールできない）。
util_libsでirqchipディレクトリができたのが11.0.x対応版からだったので、10.1.1を
試すことにした。

```bash
$ mkdir sel4test && cd sel4test
$ repo init -u https://github.com/seL4/seL4test-manifest.git -b refs/tags/10.1.1
$ repo sync
$ vi libsel4/tools/syscall_stub_gen.py
$ git diff libsel4/tools/syscall_stub_gen.py
diff --git a/libsel4/tools/syscall_stub_gen.py b/libsel4/tools/syscall_stub_gen.py
index 1d629b6c3..6edb18c85 100644
--- a/libsel4/tools/syscall_stub_gen.py
+++ b/libsel4/tools/syscall_stub_gen.py
@@ -1036,7 +1036,7 @@ def main():
     else:
         wordsize = int(args.wsize)

-    if wordsize is -1:
+    if wordsize == -1:
         print("Invalid word size.")
         sys.exit(2)

$ vi tools/circular_includes.py
$ git diff tools/circular_includes.py
diff --git a/tools/circular_includes.py b/tools/circular_includes.py
index ffe1829b5..9078d04d3 100755
--- a/tools/circular_includes.py
+++ b/tools/circular_includes.py
@@ -62,7 +62,7 @@ def main(parse_args):
                 file_stack.append(header)
         else:
             # popped back up to an earlier header
-            while file_stack[-1] != header:
+            while (len(file_stack) > 0) and (file_stack[-1] != header):
                 file_stack.pop()

     return 0
$ ninja
# linkエラー: multiple definitions
```

## コミットを発見

[commit 5a34161: Do not generate data symbols for enums](https://github.com/seL4/seL4/commit/5a341610c4a4bc14f9cd86f31e39a34223c754d1#diff-abbb42a89b63341bd26357c8addae2ee68025e835e477bedc71e419401ddd054)がそれらしい。適用して実行すると`multiple definitions`によるエラーは解決した。

```diff
$ git diff
diff --git a/libsel4/include/sel4/constants.h b/libsel4/include/sel4/constants.h
index 0251ade04..540fb2579 100644
--- a/libsel4/include/sel4/constants.h
+++ b/libsel4/include/sel4/constants.h
@@ -39,7 +39,7 @@ typedef enum {
 } seL4_BreakpointAccess;

 /* Format of a debug-exception message. */
-enum {
+typedef enum {
     seL4_DebugException_FaultIP,
     seL4_DebugException_ExceptionReason,
     seL4_DebugException_TriggerAddress,
diff --git a/libsel4/include/sel4/shared_types.h b/libsel4/include/sel4/shared_types.h
index 2da0f8e2a..9b4534492 100644
--- a/libsel4/include/sel4/shared_types.h
+++ b/libsel4/include/sel4/shared_types.h
@@ -25,7 +25,7 @@ typedef struct seL4_IPCBuffer_ {
     seL4_Word receiveDepth;
 } seL4_IPCBuffer __attribute__ ((__aligned__ (sizeof(struct seL4_IPCBuffer_))));

-enum {
+typedef enum {
     seL4_CapFault_IP,
     seL4_CapFault_Addr,
     seL4_CapFault_InRecvPhase,
diff --git a/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h b/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
index 39ab3837f..b98cd9f49 100644
--- a/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
+++ b/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
@@ -25,7 +25,7 @@ enum {
 #endif /* CONFIG_IPC_BUF_GLOBALS_FRAME */

 /* format of an unknown syscall message */
-enum {
+typedef enum {
     seL4_UnknownSyscall_R0,
     seL4_UnknownSyscall_R1,
     seL4_UnknownSyscall_R2,
@@ -45,7 +45,7 @@ enum {
 } seL4_UnknownSyscall_Msg;

 /* format of a user exception message */
-enum {
+typedef enum {
     seL4_UserException_FaultIP,
     seL4_UserException_SP,
     seL4_UserException_CPSR,
@@ -57,7 +57,7 @@ enum {
 } seL4_UserException_Msg;

 /* format of a vm fault message */
-enum {
+typedef enum {
     seL4_VMFault_IP,
     seL4_VMFault_Addr,
     seL4_VMFault_PrefetchFault,
@@ -67,19 +67,19 @@ enum {
 } seL4_VMFault_Msg;

 #ifdef CONFIG_ARM_HYPERVISOR_SUPPORT
-enum {
+typedef enum {
     seL4_VGICMaintenance_IDX,
     seL4_VGICMaintenance_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VGICMaintenance_Msg),
 } seL4_VGICMaintenance_Msg;

-enum {
+typedef enum {
     seL4_VCPUFault_HSR,
     seL4_VCPUFault_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VCPUFault_Msg),
 } seL4_VCPUFault_Msg;

-enum {
+typedef enum {
     seL4_VCPUReg_SCTLR = 0,
     seL4_VCPUReg_ACTLR,
     seL4_VCPUReg_TTBCR,
diff --git a/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h b/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
index 40164ade4..e1567781e 100644
--- a/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
+++ b/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
@@ -19,7 +19,7 @@

 #ifndef __ASSEMBLER__
 /* format of an unknown syscall message */
-enum {
+typedef enum {
     seL4_UnknownSyscall_X0,
     seL4_UnknownSyscall_X1,
     seL4_UnknownSyscall_X2,
@@ -39,7 +39,7 @@ enum {
 } seL4_UnknownSyscall_Msg;

 /* format of a user exception message */
-enum {
+typedef enum {
     seL4_UserException_FaultIP,
     seL4_UserException_SP,
     seL4_UserException_SPSR,
@@ -51,7 +51,7 @@ enum {
 } seL4_UserException_Msg;

 /* format of a vm fault message */
-enum {
+typedef enum {
     seL4_VMFault_IP,
     seL4_VMFault_Addr,
     seL4_VMFault_PrefetchFault,
@@ -62,19 +62,19 @@ enum {

 #ifdef CONFIG_ARM_HYPERVISOR_SUPPORT

-enum {
+typedef enum {
     seL4_VGICMaintenance_IDX,
     seL4_VGICMaintenance_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VGICMaintenance_Msg),
 } seL4_VGICMaintenance_Msg;

-enum {
+typedef enum {
     seL4_VCPUFault_HSR,
     seL4_VCPUFault_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VCPUFault_Msg),
 } seL4_VCPUFault_Msg;

-enum {
+typedef enum {
     /* VM control registers EL1 */
     seL4_VCPUReg_SCTLR = 0,
     seL4_VCPUReg_TTBR0,
diff --git a/libsel4/tools/syscall_stub_gen.py b/libsel4/tools/syscall_stub_gen.py
index 1d629b6c3..6edb18c85 100644
diff --git a/libsel4/include/sel4/constants.h b/libsel4/include/sel4/constants.h
index 0251ade04..540fb2579 100644
--- a/libsel4/include/sel4/constants.h
+++ b/libsel4/include/sel4/constants.h
@@ -39,7 +39,7 @@ typedef enum {
 } seL4_BreakpointAccess;

 /* Format of a debug-exception message. */
-enum {
+typedef enum {
     seL4_DebugException_FaultIP,
     seL4_DebugException_ExceptionReason,
     seL4_DebugException_TriggerAddress,
diff --git a/libsel4/include/sel4/shared_types.h b/libsel4/include/sel4/shared_t
ypes.h
index 2da0f8e2a..9b4534492 100644
--- a/libsel4/include/sel4/shared_types.h
+++ b/libsel4/include/sel4/shared_types.h
@@ -25,7 +25,7 @@ typedef struct seL4_IPCBuffer_ {
     seL4_Word receiveDepth;
 } seL4_IPCBuffer __attribute__ ((__aligned__ (sizeof(struct seL4_IPCBuffer_))))
;

-enum {
+typedef enum {
     seL4_CapFault_IP,
     seL4_CapFault_Addr,
     seL4_CapFault_InRecvPhase,
diff --git a/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h b/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
index 39ab3837f..b98cd9f49 100644
--- a/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
+++ b/libsel4/sel4_arch_include/aarch32/sel4/sel4_arch/constants.h
@@ -25,7 +25,7 @@ enum {
 #endif /* CONFIG_IPC_BUF_GLOBALS_FRAME */

 /* format of an unknown syscall message */
-enum {
+typedef enum {
     seL4_UnknownSyscall_R0,
     seL4_UnknownSyscall_R1,
     seL4_UnknownSyscall_R2,
@@ -45,7 +45,7 @@ enum {
 } seL4_UnknownSyscall_Msg;

 /* format of a user exception message */
-enum {
+typedef enum {
     seL4_UserException_FaultIP,
     seL4_UserException_SP,
     seL4_UserException_CPSR,
@@ -57,7 +57,7 @@ enum {
 } seL4_UserException_Msg;

 /* format of a vm fault message */
-enum {
+typedef enum {
     seL4_VMFault_IP,
     seL4_VMFault_Addr,
     seL4_VMFault_PrefetchFault,
@@ -67,19 +67,19 @@ enum {
 } seL4_VMFault_Msg;

 #ifdef CONFIG_ARM_HYPERVISOR_SUPPORT
-enum {
+typedef enum {
     seL4_VGICMaintenance_IDX,
     seL4_VGICMaintenance_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VGICMaintenance_Msg),
 } seL4_VGICMaintenance_Msg;

-enum {
+typedef enum {
     seL4_VCPUFault_HSR,
     seL4_VCPUFault_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VCPUFault_Msg),
 } seL4_VCPUFault_Msg;

-enum {
+typedef enum {
     seL4_VCPUReg_SCTLR = 0,
     seL4_VCPUReg_ACTLR,
     seL4_VCPUReg_TTBCR,
diff --git a/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h b/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
index 40164ade4..e1567781e 100644
--- a/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
+++ b/libsel4/sel4_arch_include/aarch64/sel4/sel4_arch/constants.h
@@ -19,7 +19,7 @@

 #ifndef __ASSEMBLER__
 /* format of an unknown syscall message */
-enum {
+typedef enum {
     seL4_UnknownSyscall_X0,
     seL4_UnknownSyscall_X1,
     seL4_UnknownSyscall_X2,
@@ -39,7 +39,7 @@ enum {
 } seL4_UnknownSyscall_Msg;

 /* format of a user exception message */
-enum {
+typedef enum {
     seL4_UserException_FaultIP,
     seL4_UserException_SP,
     seL4_UserException_SPSR,
@@ -51,7 +51,7 @@ enum {
 } seL4_UserException_Msg;

 /* format of a vm fault message */
-enum {
+typedef enum {
     seL4_VMFault_IP,
     seL4_VMFault_Addr,
     seL4_VMFault_PrefetchFault,
@@ -62,19 +62,19 @@ enum {

 #ifdef CONFIG_ARM_HYPERVISOR_SUPPORT

-enum {
+typedef enum {
     seL4_VGICMaintenance_IDX,
     seL4_VGICMaintenance_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VGICMaintenance_Msg),
 } seL4_VGICMaintenance_Msg;

-enum {
+typedef enum {
     seL4_VCPUFault_HSR,
     seL4_VCPUFault_Length,
     SEL4_FORCE_LONG_ENUM(seL4_VCPUFault_Msg),
 } seL4_VCPUFault_Msg;

-enum {
+typedef enum {
     /* VM control registers EL1 */
     seL4_VCPUReg_SCTLR = 0,
     seL4_VCPUReg_TTBR0,


$ ninja
...
[206/240] Building C object projects/s...les/sel4test-driver.dir/src/main.c.obj
FAILED: projects/sel4test/apps/sel4test-driver/CMakeFiles/sel4test-driver.dir/src/main.c.obj
ccache /usr/bin/arm-linux-gnueabi-gcc --sysroot=/home/vagrant/sel4test/build-rpi3 -DHAVE_AUTOCONF -I/home/vagrant/sel4test/projects/sel4test/apps/sel4test-driver/include -I/home/vagrant/sel4test/build-rpi3/autoconf -I/home/vagrant/sel4test/build-rpi3/kernel/gen_config -I/home/vagrant/sel4test/build-rpi3/elfloader-tool/gen_config -I/home/vagrant/sel4test/build-rpi3/libsel4/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4vka/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4utils/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4platsupport/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4serialserver/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4debug/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4test/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4muslcsys/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/seL4_libs/libsel4vmm/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/sel4test/apps/sel4test-driver/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/util_libs/libutils/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/util_libs/libplatsupport/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/util_libs/libethdrivers/gen_config -I/home/vagrant/sel4test/build-rpi3/projects/musllibc/build-temp/stage/include -I/home/vagrant/sel4test/kernel/libsel4/include -I/home/vagrant/sel4test/kernel/libsel4/arch_include/arm -I/home/vagrant/sel4test/kernel/libsel4/sel4_arch_include/aarch32 -I/home/vagrant/sel4test/kernel/libsel4/sel4_plat_include/bcm2837 -I/home/vagrant/sel4test/kernel/libsel4/mode_include/32 -I/home/vagrant/sel4test/build-rpi3/libsel4/include -I/home/vagrant/sel4test/build-rpi3/libsel4/arch_include/arm -I/home/vagrant/sel4test/build-rpi3/libsel4/sel4_arch_include/aarch32 -I/home/vagrant/sel4test/projects/seL4_libs/libsel4allocman/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4allocman/sel4_arch/aarch32 -I/home/vagrant/sel4test/projects/seL4_libs/libsel4allocman/arch/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4vka/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4vka/sel4_arch_include/aarch32 -I/home/vagrant/sel4test/projects/seL4_libs/libsel4vka/arch_include/arm -I/home/vagrant/sel4test/projects/util_libs/libutils/include -I/home/vagrant/sel4test/projects/util_libs/libutils/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4utils/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4utils/sel4_arch_include/aarch32 -I/home/vagrant/sel4test/projects/seL4_libs/libsel4utils/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4vspace/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4vspace/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4simple/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4simple/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4platsupport/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4platsupport/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4platsupport/plat_include/bcm2837 -I/home/vagrant/sel4test/projects/util_libs/libplatsupport/include -I/home/vagrant/sel4test/projects/util_libs/libplatsupport/plat_include/bcm2837 -I/home/vagrant/sel4test/projects/util_libs/libplatsupport/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4simple-default/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4debug/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4debug/arch_include/arm -I/home/vagrant/sel4test/projects/seL4_libs/libsel4debug/sel4_arch_include/aarch32 -I/home/vagrant/sel4test/projects/util_libs/libelf/include -I/home/vagrant/sel4test/projects/util_libs/libcpio/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4test/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4sync/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4muslcsys/include -I/home/vagrant/sel4test/projects/sel4test/libsel4testsupport/include -I/home/vagrant/sel4test/projects/seL4_libs/libsel4serialserver/include -march=armv8-a+crc -marm   -D__KERNEL_32__ -g -nostdinc -fno-pic -fno-pie -fno-stack-protector -fno-asynchronous-unwind-tables -ftls-model=local-exec -mfloat-abi=softfp -Werror -g -std=gnu11 -MD -MT projects/sel4test/apps/sel4test-driver/CMakeFiles/sel4test-driver.dir/src/main.c.obj -MF projects/sel4test/apps/sel4test-driver/CMakeFiles/sel4test-driver.dir/src/main.c.obj.d -o projects/sel4test/apps/sel4test-driver/CMakeFiles/sel4test-driver.dir/src/main.c.obj -c /home/vagrant/sel4test/projects/sel4test/apps/sel4test-driver/src/main.c
/home/vagrant/sel4test/projects/sel4test/apps/sel4test-driver/src/main.c: In function ‘sel4test_run_tests’:
/home/vagrant/sel4test/projects/sel4test/apps/sel4test-driver/src/main.c:336:5: error: converting a packed ‘struct testcase’ pointer (alignment 1) to a ‘testcase_t’ {aka ‘struct testcase’} pointer (alignment 64) may result in an unaligned pointer value [-Werror=address-of-packed-member]
  336 |     int num_tests = collate_tests(__start__test_case, driver_tests, tests, 0, &reg, &skipped_tests);
      |     ^~~
In file included from /home/vagrant/sel4test/projects/sel4test/apps/sel4test-driver/src/main.c:37:
/home/vagrant/sel4test/projects/seL4_libs/libsel4test/include/sel4test/test.h:122:8: note: defined here
  122 | struct testcase {
      |        ^~~~~~~~
/home/vagrant/sel4test/projects/seL4_libs/libsel4test/include/sel4test/test.h:122:8: note: defined here
cc1: all warnings being treated as errors
[208/240] Building C object projects/s...est-driver.dir/src/arch/arm/arch.c.obj
ninja: build stopped: subcommand failed.
```

- エラーが変わった。
    ```
    `error: converting a packed ‘struct testcase’ pointer (alignment 1) to a ‘testcase_t’ {aka ‘struct testcase’} pointer (alignment 64) may result in an unaligned pointer value [-Werror=address-of-packed-member]`
    ```

## コンパイラオプションに`-Werror=address-of-packed-member`をつけて実行

結果は同じだった

```bash
$ cd ../projects/sel4test
$ git diff
diff --git a/apps/sel4test-driver/CMakeLists.txt b/apps/sel4test-driver/CMakeLists.txt
index 418a79f..a4abdbc 100644
--- a/apps/sel4test-driver/CMakeLists.txt
+++ b/apps/sel4test-driver/CMakeLists.txt
@@ -38,7 +38,7 @@ MakeCPIO(archive.o "$<TARGET_FILE:sel4test-tests>")
 add_executable(sel4test-driver EXCLUDE_FROM_ALL ${static} archive.o)
 target_include_directories(sel4test-driver PRIVATE "include")
 target_link_libraries(sel4test-driver Configuration muslc sel4 sel4allocman sel4vka sel4utils sel4test sel4platsupport sel4muslcsys sel4testsupport)
-target_compile_options(sel4test-driver PRIVATE -Werror -g)
+target_compile_options(sel4test-driver PRIVATE -Werror -Wno-address-of-packed-member -g)

 # Set this image as the rootserver
 DeclareRootserver(sel4test-driver)

$ cd build-rpi3
$ ../init-build.sh -DPLATFORM=rpi3 -DAARCH32=1
$ ninja
[36/37] Linking C executable elfloader-tool/elfloader
FAILED: elfloader-tool/elfloader
...
/usr/lib/gcc-cross/arm-linux-gnueabi/11/../../../../arm-linux-gnueabi/bin/ld: error: source object elfloader-tool/CMakeFiles/elfloader.dir/src/arch-arm/32/cpuid.c.obj has EABI version 5, but target elfloader-tool/elfloader has EABI version 0

$ vi projects/sel4test/apps/sel4test-driver/CMakeLists.txt
$ git diff
diff --git a/apps/sel4test-driver/CMakeLists.txt b/apps/sel4test-driver/CMakeLists.txt
index 418a79f..a4abdbc 100644
--- a/apps/sel4test-driver/CMakeLists.txt
+++ b/apps/sel4test-driver/CMakeLists.txt
@@ -38,7 +38,7 @@ MakeCPIO(archive.o "$<TARGET_FILE:sel4test-tests>")
 add_executable(sel4test-driver EXCLUDE_FROM_ALL ${static} archive.o)
 target_include_directories(sel4test-driver PRIVATE "include")
 target_link_libraries(sel4test-driver Configuration muslc sel4 sel4allocman sel4vka sel4utils sel4test sel4platsupport sel4muslcsys sel4testsupport)
-target_compile_options(sel4test-driver PRIVATE -Werror -g)
+target_compile_options(sel4test-driver PRIVATE -Werror -Wno-address-of-packed-member -g)

 # Set this image as the rootserver
 DeclareRootserver(sel4test-driver)

$ ninja
```

## gccの旧バージョン(gcc 10)を試す

結果は同じでEABIバージョンが違うというエラーとなる

```bash
$ sudo apt install gcc-10-arm-linux-gnueabi g++-10-arm-linux-gnueabi

## update-alternativesに登録して選択
$ sudo update-alternatives --install /usr/bin/arm-linux-gnueabi-gcc arm-linux-gnueabi-gcc /usr/bin/arm-linux-gnueabi-gcc-11 11
update-alternatives: using /usr/bin/arm-linux-gnueabi-gcc-11 to provide /usr/bin/arm-linux-gnueabi-gcc (arm-linux-gnueabi-gcc) in auto mode
$ sudo update-alternatives --install /usr/bin/arm-linux-gnueabi-gcc arm-linux-gnueabi-gcc /usr/bin/arm-linux-gnueabi-gcc-10 10
$ sudo update-alternatives --config arm-linux-gnueabi-gcc
There are 2 choices for the alternative arm-linux-gnueabi-gcc (providing /usr/bin/arm-linux-gnueabi-gcc).

  Selection    Path                               Priority   Status
------------------------------------------------------------
* 0            /usr/bin/arm-linux-gnueabi-gcc-11   11        auto mode
  1            /usr/bin/arm-linux-gnueabi-gcc-10   10        manual mode
  2            /usr/bin/arm-linux-gnueabi-gcc-11   11        manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
update-alternatives: using /usr/bin/arm-linux-gnueabi-gcc-10 to provide /usr/bin/arm-linux-gnueabi-gcc (arm-linux-gnueabi-gcc) in manual mode

$ arm-linux-gnueabi-gcc --version
arm-linux-gnueabi-gcc (Ubuntu 10.4.0-4ubuntu1~22.04) 10.4.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

$ sudo update-alternatives --install /usr/bin/arm-linux-gnueabi-g++ arm-linux-gnueabi-g++ /usr/bin/arm-linux-gnueabi-g++-11 11
update-alternatives: using /usr/bin/arm-linux-gnueabi-g++-11 to provide /usr/bin/arm-linux-gnueabi-g++ (arm-linux-gnueabi-g++) in auto mode
$ sudo update-alternatives --install /usr/bin/arm-linux-gnueabi-g++ arm-linux-gnueabi-g++ /usr/bin/arm-linux-gnueabi-g++-10 10
$ sudo update-alternatives --config arm-linux-gnueabi-g++
There are 2 choices for the alternative arm-linux-gnueabi-g++ (providing /usr/bin/arm-linux-gnueabi-g++).

  Selection    Path                               Priority   Status
------------------------------------------------------------
* 0            /usr/bin/arm-linux-gnueabi-g++-11   11        auto mode
  1            /usr/bin/arm-linux-gnueabi-g++-10   10        manual mode
  2            /usr/bin/arm-linux-gnueabi-g++-11   11        manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
update-alternatives: using /usr/bin/arm-linux-gnueabi-g++-10 to provide /usr/bin/arm-linux-gnueabi-g++ (arm-linux-gnueabi-g++) in manual mode

$ arm-linux-gnueabi-g++ --version
arm-linux-gnueabi-g++ (Ubuntu 10.4.0-4ubuntu1~22.04) 10.4.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## elfloader-tool/archive.oを直接修正

これでうまく行った。これをビルドプロセスのどこで指定するのかは今のところ不明。

```bash
$ arm-linux-gnueabi-readelf -h sel4test/build-rpi3/elfloader-tool/archive.o
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 61 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            ARM                              // ここも違う
  ABI Version:                       0
  Type:                              REL (Relocatable file)
  Machine:                           ARM
  Version:                           0x1
  Entry point address:               0x0
  Start of program headers:          0 (bytes into file)
  Start of section headers:          4424032 (bytes into file)
  Flags:                             0x0                              // これが問題
  Size of this header:               52 (bytes)
  Size of program headers:           0 (bytes)
  Number of program headers:         0
  Size of section headers:           40 (bytes)
  Number of section headers:         5
  Section header string table index: 4

$ arm-linux-gnueabi-readelf -h sel4/build-rpi/elfloader/archive.o
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              REL (Relocatable file)
  Machine:                           ARM
  Version:                           0x1
  Entry point address:               0x0
  Start of program headers:          0 (bytes into file)
  Start of section headers:          4872008 (bytes into file)
  Flags:                             0x5000000, Version5 EABI
  Size of this header:               52 (bytes)
  Size of program headers:           0 (bytes)
  Number of program headers:         0
  Size of section headers:           40 (bytes)
  Number of section headers:         9
  Section header string table index: 8


$ xxd sel4test/build-rpi3/elfloader-tool/archive.o

00000000: 7f45 4c46 0101 0161 0000 0000 0000 0000  .ELF...a........
                           ^^
00000010: 0100 2800 0100 0000 0000 0000 0000 0000  ..(.............
00000020: 6081 4300 0000 0000 3400 0000 0000 2800  `.C.....4.....(.
                           ^^
00000030: 0500 0400

$ xxd sel4/build-rpi/elfloader/archive.o

00000000: 7f45 4c46 0101 0100 0000 0000 0000 0000  .ELF............
00000010: 0100 2800 0100 0000 0000 0000 0000 0000  ..(.............
00000020: 4857 4a00 0000 0005 3400 0000 0000 2800  HWJ.....4.....(.
00000030: 0900 0800
```

elfヘッダーの`^`の部分をhexeditで直接変更でうまく行った。archive.oは

```bash
$ ninja
[2/2] Generating ../../../../images/sel4test-driver-image-arm-bcm2837          // imageの作成に成功
$ ls images
sel4test-driver-image-arm-bcm2837
```

raspiで実行するとエラーはあるが最後まで実行された[ログ](sel4_run.log)。

```bash
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
4582872 bytes read in 201 ms (21.7 MiB/s)
U-Boot> bootelf 0x10000000
## Starting application at 0x20000000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[20000000..2045ffff]
ELF-loading image 'kernel'
  paddr=[1000000..1037fff]
  vaddr=[e0000000..e0037fff]
  virt_entry=e0000000
ELF-loading image 'sel4test-driver'
  paddr=[1038000..13dffff]
  vaddr=[10000..3b7fff]
  virt_entry=4a0fc
Enabling MMU and paging
Jumping to kernel-image entry point...

DIDRv: 6, armv 80, coproc baseline only? no.
Bootstrapping kernel
Booting all finished, dropped to user space
Node 0 of 1
IOPT levels:     0
IPC buffer:      0x3b8000
Empty slots:     [974 --> 4096)
sharedFrames:    [0 --> 0)
userImageFrames: [16 --> 952)
userImagePaging: [12 --> 16)
untypeds:        [952 --> 974)
Initial thread domain: 0
Initial thread cnode size: 12
List of untypeds
------------------
Paddr    | Size   | Device
0x1000000 | 16 | 0
0x13e4000 | 14 | 0
0x13e8000 | 15 | 0
0x13f0000 | 16 | 0
0x1400000 | 22 | 0
0x1800000 | 23 | 0
0x2000000 | 24 | 0
0x3000000 | 25 | 0
0x5000000 | 25 | 0
0x7000000 | 23 | 0
0x7800000 | 22 | 0
0x7c00000 | 21 | 0
0x7e00000 | 20 | 0
0x7f00000 | 19 | 0
0x7f80000 | 18 | 0
0x7fc0000 | 17 | 0
0x7fe0000 | 15 | 0
0x7fe8000 | 13 | 0
0x3f300000 | 12 | 1
0x3f980000 | 12 | 1
0x3f215000 | 12 | 1
0x3f00b000 | 12 | 1
Untyped summary
4 untypeds of size 12
1 untypeds of size 13
1 untypeds of size 14
2 untypeds of size 15
2 untypeds of size 16
1 untypeds of size 17
1 untypeds of size 18
1 untypeds of size 19
1 untypeds of size 20
1 untypeds of size 21
2 untypeds of size 22
2 untypeds of size 23
1 untypeds of size 24
2 untypeds of size 25
Switching to a safer, bigger stack...
seL4 Test
=========

vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 2141
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1071
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 5361
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 2681
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1341
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 6711
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 3351
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1671
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 8381
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 4191
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 2091
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1041
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 5241
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 2621
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1311
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 6551
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 3271
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 1631
vka_alloc_object_at_maybe_dev@object.h:58 Failed to allocate object of size 8191

vspace_reserve_range_at@vspace.h:645 vspace is NULL
create_reservations@elf.c:254 Failed to make reservation: 0x10000, 794624
elf_reserve_regions_in_vspace@elf.c:447 Failed to create reservations
sel4utils_elf_reserve@elf.c:482 Failed to reserve regions
Starting test suite sel4test
Starting test 0: Test that there are tests
Starting test 1: SYSCALL0000
Starting test 2: SYSCALL0001
<<seL4(CPU 0) [decodeCNodeInvocation/53 T0xe6fea400 "sel4test-driver" @14440]: >
<<seL4(CPU 0) [decodeCNodeInvocation/53 T0xe6fea400 "sel4test-driver" @14440]: >

...

Starting test 123: VSPACE0000
Running test VSPACE0000 (Test threads in different cspace/vspace)
Test VSPACE0000 passed
Starting test 124: VSPACE0001
Running test VSPACE0001 (Test unmapping a page after deleting the PD)
Test VSPACE0001 passed
Starting test 125: VSPACE0002
Running test VSPACE0002 (Test create ASID pool)
<<seL4(CPU 0) [decodeARMMMUInvocation/2943 T0xe4012400 "VSPACE0002" @697e0]: AS>
Test VSPACE0002 passed
Starting test 127: Test all tests ran
Test suite passed. 127 tests passed. 39 tests disabled.
All is well in the universe

seL4 root server abort()ed
Debug halt syscall from user thread 0xe6fea400 "sel4test-driver"
halting...
Kernel entry via Unknown syscall, word: 523
```
