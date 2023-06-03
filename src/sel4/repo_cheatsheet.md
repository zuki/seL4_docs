# Repoチートシート

RepoはAndroid Open Source Projectが開発したツールです。プロジェクトでは
Repoをソース依存関係の管理に使用しています。

このページでは一般的なマニフェストの構成法について説明し、
ワークフローで使用している共通のRepoコマンドについても説明します。

以下は、sel4testプロジェクト用のマニフェストの例です。これらのファイルは
https://github.com/seL4/sel4test-manifest/ にあります。

- `common.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
     Copyright 2018, Data61, CSIRO
     SPDX-License-Identifier: BSD-2-Clause
-->
<manifest>
<remote name="seL4" fetch="." />
<remote fetch="../sel4proj" name="sel4proj"/>
<remote fetch="https://github.com/nanopb" name="nanopb" />
<remote fetch="https://github.com/riscv" name="opensbi"/>

<default revision="master" remote="seL4" />

<project name="seL4_tools.git" path="tools/seL4">
    <linkfile src="cmake-tool/init-build.sh" dest="init-build.sh"/>
    <linkfile src="cmake-tool/griddle" dest="griddle"/>
</project>
<project name="sel4runtime.git" path="projects/sel4runtime"/>
<project name="musllibc.git" path="projects/musllibc" revision="sel4"/>
<project name="seL4_libs.git" path="projects/seL4_libs"/>
<project name="util_libs.git" path="projects/util_libs"/>
<project name="sel4test.git" path="projects/sel4test">
    <linkfile src="easy-settings.cmake" dest="easy-settings.cmake"/>
</project>
<project name="sel4_projects_libs" path="projects/sel4_projects_libs" />
<project name="opensbi" remote="opensbi" revision="refs/tags/v0.8" path="tools/opensbi"/>
<project name="nanopb" path="tools/nanopb" revision="refs/tags/0.4.3" upstream="master" remote="nanopb"/>
</manifest>
```

- `master.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
     Copyright 2018, Data61, CSIRO
     SPDX-License-Identifier: BSD-2-Clause
-->
<manifest>
<!-- include all the common repositories, we just want to define the kernel -->
<include name="common.xml"/>
<project name="seL4.git" path="kernel"/>
</manifest>
```

## マニフェストのレイアウト

