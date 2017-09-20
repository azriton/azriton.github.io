---
title: Raspberry Pi 基盤 の LED を 消灯する
date: 2017-09-20
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Crystal Signal Pi
- Raspbian
---

![](/images/raspi/crystal-signal-pi/crystal-signal-pi.jpg "Crystal Signal Pi")

Raspberry Pi の 基盤には ACT と PWR の LED があります. 普段から Crystal Signal Pi の 四角柱を光らせていますが、壁際においているため PWR の 赤 LED が 反射し色が混ざるのと、夜間消灯中にも壁に反射する赤色が気になるので、基盤 の LED を 消灯したいと思います.

**作業環境**
- Raspberry Pi 3 Model B
- Crystal Signal Pi
- Raspbian Jessie Lite


## 現在の状況
まずは、現在の状況.

日中 の Crystal Signal Pi 点灯中でも、白い壁に反射する 赤 LED が ちょっと気になります...
![](/images/raspi/crystal-signal-pi/led/01.jpg)

夜間消灯中に輝く PWR の 赤 LED. 白い壁に反射して赤に染まっています. これだけの光量だと離れていても、棚の一角が かなり赤く光ります.
![](/images/raspi/crystal-signal-pi/led/02.jpg)


## コマンドラインから 一時的 に 基盤 の LED を 消灯する
Raspberry Pi というか Raspbian Jessie で 基盤 の LED を 操作するには、 `/sys/class/leds/` にある `led0` と `led1` ディレクトリのファイルを操作します. `led0` が ACT で `led1` が PWR です.

操作するファイルは ２つで、 `trigger` と `brightness` です. `trigger` は LED を 光らせるトリガーで、 `brightness` は LED の 明るさです.

まず、現在の設定を確認します.
`led0` ACT  は `mmc0` で `255`. つまり SD カード の 読み書きをトリガーとして最大光量で点灯します.
`led0` PWR  は `input` で `255`. つまり電源の有無をトリガーとして最大光量で点灯します.

```console
pi@raspberrypi:~ $ cat /sys/class/leds/led0/trigger
none kbd-scrolllock kbd-numlock kbd-capslock kbd-kanalock kbd-shiftlock kbd-altgrlock kbd-ctrllock kbd-altlock kbd-shiftllock kbd-shiftrlock kbd-ctrlllock kbd-ctrlrlock timer oneshot heartbeat backlight gpio cpu0 cpu1 cpu2 cpu3 default-on input panic [mmc0] mmc1 rfkill0 rfkill1

pi@raspberrypi:~ $ echo 0 | sudo tee /sys/class/leds/led0/brightness
255


pi@raspberrypi:~ $ cat /sys/class/leds/led1/trigger
none kbd-scrolllock kbd-numlock kbd-capslock kbd-kanalock kbd-shiftlock kbd-altgrlock kbd-ctrllock kbd-altlock kbd-shiftllock kbd-shiftrlock kbd-ctrlllock kbd-ctrlrlock timer oneshot heartbeat backlight gpio cpu0 cpu1 cpu2 cpu3 default-on [input] panic mmc0 mmc1 rfkill0 rfkill1

pi@raspberrypi:~ $ echo 0 | sudo tee /sys/class/leds/led1/brightness
255
```

では、これらに設定をして消灯します.
`trigger` を `none` にすることで、LED を 点灯するアクションを起こさせなくします. 続いて、`brightness` を `0` にして消灯します.
`led0` ACT は 点滅なので `trigger` を 止めれば消えている状態になるはずですが、SD カードへアクセス中の瞬間にあたると点灯したままになるので、その場合は `brightness` を `0` にして消灯します. (イベントからのトリガーが止まるだけで、明るさとは関係ないということですね)

```console
pi@raspberrypi:~ $ echo none | sudo tee /sys/class/leds/led0/trigger
none

pi@raspberrypi:~ $ echo none | sudo tee /sys/class/leds/led1/trigger
none
pi@raspberrypi:~ $ echo 0 | sudo tee /sys/class/leds/led1/brightness
0
```

こちらは現在の状態を変更するものなので、設定したとおりに LED が すぐに消灯します. これで光が混ざらなくなって気にならないし、夜間消灯も完全に消えてくれます.
![](/images/raspi/crystal-signal-pi/led/03.jpg)

なお `brightness` の 数値で明るさの変化は見られませんでした. `0` `1` で ON/OFF ぐらいのもののようです. 初期値 と `max_brightness` の 値が `255` なので変化できそうなのに...


## 恒久対策
上記設定方法は現在の設定変更で使えるものになります. そのためリブートすると元の状態に戻ります.
恒久的に設定するには `/boot/config.txt` に 設定を記述します.
設定の詳細については `/boot/overlays/README` ファイル、もしくは最新になりますが raspberrypi/firmware の [firmware/boot/overlays/README](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README) を ご確認ください.

```txt
pi@raspberrypi:~ $ echo "dtparam=act_led_trigger=none,act_led_activelow=on" | sudo tee -a /boot/config.txt
pi@raspberrypi:~ $ echo "dtparam=pwr_led_trigger=none,pwr_led_activelow=on" | sudo tee -a /boot/config.txt
```


## Raspberry Pi Zero の 場合
Raspberry Pi Zero は PWR LED しかないので、コマンドラインからは `led0` で 設定します.
`/boot/config.txt` は `act_led_~` なので変わらずです.



- - - -
Raspberry Pi の 基盤には PWR の 赤 LED のほかに、ACT の 緑 もありますが、緑は点灯では無く点滅であり、強く光り続けないので今回は PWR の 赤だけを消灯することにして様子見しようと思ったら、やはり気になるので一緒に消しました. 意外と光るものなんですね. (;^_^A
