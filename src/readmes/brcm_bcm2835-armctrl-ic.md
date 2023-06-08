# BCM2835の最上位 ("ARMCTRL") の割り込みコントローラ

BCM2835は独自の最上位割り込みコントローラを搭載しており、2レベル
レジスタ方式で72の割り込みソースをサポートします。この割り込み
コントローラまたはそれを含むHWブロックはSoCのドキュメントでは
"armctrl"と呼ばれることがあり、そのためこのバインディングの名前に
なっています。

BCM2836には同じ割り込みを持つ、同じ割り込みコントローラがありますが、
CPUごとの割り込みコントローラがルートであり、そこでの割り込みは
ARMCTRLに処理すべき割り込みがあることを示します。

## 必須の属性

- compatible: "brcm,bcm2835-armctrl-ic" か "brcm,bcm2836-armctrl-ic" で
  ある必要があります。
- reg : レジスタのベース物理アドレスとサイズを指定します。
- interrupt-controller: ノードを割り込みコントローラであると識別します。
- #interrupt-cells: 割り込みソースをエンコードするために必要なセル数を
  指定します。値は2でなければなりません。

  1セル目は割り込みバンクであり、”IRQ基本保留”レジスタの割り込みは0、
  ”IRQ保留1"レジスタの割り込みは1, ”IRQ保留2"レジスタの割り込みは2です。

  2番目のセルはバンク内の割り込み番号です。有効な値はバンク0では0～7、
  バンク1/2では0～31です。

## `brcm,bcm2836-armctrl-ic`の追加必須プロパティ

- interrupts: この割り込みコントローラが処理する親の割り込みを指定します。

## 割り込みソース

### Bank 0

```
0: ARM_TIMER
1: ARM_MAILBOX
2: ARM_DOORBELL_0
3: ARM_DOORBELL_1
4: VPU0_HALTED
5: VPU1_HALTED
6: ILLEGAL_TYPE0
7: ILLEGAL_TYPE1
```

### Bank 1

```
0: TIMER0
1: TIMER1
2: TIMER2
3: TIMER3
4: CODEC0
5: CODEC1
6: CODEC2
7: VC_JPEG
8: ISP
9: VC_USB
10: VC_3D
11: TRANSPOSER
12: MULTICORESYNC0
13: MULTICORESYNC1
14: MULTICORESYNC2
15: MULTICORESYNC3
16: DMA0
17: DMA1
18: VC_DMA2
19: VC_DMA3
20: DMA4
21: DMA5
22: DMA6
23: DMA7
24: DMA8
25: DMA9
26: DMA10
27: DMA11-14 - DMA 11-14 の共有割り込み
28: DMAALL - すべてのDMA割り込み（chanel 15を含む）でトリガーされる
29: AUX
30: ARM
31: VPUDMA
```

### Bank 2

```
0: HOSTPORT
1: VIDEOSCALER
2: CCP2TX
3: SDC
4: DSI0
5: AVE
6: CAM0
7: CAM1
8: HDMI0
9: HDMI1
10: PIXELVALVE1
11: I2CSPISLV
12: DSI1
13: PWA0
14: PWA1
15: CPR
16: SMI
17: GPIO0
18: GPIO1
19: GPIO2
20: GPIO3
21: VC_I2C
22: VC_SPI
23: VC_I2SPCM
24: VC_SDIO
25: VC_UART
26: SLIMBUS
27: VEC
28: CPG
29: RNG
30: VC_ARASANSDIO
31: AVSPMON
```

## 例

```
/* BCM2835, first level */
intc: interrupt-controller {
	compatible = "brcm,bcm2835-armctrl-ic";
	reg = <0x7e00b200 0x200>;
	interrupt-controller;
	#interrupt-cells = <2>;
};

/* BCM2836, second level */
intc: interrupt-controller {
	compatible = "brcm,bcm2836-armctrl-ic";
	reg = <0x7e00b200 0x200>;
	interrupt-controller;
	#interrupt-cells = <2>;

	interrupt-parent = <&local_intc>;
	interrupts = <8>;
};
```
