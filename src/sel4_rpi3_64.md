# rpi3 64版の実行メモ

## v10.1.1: 作成できず

```bash
$ ninja
[1/39] Linking CXX executable projects...est/apps/sel4test-tests/sel4test-tests
FAILED: projects/sel4test/apps/sel4test-tests/sel4test-tests
: && ccache /usr/bin/aarch64-linux-gnu-g++ --sysroot=/home/vagrant/sel4test/build-rpi3-64  -march=armv8-a+crc   -D__KERNEL_64__ -nostdinc++ -g -D__KERNEL_64__   -static -nostdlib -z max-page-size=0x1000 -u __vsyscall_ptr /home/vagrant/sel4test/build-rpi3-64/lib/crt1.o /home/vagrant/sel4test/build-rpi3-64/lib/crti.o /usr/lib/gcc-cross/aarch64-linux-gnu/11/crtbegin.o projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/arch/arm/arch.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/helpers.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/main.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/benchmark_api.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/binding.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/breakpoints.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/cache.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/cnodeops.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/cspace.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/domains.cxx.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/endpoints.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/ept.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/faults.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/fpu.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/frames.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/inc_untyped.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/interrupt.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/ioports.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/iopt.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/ipc.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/multicore.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/nbwait.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/pagetables.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/preempt.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/regressions.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/schedcontext.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/scheduler.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/serial_server.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/sync.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/threads.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/tls.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/trivial.c.obj projects/sel4test/apps/sel4test-tests/CMakeFiles/sel4test-tests.dir/src/tests/vspace.c.obj -lgcc -lgcc_eh  libsel4/libsel4.a  projects/seL4_libs/libsel4allocman/libsel4allocman.a  projects/seL4_libs/libsel4vka/libsel4vka.a  projects/seL4_libs/libsel4utils/libsel4utils.a  projects/seL4_libs/libsel4test/libsel4test.a  projects/seL4_libs/libsel4sync/libsel4sync.a  projects/seL4_libs/libsel4muslcsys/libsel4muslcsys.a  projects/sel4test/libsel4testsupport/libsel4testsupport.a  projects/seL4_libs/libsel4serialserver/libsel4serialserver.a  projects/seL4_libs/libsel4test/libsel4test.a  projects/seL4_libs/libsel4utils/libsel4utils.a  projects/util_libs/libelf/libelf.a  projects/util_libs/libcpio/libcpio.a  projects/seL4_libs/libsel4sync/libsel4sync.a  projects/seL4_libs/libsel4platsupport/libsel4platsupport.a  projects/util_libs/libplatsupport/libplatsupport.a  projects/seL4_libs/libsel4simple-default/libsel4simple-default.a  projects/seL4_libs/libsel4vspace/libsel4vspace.a  projects/seL4_libs/libsel4simple/libsel4simple.a  projects/seL4_libs/libsel4vka/libsel4vka.a  projects/seL4_libs/libsel4debug/libsel4debug.a  libsel4/libsel4.a  projects/util_libs/libutils/libutils.a  projects/musllibc/build-temp/lib/libc.a     -lgcc -lgcc_eh  libsel4/libsel4.a  projects/seL4_libs/libsel4allocman/libsel4allocman.a  projects/seL4_libs/libsel4vka/libsel4vka.a  projects/seL4_libs/libsel4utils/libsel4utils.a  projects/seL4_libs/libsel4test/libsel4test.a  projects/seL4_libs/libsel4sync/libsel4sync.a  projects/seL4_libs/libsel4muslcsys/libsel4muslcsys.a  projects/sel4test/libsel4testsupport/libsel4testsupport.a  projects/seL4_libs/libsel4serialserver/libsel4serialserver.a  projects/seL4_libs/libsel4test/libsel4test.a  projects/seL4_libs/libsel4utils/libsel4utils.a  projects/util_libs/libelf/libelf.a  projects/util_libs/libcpio/libcpio.a  projects/seL4_libs/libsel4sync/libsel4sync.a  projects/seL4_libs/libsel4platsupport/libsel4platsupport.a  projects/util_libs/libplatsupport/libplatsupport.a  projects/seL4_libs/libsel4simple-default/libsel4simple-default.a  projects/seL4_libs/libsel4vspace/libsel4vspace.a  projects/seL4_libs/libsel4simple/libsel4simple.a  projects/seL4_libs/libsel4vka/libsel4vka.a  projects/seL4_libs/libsel4debug/libsel4debug.a  libsel4/libsel4.a  projects/util_libs/libutils/libutils.a  projects/musllibc/build-temp/lib/libc.a /usr/lib/gcc-cross/aarch64-linux-gnu/11/crtend.o /home/vagrant/sel4test/build-rpi3-64/lib/crtn.o -o projects/sel4test/apps/sel4test-tests/sel4test-tests && :
/usr/lib/gcc-cross/aarch64-linux-gnu/11/../../../../aarch64-linux-gnu/bin/ld: /usr/lib/gcc-cross/aarch64-linux-gnu/11/libgcc.a(lse-init.o): in function `init_have_lse_atomics':
(.text.startup+0xc): undefined reference to `__getauxval'
collect2: error: ld returned 1 exit status
ninja: build stopped: subcommand failed.
```

