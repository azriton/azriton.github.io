---
title: Crystal Signal Pi on Docker
date: 2017-04-14
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Crystal Signal Pi
---

![](/images/raspi/crystal-signal-pi/crystal-signal-pi.png "Crystal Signal Pi")

Raspberry Pi に 光り輝く四角柱 を 立てた Crystal Signal Pi、ようやくミドルウェアを導入しました. ミドルウェア を Docker の コンテナとして導入しましたので、φ(..)メモメモ.


**作業環境**
- Raspberry Pi 3 Model B
- Crystal Signal Pi
- Docker


## なぜ Docker ？
Raspbian Jessie Lite で Docker する - インストール編 の [なぜ Docker ？](/2017/03/30/Raspbian-Jessie-Lite%E3%81%A7Docker%E3%81%99%E3%82%8B-%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E7%B7%A8/#なぜ-Docker-？) で 書きました通り、ラズパイの環境に様々な SDK や ライブラリなどを混在させて競合が発生しないように、各環境を分離するためになります.

Crystal Signal Pi ミドルウェア は、Apache2 と Python2、そして pigpio に 依存しています. 依存関係が少ないので整理しやすいですが、他でまた Apache が 必要となったり、 Python3 を を 使い始めたら... と、思うとコンテナに分離しておけば環境もすっきりしますし、今後 Crystal Signal Pi を カスタマイズしようと思った際に、コンテナだったら Try & Error で 簡単に作り直しもできるのもよいです.

また、ちょっと他でラズパイが要りようになった際にも、コンテナを止めるだけで容易に転用できます. (もちろん、Crystal Signal Pi に 戻すのも簡単)

こんな考えから、Crystal Signal Pi on Docker で 作ることにしました.


## ホスト側 OS に pigpio の 導入
Crystal Signal Pi は pigpio の サービス を 介して GPIO を 操作しています. そのため、pigpio を 導入する必要があります.
今回は Docker コンテナ として Crystal Signal Pi ミドルウェア を 導入しますが、pigpio は ラズパイ の GPIO 操作で共通的に使うものなので、コンテナではなく ホスト OS に 導入しました.
```shell-session
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get install -y --no-install-recommends pigpio
pi@raspberrypi:~ $ sudo systemctl enable pigpiod.service
pi@raspberrypi:~ $ sudo systemctl restart pigpiod.service
```


## Dockerfile の 作成
Crystal Signal Pi 用に、以下のような `Dockerfile` を 用意します.
ベースは軽量の `armhf/alpine` で、それに合わせてパッケージ管理を `apk` で 行います. `apk` を 2回に分けているのは、稼働時に必要となるパッケージ(`curl`、`apache2`、`python`) と ビルド時だけ必要なものを分離し、ビルド時だけに必要なものはコンテナ・イメージを小さくするため最後に `apk del` するためです. `apk --virtual` の オプションで追加(インストール) したものを疑似的にまとめることができ、削除する際に同じキーワードで削除できるのがいいですね.
```shell-session
pi@raspberrypi:~ $ nano Dockerfile
FROM armhf/alpine

RUN apk --no-cache upgrade \
 && apk add --no-cache curl apache2 python \
 && apk add --no-cache --virtual build-dependencies openssl rsync py2-pip gcc make python-dev musl-dev \
 && pip install --upgrade pip \
 && pip install pigpio \
 \
 && curl -sSL https://raw.githubusercontent.com/azriton/crystal-signal-armhf-alpine/master/install.sh | sh \
 \
 && apk del --purge build-dependencies \
 && rm -fr /tmp/* \
 && rm -fr /root/.cache \
;


EXPOSE 80
CMD /usr/sbin/httpd -f /etc/apache2/httpd.conf && /usr/local/bin/LEDController.py
```

上記 `Dockerfile` では、Crystal Signal Pi の ミドルウェアをインストールするスクリプト `install.sh` は、私の GitHub リポジトリから `curl` して実行しています. これは Docker コンテナで動かすためにスクリプトを一部修正してるものになります.
後々のアップデートに追随できなくなると困るので、完全な作り変えはせず、以下の方針で変更しある程度 diff が とれるようにしました. (どのくらい変えるかのバランスで悩んだ. こんな時はどうするのか、よい方法を知りたい)
- OS と Python の パッケージ管理は `Dockerfile` へ 外部化する
- 処理を実行させないものは削除する (コメントアウトにしない)

※ この `Dockerfile` も GitHub の リポジトリにアップしました.
[https://github.com/azriton/crystal-signal-docker-armhf-alpine](https://github.com/azriton/crystal-signal-docker-armhf-alpine)


## ビルドする
`Dockerfile` が 用意できたら、`docker build` します.
※ `--tag=repository/tag` は 自身のリポジトリとタグを設定します.
```shell-session
pi@raspberrypi:~ $ docker build --force-rm --no-cache --tag=repository/tag .
```


## 実行！
Docker コンテナ の 実行は以下になります. `--tag=repository/tag` は ビルド時に指定したものと同じにします.
```shell-session
pi@raspberrypi:~ $ docker run -d -p 80:80 --net host --name crystal-signal-pi repository/tag
```

Crystal Signal Pi の 管理コンソールへブラウザでアクセスできるように `-p 80:80` で 80番ポートを公開します.
`--net host` で コンテナからホスト側のネットワークスタックへアクセスできるようにします. これはホスト側に導入した `pigpiod.service` へ アクセスするためです.

![](/images/raspi/crystal-signal-pi/run/01.png)
![](/images/raspi/crystal-signal-pi/run/02.jpg)



- - - -
今回 armhf/alpine の コンテナを使ったことから、パッケージ管理のコマンドが異なったり、コンテナ内では `systemd` が 使えなかったりと、多少勝手が異なりましたが、何とか動かすことができました.
Docker 化 できたので、これで何も恐れることなく Try & Error できます. いざ、いろいろなシーンに合わせて光を変える挑戦へ！
