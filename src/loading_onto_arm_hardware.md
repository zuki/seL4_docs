# ARMハードウェア上での起動

ARMプラットフォームにはオペレーティングシステムを起動する方法が数多く
あります。通常、低レベルのROMコード（各デバイスに非常に固有）があり、
RAMを利用可能にし、OSの起動を行う第2段階のブートローダをロードするために
必要なクロックを有効にします。いくつかのステージでトラストゾーンやHYPモード
などを有効にするものもあります。

ROMの後の主なブートローダには、U-Boot、UEFI、Loki（Samsungデバイス用）、simplebootがあります。これらのほとんどはソフトウェアのロードを可能にする
USBを使ったFastbootを提供しています。

arm用のUEFI: http://blog.hansenpartnership.com/efitools-for-arm-released/。
arm用のU-Boot: http://www.denx.de/wiki/U-Boot

U-Bootから、SDカードやフラッシュから、FastbootやTFTPを使ってロードします。
ほとんどのアプリケーションには2つの部分があります。"kernel"部分はカーネルと
して扱い、"application"部分は初期ルートディスクとして扱います。イメージに
1つの部分しかない場合（たとえば、いくつかのプラットフォームのseL4test）、
それをカーネルとして扱います。

詳細な手順は、ボードによって異なります。一般的な説明については
[General Hardware Page](https://docs.sel4.systems/Hardware/index)を
参照してください。

## Fastboot

seL4が使用できるBeagle Boards以外のほとんどのARMプラットフォームは
Fastboot経由のブートをサポートしています。

Fastboot経由で起動するにはseL4のビルドシステムにより生成されたイメージ
ファイルをU-Bootイメージに変換する必要があります。

```bash
mkimage -A arm -a 0x48000000 -e 0x48000000 -C none -A arm -T kernel -O qnx -d INPUT_FILE OUTPUT_FILE
```

QNXを選んだ理由はseL4と同様にQNXもELFロードを想定していることを利用する
ためです。これに代わるものは`objcopy`を使ってELFファイルをバイナリファイルに
変換することです。使用するアドレスはボードによって異なります。ロードアドレスを
変更しない限り次の値を使用してください。

| プラットフォーム | アドレス |
|:-----------------|:---------|
| Arndale, Odroid-X, Odroid-XU | 0x48000000 |
| Sabre Lite | 0x30000000 |
| Panda, Panda ES | 0x80000000 |

イメージができたら、ボードをFastbootモードにし（U-Bootに割り込み、"fastboot"と
入力する）、実行します。

```bash
fastboot boot OUTPUT_FILE
```

### Beagle Board

Fastbootに対応したBeagle Board用のU-bootをコンパイルするか、標準のU-Bootと
dfu-utilを使用してイメージをボードに転送することができます。

ファイルをダウンロードするアドレスはU-Bootの環境変数`loadaddr`で制御します。
ELFファイルをダウンロードしてU-Bootのコマンドラインで`bootelf`を実行するか、
（`mkimage`で作成した）U-Bootのイメージファイルをダウンロードして`bootm`を
実行するかのいずれかで実行できます。ELFセクションやイメージ領域が
ELF/イメージ自体の位置と重ならないように、あるいは存在しないメモリアドレスに
ロードされないように注意する必要があるでしょう (オリジナルのBeagle Boardでは
0x81000000はうまくいきますが、0x90000000では動作しません。そこにはRAMがない
からです)。

```bash
dfu-util -D sel4test-image-arm
```

## SDカードからの起動

ボードからSDカードを取り出し、ビルドホストに接続されたSDカードリーダーに
セットします。SDカードの最初のパーティションにある（MS-DOS）ファイルシステムを
マウントし、イメージをそこにコピーします。ファイルシステムをアンマウントし、
カードをボードに戻します。ボードをリセットします（電源を入れ直すか、リセット
ボタンを押す）。イメージを実行するには次のようにします。

```bash
mmc init
mmcinfo
fatload mmc 0 ${loadaddr} sel4test-image-arm
bootelf ${loadaddr}
```

何があるかを見るには次のコマンドを実行します。

```bash
fatls mmc 0
```

ほとんどのU-Bootの実装ではその環境に適した`loadaddr`を定義しています。

## TFPTによる起動

DHCPサーバとTFTPサーバーのセットアップについてはこのドキュメントの範囲外ですが、
それらの設定を行い、まだ持っていなければTFTP対応のU-Bootをボードにインストール
します。

デバイスの電源を入れ、U-Bootの自動起動機能が有効な場合は何らかのキーを押して
それを停止させ、以下を入力します。

```bash
dhcp file address
bootelf address
```