`getauxval`はmuslllibcにある。ldのオプション設定の問題だと思われるが解決法はわからず。

```
$ cd build-rpi3-64/projects/musllibc/build-temp/lib
$ aarch64-linux-gnu-nm libc.a | grep getauxval
getauxval.o:
0000000000000000 T getauxval
```

## v12.1.0: 作成成功

```bash
$ repo init -u https://github.com/seL4/seL4test-manifest.git -b refs/tags/12.1.0
Downloading Repo source from https://gerrit.googlesource.com/git-repo

... A new version of repo (2.32) is available.
... New version is available at: /home/vagrant/sel4_12.1/.repo/repo/repo
... The launcher is run from: /usr/bin/repo
!!! The launcher is not writable.  Please talk to your sysadmin or distro
!!! to get an update installed.


Your identity is: SUZUKI Keiji <zuki.ebetsu@gmail.com>
If you want to change this, please re-run 'repo init' with --config-name

repo has been initialized in /home/vagrant/sel4_12.1

$ repo sync

... A new version of repo (2.32) is available.
... New version is available at: /home/vagrant/sel4_12.1/.repo/repo/repo
... The launcher is run from: /usr/bin/repo
!!! The launcher is not writable.  Please talk to your sysadmin or distro
!!! to get an update installed.

remote: Enumerating objects: 660, done.
remote: Counting objects: 100% (516/516), done.
remote: Compressing objects: 100% (111/111), done.
remote: Total 660 (delta 412), reused 507 (delta 405), pack-reused 144
Fetching: 100% (10/10), done in 21.742s
Checking out: 100% (10/10), done in 0.564s
repo sync has finished successfully.

$ mkdir build-rpi3-64 && cd build-rpi3-64/
$ ../init-build.sh -DPLATFORM=rpi3 -DAARCH64=1
loading initial cache file /home/vagrant/sel4_12.1/projects/sel4test/settings.cmake
-- Set platform details from PLATFORM=rpi3
--   KernelPlatform: bcm2837
--   KernelARMPlatform: rpi3
-- Setting from flags KernelSel4Arch: aarch64
-- Found seL4: /home/vagrant/sel4_12.1/kernel
-- The C compiler identification is GNU 11.3.0
-- The CXX compiler identification is GNU 11.3.0
-- The ASM compiler identification is GNU
-- Found assembler: /usr/bin/aarch64-linux-gnu-gcc
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/aarch64-linux-gnu-gcc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/aarch64-linux-gnu-g++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found elfloader-tool: /home/vagrant/sel4_12.1/tools/seL4/elfloader-tool
-- /home/vagrant/sel4_12.1/build-rpi3-64/kernel/gen_headers/plat/machine/devices_gen.h is out of date. Regenerating...
interrupts for device /soc/serial@7e215040
WARNING:root:Not sure how to parse interrupts for "/soc/interrupt-controller@7e00b200"
interrupts for device /soc/interrupt-controller@7e00b200
interrupts for device /soc/local_intc@40000000
interrupts for device /timer
-- CPIO test cpio_reproducible_flag PASSED
-- Found musllibc: /home/vagrant/sel4_12.1/projects/musllibc
-- Found util_libs: /home/vagrant/sel4_12.1/projects/util_libs
-- Found seL4_libs: /home/vagrant/sel4_12.1/projects/seL4_libs
-- Found sel4_projects_libs: /home/vagrant/sel4_12.1/projects/sel4_projects_libs
-- Found sel4runtime: /home/vagrant/sel4_12.1/projects/sel4runtime
-- Performing Test compiler_arch_test
-- Performing Test compiler_arch_test - Success
-- libmuslc architecture: 'aarch64' (from KernelSel4Arch 'aarch64')
-- Detecting cached version of: musllibc
-- Found Git: /usr/bin/git (found version "2.34.1")
--   Not found cache entry for musllibc - will build from source
-- Found PythonInterp: /usr/bin/python (found version "3.10.6")
CMake Warning (dev) at /usr/share/cmake-3.22/Modules/FindPackageHandleStandardArgs.cmake:438 (message):
  The package name passed to `find_package_handle_standard_args` (NANOPB)
  does not match the name of the calling package (Nanopb).  This can lead to
  problems in calling code that expects `find_package` result variables
  (e.g., `_FOUND`) to follow a certain pattern.
Call Stack (most recent call first):
  /home/vagrant/sel4_12.1/tools/nanopb/extra/FindNanopb.cmake:340 (FIND_PACKAGE_HANDLE_STANDARD_ARGS)
  /home/vagrant/sel4_12.1/tools/seL4/cmake-tool/helpers/nanopb.cmake:29 (find_package)
  /home/vagrant/sel4_12.1/projects/sel4_projects_libs/libsel4nanopb/CMakeLists.txt:11 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- Found NANOPB: /home/vagrant/sel4_12.1/tools/nanopb
-- Configuring done
-- Generating done
-- Build files have been written to: /home/vagrant/sel4_12.1/build-rpi3-64
$ ninja
[302/302] Generating ../../images/sel4test-driver-image-arm-bcm2837   # stderr出力はninja_err.log参照
$ ls
apps            CMakeFiles           gcc.cmake  launch_gdb  nanopb
build.ninja     cmake_install.cmake  images     lib         simulate
CMakeCache.txt  elfloader            kernel     libsel4
$ ls images
sel4test-driver-image-arm-bcm2837
$ file images/sel4test-driver-image-arm-bcm2837
images/sel4test-driver-image-arm-bcm2837: data
```

