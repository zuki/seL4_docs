# Raspberry Pi 3 Model B, B+

[オリジナル](https://docs.sel4.systems/Hardware/Rpi3.html)

実験用のポートであり、動作を保証するものではありません。

## ハードウェア

[RPI-3B-V1_2-SCHEMATIC-REDUCED.pdf](https://www.raspberrypi.org/documentation/hardware/raspberrypi/schematics/RPI-3B-V1_2-SCHEMATIC-REDUCED.pdf)

| 機能 | 部品 | データシート |
|:-----|:-----|:-------------|
| USB * ETH | SMSC LAN9514 | [9514.pdf](http://ww1.microchip.com/downloads/en/DeviceDoc/9514.pdf) |
| SoC | BCM2837 |

## Raspberry Pi 3B/3B+上でseL4を実行する

私達のブログの特集記事["seL4 on the Raspberry Pi 3"](https://research.csiro.au/tsblog/sel4-raspberry-pi-3/)も
参照してください（他のブログ記事もチェックしてみてください！）。

## シリアル接続

シリアルTXとRXはそれぞれGPIOピン14と15にあります（上のハードウェア
回路図を参照してください）。

## リセット

拡張ヘッダーとUSBソケットの間にある"RUN"と書かれたジャンパーをショート
させるとCPUのソフトリセットが実行されます。

## U-Boot

現在、デフォルトのU-BootではseL4 ELFLoaderのキャッシュ設定の問題により
RPi3上でseL4イメージを正常に起動できません。この問題は、seL4をロードする
前にU-bootのキャッシュを無効にすることで修正できます。以前の純正U-bootは
カーネルイメージをロードする前にキャッシュを無効にしていましたが、
残念ながら[このパッチ](https://github.com/u-boot/u-boot/commit/995eab8b5b580b67394312b1621c60a71042cd18)に
よりU-bootはキャッシュを無効にしなくなりました。

キャッシュを無効にするU-bootバイナリを入手する方法は、U-Bootをソースから
コンパイルするか、[すでに動作しているこのビルド済みのU-Bootバイナリイメージを使用する](https://sel4.systems/Info/Docs/u-boot-working-rpi3-32bit-v2017.11.bin)か
のいずれかです。

U-bootをビルドする場合は以下のようにU-bootをアップストリームから
クローンして、コミット "995eab8b5b580b67394312b1621c60a71042cd18" を
リバートしてからビルドしてください。

```
git clone https://github.com/u-boot/u-boot.git u-boot
cd u-boot
git revert 995eab8b5b580b67394312b1621c60a71042cd18
make CROSS_COMPILE=arm-linux-gnueabi- rpi_3_32b_defconfig
make CROSS_COMPILE=arm-linux-gnueabi-
```

これによりRPi3上でseL4を正常に起動する最新のU-bootを入手することが
できます。

**注意:** 最新バージョンのu-bootを使用している場合、自動リバートでは
うまくいきません。チェンジセットを探して手動で変更を戻す必要があります。

構成定義`rpi_3_3b_defconfig`を使ってu-bootをビルドすると、Raspberry PI
Model 3Bと3B+でseL4を起動できるイメージが作成されるはずです。残念ながら
3B+の場合、このバージョンのu-bootではtftpブートは動作しません（`tftp`
コマンドは`No Ethernet found`というエラーを報告します）。この場合、3B+に
特化したイメージをビルドする必要があります。残念ながら、提供されている
3B+モデル用のdefconfigファイル (`rpi_3_b_plus_defconfig`) は正しく
ビルドされないようです。

Raspberry PI Model 3B+でtftpブートを使用するには3B用のdefconfig（上記の
通り）を使用し、`.config`の`CONFIG_DEFAULT_DEVICE_TREE`パラメータ

```
CONFIG_DEFAULT_DEVICE_TREE="bcm2837-rpi-3-b"
```

を、次のように変更してください。

```
CONFIG_DEFAULT_DEVICE_TREE="bcm2837-rpi-3-b-plus"
```

これで生成されたイメージはTFTP用にオンボードEThernetデバイスを使える
はずです。

## SDカードの設定

PIはSDカード上の最初のFAT32パーティションから起動します。ファイルが
指定されている場合はこのパーティションのルートディレクトリに配置する
必要があります。

| ステージ | ファイル名 | 説明 | ソース |
|:---------|:-----------|:-----|:-------|
| FSBL | - | SDをマウントしてSSBLをロードする | ROM |
| SSBL | bootcode.bin | GPUファームウェアをロードしてGPUを起動する | https://github.com/raspberrypi/firmware/tree/master/boot |
| GPUファームウェア | start.elf または recovery.elf | GPUブートローダをロードしてCPUを起動する | https://github.com/raspberrypi/firmware/tree/master/boot |
| 通常はLinuxカーネルだがu-bootも可 | u-boot.bin | [ビルド済みのU-boot](https://sel4.systems/Info/Docs/u-boot-working-rpi3-32bit-v2017.11.bin)か上の指示によりビルドしたU-bootのいずれか |
| | config.txt | u-bootパラメタ | `enable_uart=1`と`kernel=u-boot.bin`を追加する（例: http://codepad.org/ykVYFSyP） |
| | uboot.env | u-boot保存環境 | u-boot（デフォルト環境）により生成される。bootcmdをbootcmd_origにコピーして、bootcmdとbootdelayを削除 |

## Raspberry Pi 3にseL4をインストールする

このセクションではRPi3でカーネルイメージを起動させるための最も便利な2つの
方法について説明します。いずれの方法もseL4Testなどのプロジェクトを使って
カーネルイメージをすでにビルドしていることを前提としています：

[seL4Test](https://docs.sel4.systems/seL4Test)に説明されているように
repoを使ってsel4testプロジェクトをチェックアウトします。

```
repo init -u https://github.com/seL4/sel4test-manifest.git
repo sync
mkdir cbuild
cd cbuild
../init-build.sh -DPLATFORM=rpi3 -DAARCH32=1
# デフォルトのcmakeラッパーはターゲットプラットフォーム用の
# デフォルト設定を行います。
# 個々の設定を変更するには、`ccmake`を実行し、必要に応じて
# 設定パラメータを変更してください。
ninja
```

生成されたバイナリは`images/`ディレクトリにあります。

ここで説明する方法はSDカードを使う方法とTFTPを使う方法の2つです。

どちらの方法もSDカードを使用してRPi3ファームウェアとそれがロードする
U-bootブートローダを提供する必要があります。RPi3はブートローダを
フラッシュメモリに保存しないので、SDカードにあるブートローダを探します。
必要な基本セットについては上のSDカードの用意方法についてのセクションを
参照してください。SDカードに必要なファイルやその入手方法については上の
表で確認してください。

### SDカード

マイクロSDカードに必要なファイルをコピーして準備します（前のセクションを
参照してください）。

続いて、seL4イメージ（seL4testイメージなど）をSDカードのルート
ディレクトリにコピーしてください。それだけです。SDカードをPCから取り
出してRPi3に挿入し、RPi3の電源を入れてください。

RPi3が起動したら、必ずブートプロセスを中断して、U-bootコマンド
プロンプトを表示させます。U-bootコマンドプロンプトから次のように
入力します: `fatls mmc 0`。seL4イメージファイルの名前が表示されない
場合は、これまでの手順を再確認し、何か忘れていることがないか確認する
必要があります。ファイル名が表示されたら、次のように操作してください。

```
fatload mmc 0 0x10000000 sel4test-driver-image-arm-bcm2837
bootelf 0x10000000
```

### TFTP

内蔵SDカードにU-BootとRPi3ファームウェアに必要なファイルをセット
アップしたことを確認してください（上記の表を参照してください）。
次に、TFTPサーバーを立ち上げ、seL4イメージがTFTPサーバーから提供されて
いることを確認します。そして次のように入力してください。

```
usb start
dhcp
tftp 0x10000000 <YOUR_TFTP_SERVER_IP_ADDRESS>:sel4test-driver-image-arm-bcm2837
bootelf 0x10000000
```

Raspberry PI Model 3B+を使用している場合は上記のように正しいu-boot
イメージを構築していることを確認してください。

DHCPではなく静的IPを使用するには、以下を使用します。

```
usb start
setenv ipaddr <RPI_IP_ADDRESS>
setenv serverip <YOUR_TFTP_SERVER_IP_ADDRESS>
tftp 0x10000000 sel4test-driver-image-arm-bcm2837
bootelf 0x10000000
```