このセクションではプロジェクトで使用されているマニフェストの概要を
説明します。マニフェストのレイアウトに関する完全な説明に関しては
[こちら](https://gerrit.googlesource.com/git-repo/+/master/docs/manifest-format.md)を
ご覧ください。

```xml
    <remote name="seL4" fetch="."/>
```

`remote`要素は、gitリポジトリを特定するリモートを指定します。

```xml
    <project name="musllibc.git" remote="seL4" path="projects/musllibc" revision="sel4"/>
```

`project`要素は、リポジトリを宣言します。

- `name`は、`remote`でのリポジトリ名です。
- `path`は、リポジトリをチェックアウトする位置であり、プロジェクトが
  初期化されたディレクトリからの相対で指定します。
- `revision`は、使用するリポジトリのバージョンを指定します。ブランチと
  リビジョンハッシュがサポートされています。タグも使えますが属性値は
  `refs/tags/tagname`のような構造でなければなりません。
- `linkfile`要素は、リポジトリからプロジェクトレイアウト内の他の
  場所へのシンボリックリンクを指定します。
    - `src`は、リポジトリのチェックアウト先からの相対パスです。
    - `dest`は、プロジェクトディレクトリからの相対パスです。

```xml
    <defalut revision="master" remote="seL4"/>
```

`default`要素は、`project`要素で省略された場合のデフォルトの属性を指定します。
属性が省略されている場合、default要素の値が代わりに使用されます。

## ピン留めマニフェスト

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
   Copyright 2021 seL4 Project a Series of LF Projects, LLC
   SPDX-License-Identifier: BSD-2-Clause
-->
<manifest>
  <remote name="nanopb" fetch="https://github.com/nanopb"/>
  <remote name="opensbi" fetch="https://github.com/riscv"/>
  <remote name="seL4" fetch="."/>
  <remote name="sel4proj" fetch="../sel4proj"/>

  <default remote="seL4" revision="master"/>

  <project name="musllibc.git" path="projects/musllibc" revision="aa82031dd861f7ade4c3738b417de34120b01f15" upstream="sel4" dest-branch="sel4"/>
  <project name="nanopb" path="tools/nanopb" remote="nanopb" revision="1466e6f953835b191a7f5acf0c06c941d4cd33d9" upstream="master" dest-branch="refs/tags/0.4.3"/>
  <project name="opensbi" path="tools/opensbi" remote="opensbi" revision="a98258d0b537a295f517bbc8d813007336731fa9" upstream="refs/tags/v0.8" dest-branch="refs/tags/v0.8"/>
  <project name="seL4.git" path="kernel" revision="a6c29549fce33df65b9018f420d330bca9086712" upstream="master" dest-branch="master"/>
  <project name="seL4_libs.git" path="projects/seL4_libs" revision="14d3c94f5164c993033a35a5d9e23ba6dcf12f5f" upstream="master" dest-branch="master"/>
  <project name="seL4_tools.git" path="tools/seL4" revision="553086aab4c9fb9c4869060fe51a3e95c20ce454" upstream="master" dest-branch="master">
    <linkfile src="cmake-tool/init-build.sh" dest="init-build.sh"/>
    <linkfile src="cmake-tool/griddle" dest="griddle"/>
  </project>
  <project name="sel4_projects_libs" path="projects/sel4_projects_libs" revision="a6fbc13c792ef518e5bcf1da69c8d27b6cd19814" upstream="master" dest-branch="master"/>
  <project name="sel4runtime.git" path="projects/sel4runtime" revision="c27050513503f615137950cb3918f6f040f34407" upstream="master" dest-branch="master"/>
  <project name="sel4test.git" path="projects/sel4test" revision="110ad99b32cb94f0604ce9fef796164b595fe6aa" upstream="master" dest-branch="master">
    <linkfile src="easy-settings.cmake" dest="easy-settings.cmake"/>
  </project>
  <project name="util_libs.git" path="projects/util_libs" revision="280f7bb58290ea3d719d86894655a03f36d347e2" upstream="master" dest-branch="master"/>
</manifest>
```

ピン留めマニフェストは、すべてのリポジトリに対してピン留めされたgitリビジョンを
使用します。プロジェクトの作業バージョンを参照するピン留めマニフェストを作成する
ことは良い習慣です。私達が保守するプロジェクトでは少なくとも2 つのマニュフェスト:
default.xmlとmaster.xmlを提供しています（公開するマニフェストの詳細については、[ReleaseProcess](https://docs.sel4.systems/ReleaseProcess)を参照してください）。

## コマンド

### `init`と`sync`

`repo init`と`repo sync`は最もよく使われるコマンドです。それらの目的はマニフェストを
選択し、すべてのリポジトリをダウンロードし、プロジェクトのディレクトリ構造を設定する
ことです。多くの場合、プロジェクトのREADMEには次のような指示があります。

```bash
mkdir source_dir
cd source_dir
repo init -u https://github.com/seL4/sel4test-manifest.git
repo sync
```

これはマニフェストファイルで記述された場所にあるgitリポジトリをチェックアウトして
新しいディレクトリを初期化します。

`init`は使用するマニフェストを選択するためのもので、`sync`はそのマニフェストを
チェックアウトするためのものです。

#### `init`コマンドのパラメタ

- `-u` urlを取得します。注意: GitHubが生成する`ssh` urlは動作しません。
  `git@github.com:seL4/sel4test-manifest.git`ではなく
  `ssh://git@github.com/seL4/sel4test-manifest.git`に変更する必要があります。
- `-m` マニフェスト名です。デフォルトはdefault.xmlです。
- `-b` ブランチまたはリビジョン、または、`refs/tags/tagname`形式のタグを
  指定します。デフォルトはdefault.xmlです。

#### `sync`コマンド

このコマンドはプロジェクトディレクトリをマニフェストに記述されているものと同期
させます。ブランチをコミットした場合、repoがHEADをマニフェストで記載した内容に
切り替えた際にコミットが*失われる*可能性があります。これが起きた場合は、
`git reflog`を使用して追跡されていないコミットを見つけるか、ブランチがローカル
チェックアウトに残るようにブランチを使用してコミットの追跡を行います。

`sync`にはリモートからチェックアウトする方法を指定するフラグがあります。
たとえば、`-j`は並行チェックアウトを指定します。フラグの一覧と説明には
`repo sync --help`を使ってください。

### `diff`と`status`

この2つのコマンドはすべてのgitリポジトリで`git diff`や`git status`を実行する
ことと同じです。

### `difmanifests`

このコマンドは同じプロジェクトの2つのマニフェスト間のコミットの差分を一覧表示
することができます。

```
changed projects :

        kernel changed from master to 757c3ac98246afd0593367f1fa19054316a77495
                [-] 62445b35 x86: Correct labels for port out operations
                [-] d9780ec7 manual: label object groups in parse_doxygen_xml.py
                [-] 63c5ac6d manual: use level 3 for syscalls
                [-] deba85b2 manual: promote sel4_arch API docs level
                [-] b5ee12f0 manual: group generated API methods by object type
                [-] da1e73fe manual: use int level in parse_doxygen_xml.py
                [-] c2212688 x86: IOPort invocation has proper structure
                [-] 7b8f6106 x86: Consistently compare against PPTR_USER_TOP
                [-] 1b0a7181 manual: s/Polling Send/Non-Blocking Send
                [-] 84b065b0 manual: use fontenc package

        projects/tools changed from master to 930b6467eae8404e4a72555b693120ac0d64fc48
                [-] 121782a CMake add error condition
```

## FAQ

### seL4testなどのプロジェクトのリリース版をチェックアウトする方法は？

```bash
repo init -u https://github.com/seL4/sel4test-manifest.git -b refs/tags/
repo sync
```

### チェックアウト済みのプロジェクトのマニフェストを変更する方法は？

```bash
repo init -m master.xml
repo sync
```

これにより現在のマニフェストからマニフェストリポジトリの`master.xml`に変更されます。

### ピン留めマニフェストを作成する方法は？

```bash
repo manifest -r -o pinned.xml
```

### プロジェクトのチェックアウトと同期をより速く行う方法は？

```bash
repo init -u https://github.com/seL4/sel4test-manifest.git --no-clone-bundle --depth=1
repo sync --jobs=8 --fetch-submodules --current-branch --no-clone-bundle
```