# 64bit版u-bootの作成

- ubuntu2004上で作業
- 32ビット版同様、`multiple definitions`対策のコード修正
- 32ビット版同様、キャッシュを無効にするためのパッチ適用

```bash
$ cd u-boot
$ git checkout v2023.04 -b v2023.04
Updating files: 100% (17622/17622), done.
Switched to a new branch 'v2023.04'
$ make CROss_COMPILE=aarch64-linux-gnu- rpi_3_b_plus_defconfig
  HOSTCC  scripts/basic/fixdep
  HOSTCC  scripts/kconfig/conf.o
  YACC    scripts/kconfig/zconf.tab.c
  LEX     scripts/kconfig/zconf.lex.c
  HOSTCC  scripts/kconfig/zconf.tab.o
  HOSTLD  scripts/kconfig/conf
#
# configuration written to .config
#
$ make CROSS_COMPILE=aarch64-linux-gnu-
scripts/kconfig/conf  --syncconfig Kconfig
  CFG     u-boot.cfg
  GEN     include/autoconf.mk
  ....
  LD      u-boot
  OBJCOPY u-boot.srec
  OBJCOPY u-boot-nodtb.bin
  RELOC   u-boot-nodtb.bin
  COPY    u-boot.bin
  SYM     u-boot.sym
===================== WARNING ======================
CONFIG_OF_EMBED is enabled. This option should only
be used for debugging purposes. Please use
CONFIG_OF_SEPARATE for boards in mainline.
See doc/develop/devicetree/control.rst for more info.
====================================================
  OFCHK   .config
$ ls -l u-boot.bin
-rw-rw-r-- 1 vagrant vagrant 596568 Jun  1 10:53 u-boot.bin
```

