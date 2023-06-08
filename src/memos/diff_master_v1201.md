# `util_libs/libplatsupport`におけるmasterとv12.1.0の違い

## `src/arch/arm/`

- 違いはない
- `generic_ltimer.c`, `generic_timer.c`はgic用でありrpi3では使えない。

## `src/plat/bcm2xxx/`

```bash
# v1210
$ ls plat/bcm2837/
chardev.c  gpio.c    mailbox.c       serial.c  spt.c
clock.c    ltimer.c  mailbox_util.c  serial.h  system_timer.c
$ ls plat/bcm2711/
chardev.c  clock.c  ltimer.c  serial.c  serial.h  spt.c  system_timer.c

# master
$ ls plat/bcm2837/
clock.c  gpio.c
$ ls plat/bcm2711/
clock.c  gpio.c
```

- v1210とmasterの`clock.c`, `gpio.c`の内容は同じ
- v1210のbcm2711とbcm2837
  - `ltimer.c`は同じ
  - `serial.c`, `serial.h`, `spt.c`, `system_timer.c`は同じ
  - `chardev.c`はdevidが違うだけ

## `src/mach/bcm/`

```bash
# v1210
$ ls mach/bcm
ls: cannot access 'mach/bcm': No such file or directory

# master
$ ls mach/bcm
bcm_uart.c  ltimer.c   mailbox_util.c  serial.c  system_timer.c
chardev.c   mailbox.c  pl011_uart.c    serial.h
```

- v1210の`plat/bcm2xxx/`とmasterの`mach/bcm`
  - `ltierm.c`, `system_timer.c`はかなり内容が異なる
  - `serial.c`では設定変数で処理を振り分けている
- [コミット: libplatsupport,rpi3/4: use only system time](https://github.com/seL4/util_libs/commit/7733230693740ca2a7d7d770718b95a2e6263375)
  > Since RPi4 timer driver was a copy of RPi3, move the timer drivers under 'mach/bcm'.

## その他

- `src/ltimer.h`は同じ
- `include/platfomrsupport/ltimer.h`は同じ

## 考察

- bcmにまとめる前はrpi3のファイルをrpi4用にコピーしていただけ? (rpi4はv12.1.0では正式サポートされていない)
- bcmにまとめるに当たってrpi4に合わせて修正した?
- まとめる前は同じだったので修正コードがrpi3でも動くと判断して、rpi3実機での確認をしていない?

## 修正方針

- `ltimer.c`を`mach/bcm`から`plat/bcm2711`に移動する
- `plat/bcm2837/ltimer`を復活させる
- sptの削除や`mach/bcm/ltimer.c`でのbcm2837とbcm2711の処理切り分けは深追いしない

# masterを修正して実行

- `ltimer.c`だけでなく`spt.c`, `system_timer.c`, `spt.h`のコピーも必要だった。
- 実機で実行して成功 [ログファイル](sel4_rpi3_64_master.log)
- ファイルのコピー、移動だけでコードの修正は一切不要だった。

```bash
$ git status
Not currently on any branch.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   libplatsupport/plat_include/bcm2837/platsupport/plat/spt.h              # v1210のファイルをコピー
	modified:   libplatsupport/plat_include/bcm2837/platsupport/plat/system_timer.h     # v1210のファイルで上書き
	renamed:    libplatsupport/src/mach/bcm/ltimer.c -> libplatsupport/src/plat/bcm2711/ltimer.c
	renamed:    libplatsupport/src/mach/bcm/system_timer.c -> libplatsupport/src/plat/bcm2711/system_timer.c
	new file:   libplatsupport/src/plat/bcm2837/ltimer.c                                # v1210のファイルをコピー
	new file:   libplatsupport/src/plat/bcm2837/spt.c                                   # v1210のファイルをコピー
	new file:   libplatsupport/src/plat/bcm2837/system_timer.c                          # v1210のファイルをコピー
```
