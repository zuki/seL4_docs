# seL4の実行メモ

```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install repo
$ repo version
<repo not installed>
repo launcher version 2.17
       (from /usr/bin/repo)
git 2.34.1
Python 3.10.6 (main, Mar 10 2023, 10:55:28) [GCC 11.3.0]
OS Linux 5.15.0-72-generic (#79-Ubuntu SMP Wed Apr 19 08:22:18 UTC 2023)
CPU x86_64 (x86_64)
Bug reports: https://bugs.chromium.org/p/gerrit/issues/entry?template=Repo+tool+issue
$ which ninja
/usr/bin/ninja
$ mkdir -p sel4/seL4test && cd sel4/seL4test
$ repo init -u https://github.com/seL4/sel4test-manifest.git
Downloading Repo source from https://gerrit.googlesource.com/git-repo
repo: Updating release signing keys to keyset ver 2.3

... A new version of repo (2.32) is available.
... New version is available at: /home/vagrant/sel4/seL4test/.repo/repo/repo
... The launcher is run from: /usr/bin/repo
!!! The launcher is not writable.  Please talk to your sysadmin or distro
!!! to get an update installed.


Your identity is: SUZUKI Keiji <zuki.ebetsu@gmail.com>
If you want to change this, please re-run 'repo init' with --config-name

Testing colorized output (for 'repo diff', 'repo status'):
  black    red      green    yellow   blue     magenta   cyan     white
  bold     dim      ul       reverse
Enable color display in this user account (y/N)? y

repo has been initialized in /home/vagrant/sel4/seL4test
$ repo sync

... A new version of repo (2.32) is available.
... New version is available at: /home/vagrant/sel4/seL4test/.repo/repo/repo
... The launcher is run from: /usr/bin/repo
!!! The launcher is not writable.  Please talk to your sysadmin or distro
!!! to get an update installed.

remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 2 (delta 2), pack-reused 2
Fetching: 100% (10/10), done in 23.296s
repo sync has finished successfully.
$ ls
easy-settings.cmake  griddle  init-build.sh  kernel  projects  tools
$ which qemu-system-x86_64
/usr/bin/qemu-system-x86_64
$ mkdir build-x86 && cd build-x86
$ ../init-build.sh -DPLATFORM=x86_64 -DSIMULATION=TRUE
loading initial cache file /home/vagrant/sel4/seL4test/projects/sel4test/settings.cmake
-- Set platform details from PLATFORM=x86_64
--   KernelPlatform: pc99
--   KernelSel4Arch: x86_64
-- Found seL4: /home/vagrant/sel4/seL4test/kernel
-- The C compiler identification is GNU 11.3.0
-- The CXX compiler identification is GNU 11.3.0
-- The ASM compiler identification is GNU
-- Found assembler: /usr/bin/gcc
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/gcc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/g++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found elfloader-tool: /home/vagrant/sel4/seL4test/tools/seL4/elfloader-tool
-- Found musllibc: /home/vagrant/sel4/seL4test/projects/musllibc
-- Found util_libs: /home/vagrant/sel4/seL4test/projects/util_libs
-- Found seL4_libs: /home/vagrant/sel4/seL4test/projects/seL4_libs
-- Found sel4_projects_libs: /home/vagrant/sel4/seL4test/projects/sel4_projects_libs
-- Found sel4runtime: /home/vagrant/sel4/seL4test/projects/sel4runtime
-- Performing Test compiler_arch_test
-- Performing Test compiler_arch_test - Success
-- libmuslc architecture: 'x86_64' (from KernelSel4Arch 'x86_64')
-- Detecting cached version of: musllibc
-- Found Git: /usr/bin/git (found version "2.34.1")
--   Not found cache entry for musllibc - will build from source
-- Found Nanopb: /home/vagrant/sel4/seL4test/tools/nanopb
-- CPIO test cpio_reproducible_flag PASSED
-- Configuring done
-- Generating done
-- Build files have been written to: /home/vagrant/sel4/seL4test/build-x86
$ ninja
/home/vagrant/sel4/seL4test/kernel/tools/xmllint.sh: line 14: xmllint: command not found
$ sudo apt install libxml2-utils
$ which xmllint
/usr/bin/xmllint
$ ninja
ModuleNotFoundError: No module named 'past'
$ sudo apt install python3-future
$ ninja
ModuleNotFoundError: No module named 'ply'
$ sudo apt install python3-ply
$ ninja
FileNotFoundError: [Errno 2] No such file or directory: 'protoc'
$ sudo apt install python3-protobuf
$ sudo apt install protobuf-compiler
$ ninja
[62/62] Generating ../../images/sel4test-driver-image-x86_64-pc99
$ ./simulate
```

[simulate.log](simulate.log)
