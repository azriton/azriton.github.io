---
title: C4 Labs で 買った ブレッドボード付きケース で Lチカする
date: 2017-10-22
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/zero-and-w.jpg "Raspberry Pi Zero & Zero W")

[C4 Labs さん で ヒートシンク付きケースを購入した](/2017/09/26/C4-LabsでRaspberry-Pi-Zeroのヒートシンク付きケースを購入/)際に、ブレッドボード付きケースも購入しました. これを組み立て Lチカ(LED チカチカ) したいと思います.
このケースは Raspberry Pi Zero と Zero W の どちらでも付けることができます. 今回はネットワークの取り回しが良くなる Raspberry Pi Zero W で 作ります.

**作業環境**
- Raspberry Pi Zero W
- Raspbian Jessie Lite


## Raspberry Pi Zero に GPIO ピン を つける
さっそくケースの組み立てに入りたいところですが、Raspberry Pi Zero W は GPIO の ピンが立っていないモデルになります. GPIO の ピン が 無ければ、ブレッドボードがあっても使えないので、GPIO の ピンをつけます.

今回は Pimoroni さん の [GPIO Hammer Header (Solderless)](https://shop.pimoroni.com/products/gpio-hammer-header) の Male を 使います.
Installation Jig を 取り付けて、ハンマーで叩くだけという優れもの！
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/01.jpg)

Installation Jig の ピン用の溝がないプレート、溝のあるプレート、Raspberry Pi の 順に重ね、ガイド用のネジを通します.
GPIO Hammer Header の 穴のあるふくらみがある方を Raspberry Pi の GPIO ピン・ホール に 重ね、ハンマーで叩く天板をのせます.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/02.jpg)

そして、GPIO Hammer Header に のせた Installation Jig の 天板を思い切って、思い切ってハンマーでトントンします.
ちょっとドキドキしますが、それなりの力で叩かないと刺さりません.
いきなり強い力で叩くと割れかねませんが、徐々に力を強く加えていくことで、しっかりと刺さります.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/03.jpg)

プラスチックの土台まで、しっかり刺さったら Installation Jig を 外します.
Raspberry Pi Zero W に GPIO ピン が つきました.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/04.jpg)


## ブレッドボード付きのケース を 組み立てる
GPIO ピン が ついたので、ブレッドボード付きのケース [Zebra Zero Plus Breadboard in Wood for Raspberry Pi Zero 1.3 & Zero Wireless](https://c4labs.net/products/zebra-zero-plus-for-raspberry-pi-zero-wood-1) を組み立てます.
組み立て図がついており、各パーツの特徴もしっかりしているので組み立てやすいです.

ポイントがあるとしたら１つ、ブレッドボードの貼り付けはフレームを重ねてからのがよい、ということでしょうか. ブレッドボードに両面テープがついており貼り付けるのですが、フレームの大きさとかなりぴったりだったので、先に貼り付けるとフレームとかぶりかねません.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/05.jpg)

順番に重ねていき、天板をのせてネジ止め、そして完成！！
SD カードが取り外せるようになっているのがいいですね. これで色々なことが試せそうです.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/06.jpg)


## Lチカ！
では定番の Lチカをやってみたいと思います.
Lチカは以前作った、 [Raspbian Jessie Lite で Docker する - GPIO on Alpine Linux 編](/2017/04/05/Raspbian-Jessie-LiteでDockerする-GPIO-on-Alpine-Linux編/) の Docker 版 Lチカ を 使いました.
![](/images/raspi/c4labs/zebra-zero-plus-breadboard/07.jpg)


なお Raspberry Pi 基盤 の PWR LED が ケースのフレームにかぶり見えずらいので、電源を抜く際は注意が必要です.



- - - -
今回、木のモデルを選びました. ピン番号などの文字が少し読みづらかったので [Black Ice Case for Raspberry Pi Zero 1.3 and Zero Wireless](https://c4labs.net/products/copy-of-zebra-zero-raspberry-pi-zero-case-type-2-black-ice-with-heatsink) の方がよかったかなぁと思ったりもしますが、木の雰囲気もオシャレなのでよしです.
それにしても GPIO Hammer Header、便利すぎる！ 便利すぎます！！
