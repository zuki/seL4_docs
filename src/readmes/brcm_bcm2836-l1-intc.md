# BCM2836のCPU単位の割り込みコントローラ

BCM2836は、タイマー、PMUイベント、SMP IPI用にCPUごとに割り込み
コントローラを備えています。複数のCPUの内、1つのCPUがBCM2835
スタイルの割り込みコントローラにチェーンするペリフェラル（GPU）
イベント用の割り込みを受け取ることができます。

## 必須属性

- compatible: "brcm,bcm2836-l1-intc"である必要があります。
- reg: レジスタのベース物理アドレスとサイズを指定します。
- interrupt-controller:	ノードを割り込みコントローラであると識別します。
- #interrupt-cells:	割り込みソースをエンコードするために必要なセル数を
  指定します。値は2でなければなりません。

クライアントデバイスが使用する一般的な割り込みコントローラの
バインディングの詳細についてはこのディレクトリのinterrupts.txtを参照してください。

## 割り込みソース

```
0: CNTPSIRQ
1: CNTPNSIRQ
2: CNTHPIRQ
3: CNTVIRQ
8: GPU_FAST
9: PMU_FAST
```

## 例

```
local_intc: local_intc {
	compatible = "brcm,bcm2836-l1-intc";
	reg = <0x40000000 0x100>;
	interrupt-controller;
	#interrupt-cells = <2>;
	interrupt-parent = <&local_intc>;
};
```