## 実行: 失敗

```bash
U-Boot 2023.04 (Jun 01 2023 - 10:52:47 +0900)

DRAM:  948 MiB
RPI 3 Model B+ (0xa020d3)
Core:  66 devices, 14 uclasses, devicetree: embed
MMC:   mmc@7e202000: 0, mmc@7e300000: 1
Loading Environment from FAT... Unable to read "uboot.env" from mmc0:1...
In:    serial
Out:   vidconsole
Err:   vidconsole
Net:   No ethernet found.
starting USB...
Bus usb@7e980000: USB DWC2
scanning bus usb@7e980000 for devices... 4 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
Hit any key to stop autoboot:  0
switch to partitions #0, OK
mmc0 is current device
Scanning mmc 0:1...
Found U-Boot script /boot.scr
151 bytes read in 4 ms (36.1 KiB/s)
## Executing script at 02400000
4714744 bytes read in 207 ms (21.7 MiB/s)
## No elf image at address 0x10000000               # 0x10000000でELFファイルが見つからない
SCRIPT FAILED: continuing...
Card did not respond to voltage select! : -110
No EFI system partition
No EFI system partition
Failed to persist EFI variables
BootOrder not defined
EFI boot manager: Cannot load any image
Card did not respond to voltage select! : -110
MMC Device 2 not found
no mmc device at slot 2

Device 0: unknown device
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
BOOTP broadcast 1
DHCP client bound to address 192.168.10.106 (3 ms)
*** ERROR: `serverip' not set
Cannot autoload with TFTPGET
missing environment variable: pxeuuid
Retrieving file: pxelinux.cfg/01-b8-27-eb-ab-e8-48
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
*** ERROR: `serverip' not set
Retrieving file: pxelinux.cfg/C0A80A6A
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
*** ERROR: `serverip' not set
Retrieving file: pxelinux.cfg/C0A80A6
lan78xx_eth Waiting for PHY auto negotiation to complete..
```

- AARCH64の場合は、sel4test-driver-image-arm-bcm2837はbinary形式でelfではない
- ファイル形式は`tools/seL4/cmake-tool/helpers/application_settings.cmake`で指定していた

## elf形式を試す: 失敗

```diff
$ cd tools/seL4 && git diff
diff --git a/cmake-tool/helpers/application_settings.cmake b/cmake-tool/helpers/application_settings.cmake
index 6205c40..af33d5b 100644
--- a/cmake-tool/helpers/application_settings.cmake
+++ b/cmake-tool/helpers/application_settings.cmake
@@ -21,7 +21,7 @@ function(ApplyData61ElfLoaderSettings kernel_platform kernel_sel4_arch)
         set(ElfloaderImage "uimage" CACHE STRING "" FORCE)
         #rpi3
     elseif(${kernel_platform} STREQUAL "bcm2837" AND ${kernel_sel4_arch} STREQUAL "aarch64")
