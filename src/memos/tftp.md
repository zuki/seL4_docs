# tftpからのロード

## macでtftpdを起動

```bash
$ sudo launchctl load -w /System/Library/LaunchDaemons/tftp.plist
$ sudo lsof -i:69
COMMAND PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
launchd   1 root   14u  IPv6 0x2d6e58317b992a97      0t0  UDP *:tftp
launchd   1 root   15u  IPv4 0x2d6e58317b992d87      0t0  UDP *:tftp
launchd   1 root   17u  IPv6 0x2d6e58317b992a97      0t0  UDP *:tftp
launchd   1 root   18u  IPv4 0x2d6e58317b992d87      0t0  UDP *:tftp
```

## seL4イメージをttfpdにコピー

```bash
$ sudo cp sel4test-driver-image-arm-bcm2837 /private/tftpboot/
```

## u-bootからロード

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
U-Boot> usb start
U-Boot> dhcp
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
BOOTP broadcast 1
DHCP client bound to address 192.168.10.106 (2 ms)
*** ERROR: `serverip' not set
Cannot autoload with TFTPGET
U-Boot> tftp 0x10000000 192.168.10.103:sel4test-driver-image-arm-bcm2837
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
Using lan78xx_eth device
TFTP from server 192.168.10.103; our IP address is 192.168.10.106
Filename 'sel4test-driver-image-arm-bcm2837'.
Load address: 0x10000000
Loading: ##################################################  4.6 MiB
         5.8 MiB/s
done
Bytes transferred = 4817152 (498100 hex)
U-Boot> go 0x10000000
## Starting application at 0x10000000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[1a33000..1ecb0ff]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at 1b285e8.
Loaded DTB from 1b285e8.
   paddr=[123c000..123ffff]
ELF-loading image 'kernel' to 1000000
  paddr=[1000000..123bfff]
  vaddr=[ffffff8001000000..ffffff800123bfff]
  virt_entry=ffffff8001000000
ELF-loading image 'sel4test-driver' to 1240000
  paddr=[1240000..1635fff]
  vaddr=[400000..7f5fff]
  virt_entry=40f150
Enabling MMU and paging
Jumping to kernel-image entry point...

Bootstrapping kernel
available phys memory regions: 1
  [1000000..8000000]
reserved virt address space regions: 3
  [ffffff8001000000..ffffff800123c000]
  [ffffff800123c000..ffffff800123f593]
  [ffffff8001240000..ffffff8001636000]
Booting all finished, dropped to user space
spt_handle_irq@spt.c:166 handle irq called when no interrupt pending
...
```

## boot.scrによる自動化

```bash
$ vi boot.txt
$ cat boot.txt
setenv serverip 192.168.10.103
usb start
dhcp
tftp 0x10000000 sel4test-driver-image-arm-bcm2837
go 0x10000000

$ mkimage -A arm -D linux -T script -C none -n boot.scr -d boot.txt boot.scr
$ cp boot.scr <SD_FAT_dir>
$ minicom

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
Found U-Boot script /boot.scr
183 bytes read in 4 ms (43.9 KiB/s)
## Executing script at 02400000
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
BOOTP broadcast 1
DHCP client bound to address 192.168.10.106 (3 ms)
*** Warning: no boot file name; using 'C0A80A6A.img'
Using lan78xx_eth device
TFTP from server 192.168.10.103; our IP address is 192.168.10.106
Filename 'C0A80A6A.img'.
Load address: 0x1000000
Loading: *
TFTP error: 'File not found' (1)
Not retrying...
lan78xx_eth Waiting for PHY auto negotiation to complete....... done
Using lan78xx_eth device
TFTP from server 192.168.10.103; our IP address is 192.168.10.106
Filename 'sel4test-driver-image-arm-bcm2837'.
Load address: 0x10000000
Loading: ##################################################  4.6 MiB
         5.9 MiB/s
done
Bytes transferred = 4817152 (498100 hex)
## Starting application at 0x10000000 ...

ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[1a33000..1ecb0ff]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at 1b285e8.
Loaded DTB from 1b285e8.
   paddr=[123c000..123ffff]
ELF-loading image 'kernel' to 1000000
  paddr=[1000000..123bfff]
  vaddr=[ffffff8001000000..ffffff800123bfff]
  virt_entry=ffffff8001000000
ELF-loading image 'sel4test-driver' to 1240000
  paddr=[1240000..1635fff]
  vaddr=[400000..7f5fff]
  virt_entry=40f150
Enabling MMU and paging
Jumping to kernel-image entry point...
...
```

## tftpdを停止

```bash
$ sudo launchctl unload -w /System/Library/LaunchDaemons/tftp.plist
```
