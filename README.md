## linuxでlongan nanoを利用する方法

前提:VSCodeは既にインストールされているとする

1. PlatformIOをExtensionsから検索して拡張機能としてインストール
2. PlatformIOのBoards/PlatfromsからGD32Vをダウンロード

### longan nanoを知らない人へ
小型液晶付きの手のひらサイズのマイコンボードです

話題のRISC-Vやグラフィックスを使った開発に超オススメ

価格はたったの800円

販売元の一つ \
Ｓｉｐｅｅｄ　Ｌｏｎｇａｎ　Ｎａｎｏ　ＲＩＳＣ－Ｖ　ＧＤ３２ＶＦ１０３ＣＢＴ６開発ボード: マイコン関連 秋月電子通商-電子部品・ネット通販 \
https://akizukidenshi.com/catalog/g/gK-14678/

### dfu-utilの導入
PlatformIO標準搭載の
~/.platformio/packages/tool-dfuutil/bin/dfu-util
で書き込み可能だったので、必ずしも必要ではないです

追記:
うまく書き込めないことがあるので標準搭載のより新しいバージョンを推奨します

#### 以前のやり方

以下の改変版dfu-utilをダウンロード \
https://github.com/riscv-mcu/gd32-dfu-utils

libusb-develをインストールしてコンパイル

以下をダウンロード \
http://dl.sipeed.com/LONGAN/platformio/dl-packages

ちなみにdfu-utilとはusb経由でfirmwareを読み書きするツール

dfu-util - Device Firmware Upgrade Utilities \
http://dfu-util.sourceforge.net/

>Windows版 \
Getting to Blinky · Longan DOC \
http://longan.sipeed.com/en/get_started/blink.html
 \
以下からGD32_MCU_Dfu_Toolをダウンロード \
http://dl.sipeed.com/LONGAN/Nano/Tools
 \
tool-dfuutil not auto-installed · Issue #28 · sipeed/platform-gd32v \
https://github.com/sipeed/platform-gd32v/issues/28
 \
Unable to flash with dfu-util · Issue #5 · riscv-rust/longan-nano \
https://github.com/riscv-rust/longan-nano/issues/5


### dfu書き込みモードへの移行手順

Sipeed Longan Nano でLチカまでにえらい手間取った... - Sabotenboy's *sigh* \
https://blog.goo.ne.jp/sabotenboy_london/e/fd0337523b8e8f73e90360e35786617c
>ボード液晶画面側でUSBポートを下にして右のボタンを押しながらUSB接続、その後左のボタンを1クリック、最後に右ボタンを放す

うまくできない場合は少し長めに左ボタンを押して、左ボタンを離した後0.5秒後に右ボタンを離すようにするとよい

最初は

```
dfu-util: Cannot open DFU device xxxx:xxxx
```

と出てしまったが以下を頼りにrootで使用するとできた

permissionの問題なのでudevで設定するとよい

```
cp 40-logan.rules /etc/udev/rules.d
udevadm control --reload
```

dfu-util / Tickets / #26 Cannot open DFU device 0483:df11 (without using sudo) \
https://sourceforge.net/p/dfu-util/tickets/26/


### 正しく認識された時の表示

```
# ~/.platformio/packages/tool-dfuutil/bin/dfu-util -l
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2020 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Found DFU: [28e9:0189] ver=0100, devnum=59, cfg=1, intf=0, path="1-2", alt=0, name="@Internal Flash  /0x08000000/128*001Kg", serial="3CBJ"
```

### コンパイル
正しくインストールできていればVSCodeのPlatformIOタブのPROJECT TASKSでBuildというのがあるはず
これを押すと自動的にコンパイルが始まる
ウインドウの下部にチェックマークがあるがこれも同じである

### 書き込み方法
書き込むファイルは .pio/build/sipeed-longan-nano/firmware.bin とする

```
$ cd .pio/build/sipeed-longan-nano
$ ~/.platformio/packages/tool-dfuutil/bin/dfu-util -d 28e9:0189 -a 0 --dfuse-address 0x08000000:leave -D firmware.bin
```

### サンプル

Longan nano のLチカを別の色でやってみる - Qiita \
https://qiita.com/myston/items/5a57d44e0a3e7e181e8d

Longan nanoでマンデルブロ集合描画 - Qiita \
https://qiita.com/myston/items/0e32321e6d4c53280081

Longan nanoでBad Apple!!をさくっと再生する - Qiita \
https://qiita.com/myston/items/ae0d93bd177e7ecf2e5a

Longan_GD32VF_examples \
https://github.com/sipeed/Longan_GD32VF_examples