-        set(ElfloaderImage "binary" CACHE STRING "" FORCE)
+        set(ElfloaderImage "elf" CACHE STRING "" FORCE)
         #rpi4
     elseif(${kernel_platform} STREQUAL "bcm2711" AND ${kernel_sel4_arch} STREQUAL "aarch64")
         set(ElfloaderImage "efi" CACHE STRING "" FORCE)
```

```
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
4819032 bytes read in 211 ms (21.8 MiB/s)
U-Boot> bootelf 0x10000000        // すぐにプロンプトが帰り、何も実行されない
U-Boot> md 0x10000000
10000000: 464c457f 00010102 00000000 00000000  .ELF............
10000010: 00b70002 00000001 00a24000 00000000  .........@......
10000020: 00000040 00000000 00498358 00000000  @.......X.I.....
..
U-Boot> md 0xa24000
00a24000: 90000073 913fc273 9100027f 14000bf5  s...s.?.........     // ?
00a24010: 140015a2 d0000093 9101c273 f9400660  ........s...`.@.
00a24020: 9100001f f9400261 d61f0020 035f5555  ....a.@. ...UU_.
..
U-Boot> md 0x10a24000
10a24000: 55455555 15574515 11547555 57551555  UUEU.EW.UuT.U.UW     // 範囲外
10a24010: 555455d3 54775555 51555555 55d571d5  .UTUUUwTUUUQ.q.U
10a24020: 55555155 07155555 5c4d1555 551557d3  UQUUUU..U.M\.W.U
```

```
$ aarch64-none-elf-readelf -h sel4test-driver-image-arm-bcm2837
ELF Header:
...
  Type:                              EXEC (Executable file)
  Machine:                           AArch64
  Version:                           0x1
  Entry point address:               0xa24000
  Start of program headers:          64 (bytes into file)
  Start of section headers:          4817752 (bytes into file)
  Flags:                             0x0
```

## binaryに戻す

u-bootでbinary形式のimageを実行する方法

- bootelfはだめ
- bootiもだめ
- goでOK

### `booti 0x10000000`

```bash
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
4714744 bytes read in 208 ms (21.6 MiB/s)
U-Boot> booti 0x10000000
Bad Linux ARM64 Image magic!
```

### `go 0x10000000`

[実行ログ](sel4_1210_run.log)

```bash
U-Boot> fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
4714744 bytes read in 207 ms (21.7 MiB/s)
U-Boot> go 0x10000000
## Starting application at 0x10000000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[a24000..ea30f7]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at b158f8.
Loaded DTB from b158f8.
   paddr=[23c000..23ffff]
ELF-loading image 'kernel' to 0
  paddr=[0..23bfff]
  vaddr=[ffffff8000000000..ffffff800023bfff]
  virt_entry=ffffff8000000000
ELF-loading image 'sel4test-driver' to 240000
  paddr=[240000..625fff]
  vaddr=[400000..7e5fff]
  virt_entry=40f088
Enabling MMU and paging
Jumping to kernel-image entry point...

Bootstrapping kernel
...
Starting test suite sel4test
Starting test 0: Test that there are tests
Starting test 1: SYSCALL0000
Starting test 2: SYSCALL0001
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
<<seL4(CPU 0) [decodeCNodeInvocation/54 T0xffffff8007fd0400 "sel4test-driver" @404c54]: CNodeCap: Illega>
Starting test 3: SYSCALL0002
...
Starting test 125: VSPACE0005
Running test VSPACE0005 (Test overassigning ASID pool)
Test VSPACE0005 passed
Starting test 126: VSPACE0006
Running test VSPACE0006 (Test touching all available ASID pools)
Test VSPACE0006 passed
Starting test 128: Test all tests ran
Test suite passed. 128 tests passed. 41 tests disabled.
All is well in the universe

```

## `boot.scr`による自動実行も成功

