# gcc for ARM 32bit

## gcc-arm-none-eabi: newlib

```
$ apt show gcc-arm-none-eabi
Package: gcc-arm-none-eabi
Version: 15:10.3-2021.07-4
Priority: extra
Section: universe/devel
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Agustin Henze <tin@debian.org>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 432 MB
Depends: libc6 (>= 2.34), libgcc-s1 (>= 3.3.1), libgmp10, libisl23 (>= 0.15), libmpc3 (>= 1.1.0), libmpfr6 (>= 3.1.3), libstdc++6 (>= 5), zlib1g (>= 1:1.1.4), binutils-arm-none-eabi
Recommends: libnewlib-arm-none-eabi
Breaks: libnewlib-dev (<= 2.4.0.20160527-4), libstdc++-arm-none-eabi-newlib (<= 15:6.3.1+svn253039-1+10)
Homepage: https://developer.arm.com/open-source/gnu-toolchain/gnu-rm
Download-Size: 47.7 MB
APT-Manual-Installed: yes
APT-Sources: http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages
Description: GCC cross compiler for ARM Cortex-R/M processors
 Bare metal C and C++ compiler for embedded ARM chips using Cortex-M, and
 Cortex-R processors.
 This package is based on the GNU ARM toolchain provided by ARM.
$ which arm-none-eabi-gcc
/usr/bin/arm-none-eabi-gcc
$ arm-none-eabi-gcc --version
arm-none-eabi-gcc (15:10.3-2021.07-4) 10.3.1 20210621 (release)
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## gcc-arm-linux-gnueabi: libc, software fp

```
$ sudo apt show gcc-arm-linux-gnueabi
Package: gcc-arm-linux-gnueabi
Version: 4:11.2.0-1ubuntu1
Priority: optional
Section: universe/devel
Source: gcc-defaults (1.193ubuntu1)
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Debian GCC Maintainers <debian-gcc@lists.debian.org>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 25.6 kB
Depends: cpp-arm-linux-gnueabi (= 4:11.2.0-1ubuntu1), gcc-11-arm-linux-gnueabi (>= 11.2.0-1~)
Recommends: libc6-dev-armel-cross | libc-dev-armel-cross
Suggests: make, manpages-dev, autoconf, automake, libtool, flex, bison, gdb-arm-linux-gnueabi, gcc-doc
Download-Size: 1,230 B
APT-Manual-Installed: yes
APT-Sources: http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages
Description: GNU C compiler for the armel architecture
 This is the GNU C compiler, a fairly portable optimizing compiler for C.
 .
 This is a dependency package providing the default GNU C cross-compiler
 for the armel architecture.
$ which arm-linux-gnueabi-gcc
/usr/bin/arm-linux-gnueabi-gcc
$ arm-linux-gnueabi-gcc --version
arm-linux-gnueabi-gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0
Copyright (C) 2021 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## gcc-arm-linux-gnueabihf: libc, hardware fp

```
$ sudo apt show gcc-arm-linux-gnueabihf
Package: gcc-arm-linux-gnueabihf
Version: 4:11.2.0-1ubuntu1
Priority: optional
Section: devel
Source: gcc-defaults (1.193ubuntu1)
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Debian GCC Maintainers <debian-gcc@lists.debian.org>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 25.6 kB
Depends: cpp-arm-linux-gnueabihf (= 4:11.2.0-1ubuntu1), gcc-11-arm-linux-gnueabihf (>= 11.2.0-1~)
Recommends: libc6-dev-armhf-cross | libc-dev-armhf-cross
Suggests: make, manpages-dev, autoconf, automake, libtool, flex, bison, gdb-arm-linux-gnueabihf, gcc-doc
Download-Size: 1,242 B
APT-Sources: http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages
Description: GNU C compiler for the armhf architecture
 This is the GNU C compiler, a fairly portable optimizing compiler for C.
 .
 This is a dependency package providing the default GNU C cross-compiler
 for the armhf architecture.
 ```
 
# GCC for ARM 64bit

## gcc-aarch64-linux-gnu

```
$ sudo apt show gcc-aarch64-linux-gnu
Package: gcc-aarch64-linux-gnu
Version: 4:11.2.0-1ubuntu1
Priority: optional
Section: devel
Source: gcc-defaults (1.193ubuntu1)
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Debian GCC Maintainers <debian-gcc@lists.debian.org>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 25.6 kB
Depends: cpp-aarch64-linux-gnu (= 4:11.2.0-1ubuntu1), gcc-11-aarch64-linux-gnu (>= 11.2.0-1~)
Recommends: libc6-dev-arm64-cross | libc-dev-arm64-cross
Suggests: make, manpages-dev, autoconf, automake, libtool, flex, bison, gdb-aarch64-linux-gnu, gcc-doc
Download-Size: 1,242 B
APT-Manual-Installed: yes
APT-Sources: http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages
Description: GNU C compiler for the arm64 architecture
 This is the GNU C compiler, a fairly portable optimizing compiler for C.
 .
 This is a dependency package providing the default GNU C cross-compiler
 for the arm64 architecture.
$ which aarch64-linux-gnu-gcc
/usr/bin/aarch64-linux-gnu-gcc
$ aarch64-linux-gnu-gcc --version
aarch64-linux-gnu-gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0
Copyright (C) 2021 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```