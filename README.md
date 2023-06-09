# seL4をRaspberry Pi 3B+で動かすための調査と実行メモ

## seL4サイトの翻訳

- [Raspberry Pi 3 Model B, B+](src/sel4/raspi3b.md)
- [Repoチートシート](src/sel4/repo_cheatsheet.md)
- [ARMハードウェア上での起動](src/sel4/loading_onto_arm_hardware.md)
- [システムの構成とビルド](src/sel4/system_config_build.md)
- [seL4プロジェクトの構成とビルド](src/sel4/config_build_sel4_project.md)
- [プロジェクトにビルドシステムを組み込む](src/sel4/incorporating_into_project.md)

## README, コードコメントの翻訳

- [seL4-toos/elfloader-toolのREADME](src/readmes/elfloader-tool_README.md)
- [linux/Documentation/devicetree/bindings/interrupt-controller/brcm,bcm2835-armctrl-ic.txt](src/readmes/brcm_bcm2835-armctrl-ic.md)
- [linux/Documentation/devicetree/bindings/interrupt-controller/brcm,bcm2836-l1-intc.txt](src/readmes/brcm_bcm2836-l1-intc.md)
- [linux/Documentation/devicetree/bindings/interrupt-controller/interrupts.txt](src/readmes/interrupts.md)

## 実行メモ

- [QEMU上のseL4の実行メモ](src/memos/sel4-test_memo.md)
- [rpi3 32版の実行メモ](src/memos/sel4_rpi3.md)
- [rpi3 64版の実行メモ](src/memos/sel4_rpi3_64.md)
- [rpi3 64版masterの調査メモ](src/memos/sel4_master_rpi3.md)
- [rapi3b+のデバイスツリーの割り込み関連の調査](src/memos/interrupt_controller_rpi3.md)
- [masterとv12.1.0の違い](src/memos/diff_master_v1201.md)
- [tftpからのロード](src/memos/tftp.md)