```bash
$ cd <U-BOOT DIR>
$ vi boot.txt
$ cat boot.txt
fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
go 0x10000000
$ tools/mkimage -A arm -O linux -T script -C none -n boot.scr -d boot.txt boot.scr
Image Name:   boot.scr
Created:      Thu Jun  1 11:07:52 2023
Image Type:   ARM Linux Script (uncompressed)
Data Size:    87 Bytes = 0.08 KiB = 0.00 MiB
Load Address: 00000000
Entry Point:  00000000
Contents:
   Image 0: 79 Bytes = 0.08 KiB = 0.00 MiB
$ cp boot.scr /Volume/NO NAME/
```

```bash
U-Boot 2023.04-dirty (Jun 01 2023 - 15:53:38 +0900)

DRAM:  948 MiB
RPI 3 Model B+ (0xa020d3)
Core:  66 devices, 14 uclasses, devicetree: embed
MMC:   mmc@7e202000: 0, mmc@7e300000: 1
Loading Environment from FAT... Unable to read "uboot.env" from mmc0:1...
In:    serial
Out:   vidconsole
Err:   vidconsole
Net:   No ethernet found.
starting USB...
Bus usb@7e980000: USB DWC2
scanning bus usb@7e980000 for devices... 4 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
Hit any key to stop autoboot:  0
switch to partitions #0, OK
mmc0 is current device
Scanning mmc 0:1...
Found U-Boot script /boot.scr                         # boot.scrを見つけて実行
146 bytes read in 4 ms (35.2 KiB/s)
## Executing script at 02400000
4714744 bytes read in 208 ms (21.6 MiB/s)
## Starting application at 0x10000000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[a24000..ea30f7]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at b158f8.
Loaded DTB from b158f8.
   paddr=[23c000..23ffff]
ELF-loading image 'kernel' to 0
  paddr=[0..23bfff]
  vaddr=[ffffff8000000000..ffffff800023bfff]
  virt_entry=ffffff8000000000
ELF-loading image 'sel4test-driver' to 240000
  paddr=[240000..625fff]
  vaddr=[400000..7e5fff]
  virt_entry=40f088
Enabling MMU and paging
Jumping to kernel-image entry point...

Bootstrapping kernel
```

# `build-rpi3-64/elfloader/`の内容

```bash
$ file archive.archive.o.cpio
archive.archive.o.cpio: ASCII cpio archive (SVR4 with no CRC)
$ cpio -tv < archive.archive.o.cpio
-rwxrwxr-x   1 root     root       915312 Jan  1  1970 kernel.elf
-rw-rw-r--   1 root     root        13596 Jan  1  1970 kernel.dtb
-rwxrwxr-x   1 root     root      3644400 Jan  1  1970 sel4test-driver
8934 blocks
$ file archive.o
archive.o: ELF 64-bit LSB relocatable, ARM aarch64, version 1 (SYSV), not stripped
$ file elfloader
elfloader: ELF 64-bit LSB executable, ARM aarch64, version 1 (SYSV), statically linked, with debug_info, not stripped
$ aarch64-linux-gnu-readelf -lW elfloader

Elf file type is EXEC (Executable file)
Entry point 0xa24000
There are 3 program headers, starting at offset 64

Program Headers:
  Type           Offset   VirtAddr           PhysAddr           FileSiz  MemSiz   Flg Align
  LOAD           0x000000 0x0000000000a20000 0x0000000000a20000 0x00f7f0 0x00f7f0 R E 0x10000
  LOAD           0x010000 0x0000000000a30000 0x0000000000a30000 0x4730f8 0x4730f8 RW  0x10000
  GNU_STACK      0x000000 0x0000000000000000 0x0000000000000000 0x000000 0x000000 RW  0x10

 Section to Segment mapping:
  Segment Sections...
   00     .start .text .rodata .eh_frame
   01     .bss ._archive_cpio .data _driver_list
   02
$ aarch64-linux-gnu-objdump -t elfloader | grep _text
0000000000a24000 g       .start	0000000000000000 _text
```
