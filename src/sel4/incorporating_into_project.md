# プロジェクトにビルドシステムを組み込む

ここのページではCMakeベースのビルドシステムについて詳しく説明し、
新しいプロジェクトにビルドシステムを組み込むために必要な情報を提供します。
プロジェクトのニーズや希望するカスタマイズに応じて、さまざまな方法で
組み合わせることができる種々のピースが存在します。これは複数のリポジトリに
分散しているCMakeファイルの分割に反映されています。

## 基本構造

ビルドシステムは2つのピースがあります。一つは[seL4カーネルリポジトリ](https://github.com/seL4/seL4)に
あり、コンパイラツールチェーンとフラグ設定、コンフィギュレーション生成の
ためのヘルパーが含まれています。もう1つは[seL4_tools/cmake-tool](https://github.com/seL4/seL4_tools/tree/master/cmake-tool)に
あり、ライブラリとバイナリを（カーネルと一緒に）最終的なシステムイメージに
結合するためのヘルパーが含まれています。

この構造はカーネルがそれ自体のビルドに完全に責任を持ち、ビルドシステムの
残りの部分で使用するための設定とバイナリをエクスポートすることを意味します。

`cmake-tool`ディレクトリには以下の注目すべきファイルがあります。

- `default-CMakeLists.txt`: `CMakeLists.txt`の例であり、プロジェクトの
  最上位ディレクトリに置く`CMakeLists.txt`として使用することができます。
  `cmake-tool`リポジトリが`tools`という名前のディレクトリにあるという
  ディレクトリ構造を前提として、このファイルは単に`all.cmake`をインクルード
  しているだけです。このファイルを使用するには、[プロジェクトマニフェスト](https://docs.sel4.systems/RepoCheatsheet)は
  トップレベルのプロジェクトディレクトリに`CMakeLists.txt`という名前で
  このファイルへのシンボリックリンクを作成することが期待されています。
- `all.cmake`: `base.cmake`, `projects.cmake`, `configuration.cmake`を
  インクルードしているラッパーです。この3つのファイルを変更しない
  プロジェクトはこのファイルを使用することができます。
- `base.cmake`: seL4プロジェクトのビルドに必要な基本的なビルドターゲット
  (`kernel`、`libsel4`、`elfloader-tool`)をコンパイルフラグ、ヘルパールーチンを
  共に構築します。`add_subdirectory`ルーチンなどを通じてプロジェクト内で
  さらなるターゲット用のベースとして利用することができます。
- `projects.cmake`: デフォルトのプロジェクトレイアウトを想定して
  `add_subdirectory`を通じてビルドターゲットを追加します。基本的には
  プロジェクトディレクトリのサブディレクトリにある`CMakeLists.txt`ファイルを
  すべて追加します。
- `configuration.cmake`: 従来の`autoconf.h`ヘッダーをエミュレートする
  `Configuration`と呼ばれるライブラリのターゲットを提供します。`autoconf.h`
  ヘッダーにはプロジェクト*全体*の設定変数が含まれていたため、このルールは
  構成空間に追加される他のすべてのターゲットやスクリプトの後に置く必要が
  あります。
- `common.cmake`: `base.cmake`によりインクルードされるファイルで、汎用の
  ヘルパールーチンを持ちます。
- `flags.cmake`: `base.cmake`によりインクルードされるファイルで、ビルド
  フラグとリンカの呼び出しを設定します。
- `init-build.sh`: 新しいCMakeビルドディレクトリの初期設定と生成を行う
  シェルスクリプトです。
- `helpers/*`: `common.cmake`により共通にインポートされるヘルパー関数です。

## カーネルディレクトリ

`base.cmake`ファイルはseL4カーネルが特定の場所にあることを想定しています。
次のような例を考えます。

```bash
wesome_system/
├── kernel/
│   └── CMakeLists.txt
├── projects/
│   ├── awesome_system/
│   │   └── CMakeLists.txt
│   └── seL4_libs/
│       └── CMakeLists.txt
├── tools/
│   └── cmake-tool/
│       ├── base.cmake
│       ├── all.cmake
│       └── default-CMakeLists.txt
├── .repo/
└── CMakeLists.txt -> tools/cmake-tool/default-CMakeLists.txt
```

CMakeのビルドディレクトリを初期化するルートソースディレクトリとして
`awesome_system/`を使用し、`tools/cmake-tool/all.cmake`を使用する場合、
`base.cmake`はカーネルが`awesome_system/kernel`にあることを期待します。

カーネルは、以下に説明するように、別の場所に置くこともできます。

```bash
awesome_system/
├── seL4/
│   └── CMakeLists.txt
├── projects/
│   ├── awesome_system/
│   │   └── CMakeLists.txt
│   └── seL4_libs/
│       └── CMakeLists.txt
├── tools/
│   └── cmake-tool/
│       ├── base.cmake
│       ├── all.cmake
│       └── default-CMakeLists.txt
├── .repo/
└── CMakeLists.txt -> tools/cmake-tool/default-CMakeLists.txt
```

この例ではカーネルは`seL4`というディレクトリにあるため、`cmake`を実行する際に
`-DKERNEL_PATH=seL4`を引数とすることでデフォルトのカーネル配置場所を
オーバーライドすることができます。

## 高度な構造

その他のプロジェクトレイアウトも使用可能です。次のような例を考えてみます。

```bash
awesome_system/
├── seL4/
│   └── CMakeLists.txt
├── awesome/
│   └── CMakeLists.txt
├── seL4_libs/
│   └── CMakeLists.txt
├── buildsystem/
│   └── cmake-tool/
│       ├── base.cmake
│       ├── all.cmake
│       └── default-CMakeLists.txt
└── .repo/
```

この例は以下の点でデフォルトとは異なります。

- ルートディレクトリに`CMakeLists.txt`ファイルがありません
- `tools`ディレクトリが`buildsystem`にリネームされています
- `kernel`ディレクトリが`seL4`にリネームされています
- `projects`ディレクトリが省略されています

以下、このようなプロジェクト構造を実現する方法を説明します。

C`MakeLists.txt`を`awesome_system/awesome`ディレクトリに置き、CMakeを
初期化する場合、`awesome_system`ディレクトリにビルドディレクトリがあると
仮定して、以下のように実行します。

```bash
sel4@host:~/awesome_directory$ cmake -DCROSS_COMPILER_PREFIX=toolchain-prefix -DCMAKE_TOOLCHAIN_FILE=../seL4/gcc.cmake -DKERNEL_PATH=../seL4 -G Ninja ../awesome
```

重要なのは`CMAKE_TOOLCHAIN_FILE`のパスはCMakeにより直ちに解決されるので
ビルドディレクトリからの相対パスであるのに対し、`KERNEL_PATH`は
`awesome_system/awesome/CMakeLists.txt`の処理中に解決されるのでその
ディレクトリからの相対パスである点です。

`awesome_system/awesome/CMakeLists.txt`の内容は、以下のようになります。

```bash
cmake_minimum_required(VERSION 3.7.2)
include(../buildsystem/cmake-tool/base.cmake)
add_subdirectory(../seL4_libs seL4_libs)
include(../buildsystem/cmake-tool/configuration.cmake)
```

これは`projects.cmake`をインクルードしないことを除けば`all.cmake`とほとんど
同じです。プロジェクトフォルダを持たないからです。`projects.cmake`はどの
ファイルも解決しないのでインクルードしても不要です。基本フラグと環境の設定から
Configurationライブラリの完成までの間に特定のサブディレクトリ（この例では
seL4_libs）をインクルードする必要があるため、`all.cmake`をインクルードする
ことはできません。ルートソースディレクトリのサブディレクトリではない
ディレクトリを指定するため、明示的にビルドディレクトリを指定する
（`add_subdirectory`の第2引数）必要がありました。

簡単のために、カーネルパスはプロジェクトのトップレベルの`CMakeLists.txt`に
直接書くことができます。これを実現するには、次の行

```bash
set(KERNEL_PATH ../seL4)
```

を`awesome_system/awesome/CMakeLists.txt`の

```bash
include(../buildsystem/cmake-tool/base.cmake)
```

の前に指定する必要があります。これにより`cmake`を実行する際に`-DKERNEL_PATH`を
指定する必要がなくなります。

## 構成

レガシービルドシステムとの互換性のために、以下を実現する様々なヘルパーや
システムが存在します。

- `cmake-gui`に表示される様々な種類の依存関係を持つ構成変数を自動化する
- これらの変数をレガシービルドシステムと同様のフォーマットで宣言する
  Cスタイルの設定ヘッダーを生成する
- `autoconf.h`ヘッダーを生成し、`#include <autoconf.h>`を使用するレガシー
  コードが動作するようにする

次のCMakeスクリプトの断片はこれら3つがどのように組み合わされているかを
示しています。

```bash
set(configure_string "")
config_option(EnableAwesome HAVE_AWESOME "Makes library awesome" DEFAULT ON)
add_config_library(MyLibrary "${configure_string}")
generate_autoconf(MyLibraryAutoconf "MyLibrary")
target_link_libraries(MyLibrary PUBLIC MyLibrary_Config)
target_link_libraries(LegacyApplication PRIVATE MyLibrary MyLibraryAutoconf)
```

この例を1行ずつ説明します。

- `set(configure_string "")`: 様々な`config_*`ヘルパーが自動的にこの変数に
  追加されるため、`configure_string`を空白で初期化します
- `config_option(EnableAwesome HAVE_AWESOME "Makes library awesome" DEFAULT ON)`:
  CMakeスクリプトや`cmake-gui`では`EnableAwesome`として、Cヘッダーでは
  `CONFIG_HAVE_AWESOME`として表示される設定変数を宣言します。
- `add_config_library(MyLibrary "${configure_string}")`: `MyLibrary_Config`
  ターゲットを生成します。これは構成文字列を元に生成されるCヘッダーを持つ
  [インタフェースライブラリ](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries)です。
  また、構成ライブラリのグローバルリストに`MyLibrary`を追加します。このリストは
  「システム内のすべての構成」（レガシービルドシステムにおける`autoconf.h`）を
  含むライブラリの生成に使用することができます。
- `generate_autoconf(MyLibraryAutoconf "MyLibrary")`: `MyLibraryAutoconf`
  ターゲットを生成します。これは`MyLibrary_Config`に依存する
  [インタフェースライブラリ](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries)で
  あり、`MyLibrary_Config`にある構成ヘッダーを含む`autoconf.h`ファイルを
  提供します。
- `target_link_libraries(MyLibrary PUBLIC MyLibrary_Config)`:
  `#include <MyLibrary/gen_config.h>`とすることで`MyLibrary`が生成された
  構成ヘッダーを`#include`できるようにします。
- `target_link_libraries(LegacyApplication PRIVATE MyLibrary MyLibraryAutoconf)`:
  `LegacyApplication`が`MyLibraryAutoconf`から`#include <autoconf.h>`できる
  ようにします。この場合、`autoconf.h`は`#include <MyLibrary/gen_config.h>`を
  含むことになります。

様々な`config_*`ヘルパーの詳細については
[kernel/tools/helpers.cmake](https://github.com/seL4/seL4/blob/master/tools/helpers.cmake)の関数に書かれているコメントを読んでください。

## ヘルパー関数

seL4プロジェクトで再利用するためのCMake関数がいくつか存在します。以下で
説明します。

### カーネルが提供するヘルパー関数

上で説明したすべてのヘルパー関数はseL4リポジトリの`tools/helpers.cmake`で
提供されています。このファイルにあるその他の関数はカーネルのビルド自体にだけ
有用なものです。

### `cmake-tool`が提供するヘルパー関数

これらのヘルパー関数はユーザーレベルのプロジェクト用に提供されており、
`common.cmake`と`helpers/`のすべてのファイルにあります。注目すべき関数は
以下の通りです。

- `DeclareRootserver(rootserver_target)`: CMakeの実行ファイル`rootserver_target`
  をシステムのルートサーバーとして宣言します。これはプロジェクトで一度だけ
  使用でき、以下のことを行います。
    - ターゲットのビルドフラグの変更
    - チェーンローディングのために必要な追加ターゲットの作成
    - 最終的なバイナリイメージを`images`に作成するrootserver_imag`e`
      ターゲットの作成
- `MakeCPIO:` 入力ファイルのリストからリンク可能なCPIOアーカイブを作成する
  ためのルールを宣言します。
- `GenerateSimulateScript`: `simulate_gen`という名のターゲットを作成します。
  これはターゲットプラットフォームがサポートされている場合、[QEMU](https://www.qemu.org/)
  でプロジェクトをシミュレートするための`./simulate`シェルスクリプトを
  ビルドディレクトリに作成します。この関数を使用する場合、システム構成が
  シミュレーション可能であることを確認する責任はアプリケーションにあります。
  その他、生成されたシミュレーションコマンドをアプリケーションのCMakeスクリプトで
  カスタマイズできるようにする`SetSimulationScriptProperty`などの関数も提供
  されています。
- `ApplyCommonSimulationSettings`: シミュレーションと互換性のない機能を無効に
  するためにカーネルシステム構成の変更を試みます。
- `ApplyCommonReleaseVerificationSettings(release、verification)`: 'release'
  ビルド（パフォーマンスを最適化するビルド）と'verification'（検証に適した機能）
  ビルドで異なる組み合わせのフラグを設定します。これらのオプションの詳細に
  ついては[メタ構成オプション](https://docs.sel4.systems/projects/buildsystem/using.html#Meta-configuration-options)を参照してください。

### その他で提供されているヘルパー関数

[Camkes](https://docs.sel4.systems/CAmkES)、
[Camkes x86 VMM](https://docs.sel4.systems/CAmkESVM)、
[Rumprun](https://github.com/seL4/rumprun-sel4-demoapps)などのプロジェクトが
アプリケーションが自分自身で設定できるように追加のヘルパー関数を提供している
場合があります。一般にヘルパースクリプトは`helpers.cmake`の変種であり、それを
使用するCMakeスクリプトでインクルードする必要があります。
