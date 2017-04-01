---
title: Raspbian Jessie Lite で Docker する - GPIO 編
date: 2017-04-02
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

[Raspberry で Docker が 動作した](/2017/03/30/Raspbian-Jessie-LiteでDockerする-インストール編/) ので、続いては Docker の コンテナ側から GPIO を 使うプログラムを実行したいと思います.

**作業環境**
- Raspberry Pi 3 Model B
- Raspbian Jessie Lite
- Docker
- Python 3.4


## Lチカ on Docker！
GPIO と 言ったら、とりあえず Lチカ(LED チカチカ) ですね.
前回から参考にさせていただいている記事 [Make use of GPIO -- Get Started with Docker 1.12 on Raspberry Pi](http://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/#makeuseofgpio) に 説明があったので、そちらを使わせていただきたいと思います. そちらの配線図をもとに Raspberry Pi 3 で 以下のようなブレッドボード配線図にしました.
![](/images/raspi/fritzing/20170402-01.png)


## Raspbian コンテナ で Lチカ
Raspbian を 使おうと思ったのですが残念ながら、前回の [armfh](https://hub.docker.com/r/armhf) にはありませんでした. [Resin.io](http://resin.io/) で 作られいる Raspbian イメージがあるので、そちらを使うとのことです.

なお Resin.io は IoT デバイス を 管理するプラットフォームを提供しているサービスを提供しているようです. -- [Welcome to Resin.io](https://docs.resin.io/introduction/)

今回はお試しなので `Dockerfile` を 作らず、コンテナ内での作業で直接 Lチカ を 実行したいと思います.
まずは Raspbian Jessie で コンテナを起動します. GPIO に アクセスするためにはメモリの特定領域へ書き込める必要があります. そのため `--device /dev/gpiomem` オプションを付けます. (※ 参考にさせて頂いた記事では `--privileged` を 使っていますが、全デバイスへのアクセス権を与える必要はないので `--device` に しました)
※ 2017年3月現在、最新の `latest`、`jessie`、`jessie-20170301`～`jessie-20170329` は コンテナが立ち上がらず沈黙で終了するため `jessie-20170111` を 使っています. いろいろ調べたけどわからんかった、悔しい orz
```shell-session
pi@raspberrypi:~ $ docker run -it --rm --device /dev/gpiomem resin/rpi-raspbian:jessie-20170111 /bin/bash
root@8e7c54acXXXX:/#
root@8e7c54acXXXX:/# apt-get update
root@8e7c54acXXXX:/# apt-get install -y --no-install-recommends python3-pip python3-dev gcc nano
root@8e7c54acXXXX:/# pip3 install rpi.gpio
...(省略)
Successfully installed rpi.gpio
Cleaning up...
```

LEDを点滅させるプログラムを作成します.
```python(chikachika.py)
root@8e7c54acXXXX:/# nano app.py
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
root@8e7c54acXXXX:/# python3 app.py
```

Docker コンテナ から GPIO へ アクセスできました！
![](/images/raspi/fritzing/20170402-02.png)


## 参考にさせて頂いた情報
- [Get Started with Docker 1.12 on Raspberry Pi](http://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/)
- [Docker on Raspberry Pi in 4 Simple Steps -- resin.io](https://resin.io/blog/docker-on-raspberry-pi-in-4-simple-steps/)



- - - -
つたない知識をもとに Docker コンテナから GPIO へ アクセスを試みましたが、かなりハマった...

GPIO へのアクセスは "メモリへのアクセス" を 単純に考えていたので `docker` コマンドの引数に `--device /dev/mem` で 頑張ろうとしたのが間違えで、`--privileged` なら動くのに、`--device /dev/mem` は 動かない. いや、動くんだけど LED が 点灯しない. そして、ついには `--cap-add SYS_RAWIO` の 話を誤解したりと orz

本ポストにあるように `--device /dev/gpiomem` が 正しかったようです. 素直に `ls -l /dev` して、ながめれば、もっと早く気付いただろうに... 問題解決力をもっと鍛えねば.
