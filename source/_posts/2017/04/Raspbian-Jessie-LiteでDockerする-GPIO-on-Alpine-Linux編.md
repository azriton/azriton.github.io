---
title: Raspbian Jessie Lite で Docker する - GPIO on Alpine Linux 編
date: 2017-04-05
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

[ラズパイ で Lチカ on Docker が できました](/2017/04/02/Raspbian-Jessie-LiteでDockerする-GPIO編/) が、よく考えたら コンテナ の OS は Raspbian でなくてもよかったのでは？ ということで、Alpine Linux の コンテナから Lチカ してみたいと思います.

**作業環境**
- Raspberry Pi 3 Model B
- Raspbian Jessie Lite
- Docker
- Python 3.5


## Alpine Linux とは？
[Apine Linux](https://alpinelinux.org/) は 軽量 の Linux ディストリビューションで、[musl libc](http://www.musl-libc.org/) と [BusyBox](https://busybox.net) で 作られた 軽量 の 組み込み向け Linux ディストリビューションとのことです.
Docker 界隈では注目を集めているようで、`apk` というパッケージ管理を持っていることから、BusyBox よりも扱いやすいとのことです.

どのくらい軽量化というと、前回利用させていただいた `resin/rpi-raspbian` と 比べると以下のように圧倒的に小さいです.
```shell-session
pi@raspberrypi:~ $ docker images
REPOSITORY          TAG              IMAGE ID      CREATED       SIZE
armhf/alpine        latest           3ddfeafc01f0  2 weeks ago     3.6 MB
resin/rpi-raspbian  jessie-20170111  e49ffb0714c7  2 months ago  118   MB
```


## Alpine コンテナ で Lチカ
さっそく `armhf/alpine` で コンテナを起動します. コマンドが若干違いますが、やりたいことに変わりは無いので難しくは無いですね.
```shell-session
pi@raspberrypi:~ $ docker run -it --rm --device /dev/gpiomem armhf/alpine /bin/sh
/ #
/ # apk update
/ # apk upgrade
/ # apk add python3 python3-dev musl-dev gcc
/ # pip3 install --upgrade pip
/ # pip3 install rpi.gpio
...(省略)
Successfully installed rpi.gpio-0.6.3
```

LEDを点滅させるプログラムを作成します. 前回と同じものになります.
```python(chikachika.py)
/ # vi app.py
import RPi.GPIO as GPIO
import time

pin = 17

GPIO.setmode(GPIO.BCM)
GPIO.setup(pin, GPIO.OUT)

for _ in range(3):
    GPIO.output(pin, GPIO.HIGH)
    time.sleep(1.0)
    GPIO.output(pin, GPIO.LOW)
    time.sleep(1.0)

GPIO.cleanup()
```

プログラムを実行します.
```shell-session
/ # python3 app.py
```

Docker コンテナ から GPIO へ アクセスできました！(画像は前回のを拝借ですが、ちゃんと動きました.)
![](/images/raspi/fritzing/20170402-02.png)



- - - -
ラズパイ の GPIO へ アクセスするとはいえ、よく考えたら Python から `/dev/gpiomem` へ アクセスしているだけで Raspbian 固有 の コマンドなりを使っているわけではなかったので、軽量のコンテナを使うべきでした.
今回、無事 Alpine Linux で Lチカ できたので、今後は Alpine Linux を 使っていこうと思います. あと、他のサーバサイド用途で
使っているコンテナも見直しだ...
