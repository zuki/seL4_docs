# デバイスの割り込み情報を指定する

## 1) 割り込みクライアントノード

割り込みを発生させるデバイスを記述するノードには、"interrupts"プロパティと
"interrupts-extended"プロパティのいずれか、またはその両者が必要です。両者が
存在する場合は後者が優先されます。後者を認識しないソフトウェアとの互換性の
ために前者だけを提供されることがあります。これらのプロパティには出力割込み
ごとに1つずつ指定した割込み指定子のリストが含まれます。割り込み指定子の
フォーマットは割り込みがルーティングされる割り込みコントローラによって決定
されます。詳細は下のセクション2を参照してください。

**例**:
```
	interrupt-parent = <&intc1>;
	interrupts = <5 0>, <6 0>;
```

"interrupt-parent"プロパティは割り込みがルーティングされるコントローラを
指定するために使用され、割り込みコントローラノードを参照するphandleを1つ
含んでいます。このプロパティは継承されるため、割り込みクライアントノードや
そのいずれかの親ノードで指定することができます。"interrupts"プロパティに
リストアップされている割り込みは常にそのノードの割り込み親を参照しています。

"interrupts-extended"プロパティは特殊な形式であり、ノードが複数の割り込み親を
参照する必要がある場合や継承された割り込み親とは異なる割り込み親を参照する
場合に便利です。このプロパティの各エントリには親のphandleと割り込み指定子が
含まれます。

**例**:
```
	interrupts-extended = <&intc1 5 1>, <&intc2 1 0>;
```

## 2) 割り込みコントローラノード

デバイスは"interrupt-controller"プロパティにより割り込みコントローラと
してマークされます。これは空のブール値プロパティです。追加プロパティ
"#interrupt-cells"は1つの割り込みを指定するために必要なセルの数を定義します。

割り込み指定子の長さとフォーマットを定義することは割り込みコントローラの
バインディングの責任です。以下の2つのバリアントが一般的に使用されます。

###  a) 1セル

"#interrupt-cells"プロパティに1を設定し、1つのセルがコントローラ内の
割り込みのインデックスを定義します。

**例**:
```
	vic: intc@10140000 {
		compatible = "arm,versatile-vic";
		interrupt-controller;
		#interrupt-cells = <1>;
		reg = <0x10140000 0x1000>;
	};

	sic: intc@10003000 {
		compatible = "arm,versatile-sic";
		interrupt-controller;
		#interrupt-cells = <1>;
		reg = <0x10003000 0x1000>;
		interrupt-parent = <&vic>;
		interrupts = <31>; /* Cascaded to vic */
	};
```

### b) 2セル

"#interrupt-cells"プロパティに2を設定し、1番目のセルはコントローラ内の
割り込みのインデックスを定義し、2番目のセルで以下のフラグのいずれかを
指定します。

```
- ビット[3:0] トリガタイプとレベルフラグ
	1 = Low-to-High エッジトリガ
	2 = high-to-low エッジトリガ
	4 = active high レベルセンシティブ
	8 = active low レベルセンシティブ
```

**例**:
```
	i2c@7000c000 {
		gpioext: gpio-adnp@41 {
			compatible = "ad,gpio-adnp";
			reg = <0x41>;

			interrupt-parent = <&gpio>;
			interrupts = <160 1>;

			gpio-controller;
			#gpio-cells = <1>;

			interrupt-controller;
			#interrupt-cells = <2>;

			nr-gpios = <64>;
		};

		sx8634@2b {
			compatible = "smtc,sx8634";
			reg = <0x2b>;

			interrupt-parent = <&gpioext>;
			interrupts = <3 0x8>;

			#address-cells = <1>;
			#size-cells = <0>;

			threshold = <0x40>;
			sensitivity = <7>;
		};
	};
```

## 3) 割り込み起床親

SoCの一部の割り込みコントローラには、常に電源が入っており、SoCをサスペンドから
起床させるためにルーティングされる選択された割り込みを持っているものがあります。
これらの割り込みコントローラは親割り込みコントローラのカテゴリーには属さず、"wakeup-parent"プロパティにより指定することができ、起床機能を持つ割り込み
コントローラを参照するphandleを一つ持つことができます。

**例**:
```
	wakeup-parent = <&pdc_intc>;
```
