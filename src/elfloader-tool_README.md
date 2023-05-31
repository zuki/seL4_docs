<!--
     Copyright 2020, Data61, CSIRO (ABN 41 687 119 230)

     SPDX-License-Identifier: GPL-2.0-only
-->
# Elfloader

elfloaderはARMとRISC-V上のseL4用のハードウェアを準備する役割を担っています。
組み込みCPIOアーカイブからカーネルとユーザイメージをロードし、（SMPが
有効な場合は）セカンダリコアを初期化し、カーネル用のページテーブルの初期
セットをセットアップします。

## ARM

ARMプラットフォームでは、elfloaderは4つのブート方法（バイナリイメージ、u-boot uImage、
ELFファイル、EFI実行ファイル）をサポートしています。これらの方法はそれぞれ若干
異なります。また、ブートローダのDTB、あるいは組み込みCPIOアーカイブに含まれている
DTBのいずれかをseL4に提供することもできます。

1. (EFIのみ) Elfloaderは`_gnuefi_start`エントリポイントに入ります。
2. (EFIのみ) Elfloaderは自身を再配置します。
3. Elfloaderの`_start`が呼び出されます。これは`arch-arm/<arch_bitness>/crt0.S`にあります。
4. Elfloaderは[ドライバフレームワーク](#ドライバフレームワーク)を初期化し、UART/printfを
   有効にします。
5. Elfloaderはカーネル、ユーザイメージ、DTBをロードし、カーネルをメモリのどこにマッピング
   する必要があるかを決定します。
6. カーネルウィンドウがelfloaderのコードに重なる場合:
    * (AArch32 EFI のみ) elfloaderは自身を再配置します。再配置ロジックの詳細な説明は`relocate_below_kernel`を参照してください。
    * (その他のプラットフォーム) elfloader はアボートします。
7. エルフローダーはブートを再開します。自体を再配置した場合はドライバモデルを再初期化します。
8. elfloaderがHYPモードであるがseL4がHYPモードをサポートするように構成されていない場合、
   HYPモードから離脱します。
9. elfloaderはカーネル用の初期ページテーブルを設定します（`init_hyp_boot_vspace`または
   `init_boot_vspace`を参照）。
10. SMP が有効な場合、elfloaderはすべてのセカンダリコアを起動します。
11. elfloaderはMMUを有効にします。
12. elfloaderはseL4を起動し、ユーザイメージとDTBに関する情報を渡します。

### バイナリ

elfloaderは`shoehorn`ユーティリティによって生成されたベースアドレスで
実行されることを期待します。与えられたイメージの正しいアドレスは以下を
カーネルビルドディレクトリで実行することで確認できます。

```bah
aarch64-linux-gnu-objdump -t elfloader/elfloader | grep _text
```

出力の最初のフィールドにベースアドレスが含まれています。

aarch64では、elfloaderは自分自身を正しいアドレスに移動しようとしますが、
ロードアドレスと正しいアドレスが近すぎぎると、再配置コードが上書きされるために
これは失敗します。

CMakeのIMAGE_START_ADDRを設定することにより`shoehorn`をオーバーライドして
ロードアドレスをハードコードすることも可能です。

### U-Boot

elfloaderはARM/ARM64をLinuxカーネルのブート規約に従ってブートすることができます。
DTBが提供された場合はseL4に渡されます（その後、seL4はルートタスクにDTBを渡します）。

### ELF

elfloaderはELFイメージとして実行することをサポートします（U-Bootの`bootelf`などを介して）。

### EFI

elfloaderは`gnu-efi`プロジェクトに基づくEFIサポートを統合しています。elfloaderは
自身を適当な場所に再配置します。また、EFI実装からのDTBのロードをサポートします。

## RISC-V

RISC-Vのelfloaderは基本的にARMプラットフォームを踏襲しています。しかし、
利用可能なプラットフォームが少ないため、現在アクティブにサポートされているのは
2つの方法（ELFファイルまたはバイナリイメージとしてビルドする）だけです。
いずれの場合も、プラットフォームは[SBI](https://github.com/riscv/riscv-sbi-doc)
実装を提供する必要があります。elfloaderはこれをログ出力チャネルとマルチコアブート
に使用します。seL4ビルドシステムはelfloaderをペイロードとする[`OpenSBI`](https://github.com/riscv/opensbi)
をビルドすることができます。[`bbl`](https://github.com/riscv/riscv-pk)のサポートは
`OpenSBI`に取って代わられたため、削除されました。

## ドライバフレームワーク

elfloaderはプラットフォーム間でコードの重複を減らすためにドライバフレームワークを
提供しています。現在、ドライバフレームワークはUART出力にしか使用されていませんが、
拡張性を念頭に置いて設計されています。実際には、現在はARMでしか使用されていません。
RISC-VはUARTにSBIを使用し、SBIにはデバイスツリーエントリがないからです。しかし、
将来的にはRISC-Vでも有用なものになるかもしれません。

ドライバフレームワークはseL4に含まれている`hardware_gen.py`ユーティリティにより
生成されるデバイスリストを含むヘッダーファイルを使用します。現在のところ、この
ヘッダーにはDTBの`stdout-path`プロパティで指定されたUARTだけが含まれています。
リスト内の各デバイスは互換文字列 (`compat`) と、DTBの`reg`プロパティに
より指定される領域に対応するアドレスのリスト (`region_bases[]`) を持ちます。

elfloaderの各ドライバはデバイスツリー内で一致するものを探すするための互換文字列の
リストを持っています。たとえば、TegraとTIプラットフォームで使用される8250 UART
ドライバは次のリストを持っています。

```c
static const struct dtb_match_table uart_8250_matches[] = {
    { .compatible = "nvidia,tegra20-uart" },
    { .compatible = "ti,omap3-uart" },
    { .compatible = "snps,dw-apb-uart" },
    { .compatible = NULL /* sentinel */ },
};
```

各ドライバは'type'を持ちます。現在のところ、サポートされているtypeは
`DRIVER_UART`だけです。`type`は、各ドライバオブジェクトの`ops`ポインタにある
構造体のタイプを示しており、タイプ固有の機能を提供します。(たとえば、UART
ドライバは`putc`関数を含む`elfloader_uart_ops`構造体を持ちます)。
ドライバは`init`関数も提供します。これは、ドライバがデバイスにマッチしたときに
呼び出され、デバイス固有の設定 (たとえば、デバイスをUART出力として設定) の実行に
使用することができます。

最後に、各ドライバは`struct elfloader_driver`と対応する`ELFLOADER_DRIVER`文を
持ちます。再び8250 UARTドライバを例に取ると次のようになっています。

```c
static const struct elfloader_driver uart_8250 = {
    .match_table = uart_8250_matches,
    .type = DRIVER_UART,
    .init = &uart_8250_init,
    .ops = &uart_8250_ops,
};

ELFLOADER_DRIVER(uart_8250);
```

#### UART

ドライバフレームワークは`plat_console_putchar`の「デフォルト」(`__attribute__((weak))`)の
実装を提供しています。これは`uart_set_out`として指定されたelfloaderデバイスの`putc`関数を
呼び出します。また、`uart_set_out`が呼ばれる前に与えられたすべての文字を破棄します。
 (ごく初期のデバッグ用などで)ドライバフレームワークを使用したくない場合はこれを上書きする
 ことができます。

## elfloaderの移植

### ARMへの移植

カーネルのポートが開始（し、DTBが提供）されたら、elfloaderのプラットフォームへの
移植は非常に簡単です。

利用可能な物理メモリ範囲など、プラットフォーム固有のほとんど情報はDTBから抽出されます。
プラットフォームが他のプラットフォームと互換性のあるUARTを使用している場合、そのUARTは
何もせずに動作します。その他のケースでは、既存のドライバに新しい`dtb_match_table`
エントリを追加するか、新しいドライバを追加する必要があるかもしれません（後者の場合も
極めて簡単であり、既存のドライバの`match_table`と`putchar`関数を変更する必要がある
だけです）。

適切なイメージタイプを選択する必要があります。デフォルトでは`ElfloaderImage`は`elf`に
設定されていますが、様々なプラットフォーム固有のオーバーライドが存在し、このリポジトリにある
`cmake-tool/helpers/application_settings.cmake`の`ApplyData61ElfLoaderSettings`でみる
ことができます。

### RISC-Vへの移植

TODO - elfloader側で行うべきことは、実はそれほど多くないようです。
