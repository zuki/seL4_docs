# u-boot

## 環境変数

```
U-Boot> printenv
arch=arm
baudrate=115200
board=rpi
board_name=3 Model B+
board_rev=0xD
board_rev_scheme=1
board_revision=0xA020D3
boot_a_script=load ${devtype} ${devnum}:${distro_bootpart} ${scriptaddr} ${pref}
boot_efi_binary=load ${devtype} ${devnum}:${distro_bootpart} ${kernel_addr_r} ei
boot_efi_bootmgr=if fdt addr -q ${fdt_addr_r}; then bootefi bootmgr ${fdt_addr_i
boot_extlinux=sysboot ${devtype} ${devnum}:${distro_bootpart} any ${scriptaddr}}
boot_net_usb_start=usb start
boot_prefixes=/ /boot/
boot_script_dhcp=boot.scr.uimg
boot_scripts=boot.scr.uimg boot.scr
boot_syslinux_conf=extlinux/extlinux.conf
boot_targets=mmc0 mmc1 mmc2 usb0 pxe dhcp
bootcmd=run distro_bootcmd
bootcmd_dhcp=devtype=dhcp; run boot_net_usb_start; if dhcp ${scriptaddr} ${boot;
bootcmd_mmc0=devnum=0; run mmc_boot
bootcmd_mmc1=devnum=1; run mmc_boot
bootcmd_mmc2=devnum=2; run mmc_boot
bootcmd_pxe=run boot_net_usb_start; dhcp; if pxe get; then pxe boot; fi
bootcmd_usb0=devnum=0; run usb_boot
bootdelay=2
cpu=armv8
dhcpuboot=usb start; dhcp u-boot.uimg; bootm
distro_bootcmd=for target in ${boot_targets}; do run bootcmd_${target}; done
efi_dtb_prefixes=/ /dtb/ /dtb/current/
ethaddr=xx:xx:xx:xx:xx:xx
fdt_addr=2eff7700
fdt_addr_r=0x02600000
fdt_high=ffffffffffffffff
fdtcontroladdr=f8e10
fdtfile=broadcom/bcm2837-rpi-3-b-plus.dtb
initrd_high=ffffffffffffffff
kernel_addr_r=0x00080000
load_efi_dtb=load ${devtype} ${devnum}:${distro_bootpart} ${fdt_addr_r} ${prefi}
loadaddr=0x1000000
mmc_boot=if mmc dev ${devnum}; then devtype=mmc; run scan_dev_for_boot_part; fi
preboot=usb start
pxefile_addr_r=0x02500000
ramdisk_addr_r=0x02700000
scan_dev_for_boot=echo Scanning ${devtype} ${devnum}:${distro_bootpart}...; for;
scan_dev_for_boot_part=part list ${devtype} ${devnum} -bootable devplist; env et
scan_dev_for_efi=setenv efi_fdtfile ${fdtfile}; for prefix in ${efi_dtb_prefixee
scan_dev_for_extlinux=if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefi
scan_dev_for_scripts=for script in ${boot_scripts}; do if test -e ${devtype} ${e
scriptaddr=0x02400000
serial#=000000004aabe848
serverip=192.168.10.103
soc=bcm283x
stderr=serial,vidconsole
stdin=serial,usbkbd
stdout=serial,vidconsole
usb_boot=usb start; if usb dev ${devnum}; then devtype=usb; run scan_dev_for_boi
usbethaddr=xx:xx:xx:xx:xx:xx
vendor=raspberrypi
```
