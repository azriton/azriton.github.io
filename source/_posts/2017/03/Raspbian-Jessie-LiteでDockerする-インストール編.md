---
title: Raspbian Jessie Lite で Docker する - インストール編
date: 2017-03-30
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspberry の ベース環境が構築でき、いよいよ自作のアプリや検証などを始めたいところですが、アプリ等の動作環境に当たっては [Docker](https://www.docker.com/) を 使いたいと思います. まずは、その Docker が 動作する環境の構築と動作確認をします.

**作業環境**
- Raspbian Jessie Lite
- Docker


## なぜ Docker ？
Docker は ホスト の Linux系 OS が 動作している環境に、コンテナと呼ばれる仮想環境でソフトウェアを動作できるようにする技術です. コンテナ型の仮想化 は、ざっくり言うと ホスト OS 上で隔離された環境(コンテナ)を作り出し、その隔離された環境で動かすソフトウェアなどは ホスト OS の プロセスとして実行される形態になります. そのため 完全な 別 OS の 実行環境を作るハイパーバイザー型の仮想化に対して軽量で動作させることができます.

では、なぜ ラズパイ で Docker を 使おうとしているのか. まずラズパイで GPIO の 実験をしたりだとか、クラウドと連携させて IoT デバイスのような実験をしたりといったことをしたいと考えています. その際に Python や Node.js など使ったり、そのライブラリ、クラウド の SDK に、Web サーバー など の ミドルウェア と 様々なソフトウェアを入れるということが考えられます.
また、その中でうまくいったものは稼働できるよに整理して残しつつ、また他の事をするといったことも考えられるでしょう.

もし、ここまで整理してきた Raspbian の 環境だけで行うとすると、環境がごちゃごちゃになってしまうでしょうし、時には 各種 SDK や ライブラリ の バージョンが競合して問題が発生するといったことも出てくるでしょう. そのために環境を切り替えられるようなライブラリを入れたり、ディレクトリ分けしたりといったこともできるかと思いますが、Docker のような コンテナに閉じ込めてしまえば、そのような問題の多くは解決できます.
また、うまくいって残しておきたいものは環境構築のためのファイル `Dockerfile` として残しておくこともできるので、外部公開や異なる環境 (ラズパイ上には限るでしょうが) に 移すことも容易になります.

今回は、このような考えから ラズパイ に Docker で アプリ や 検証環境を載せるようにしたいと考えました.


## Docker の インストール と 起動実験
[Docker comes to Raspberry Pi - Raspberry Pi](https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/) に従って、Docker の サイトから Shell Script を 落として実行します. `curl -sSL https://get.docker.com/ | sh` だけで簡単にインストールできます.
```shell-session
pi@raspberrypi:~ $ curl -sSL https://get.docker.com/ | sh
...(省略)
+ sudo -E sh -c docker version
Client:
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Fri Mar 24 00:19:15 2017
 OS/Arch:      linux/arm

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Fri Mar 24 00:19:15 2017
 OS/Arch:      linux/arm
 Experimental: false

If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker pi

Remember that you will have to log out and back in for this to take effect!
```

続いて、インストーラーのスクリプトの出力にあったように、ルートでない pi ユーザで Docker を 実行できるように pi ユーザ を docker グループに追加します. 出力にあったコマンドを、そのまま実行します. その後、デーモンの再起動では聞かないようなので `sudo reboot` します.
```shell-session
pi@raspberrypi:~ $ sudo usermod -aG docker pi
pi@raspberrypi:~ $ sudo reboot
```


## コンテナ起動！
Docker は CPU の アーキテクチャ が ホスト と コンテナのイメージ で 同一である必要があります. そのため 2017年3月現在では Docker Hub に ある、ほとんどのイメージが x86_64 のため利用できません.

それでは困るのですが、[Get Started with Docker 1.12 on Raspberry Pi](http://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/) の 記事によると、Docker チーム が [armfh](https://hub.docker.com/r/armhf) という prefix で 実験的なイメージを用意しているとのことです.
その記事に則り、Alpine Linux の イメージを使ってコンテナを起動してみたいと思います. オプションは `-it` で `-i` の インタラクティブモード と `-t` の tty 接続になり コンテナ内に入って作業ができる状態になります. `--rm` で 終了時にコンテナを削除します. 後からコンテナにアクセスしたい場合は `--rm` は 外す必要があります.
```shell-session
pi@raspberrypi:~ $  docker run -it --rm armhf/alpine /bin/sh
Unable to find image 'armhf/alpine:latest' locally
latest: Pulling from armhf/alpine
7c863ad23cb7: Pull complete
Digest: sha256:d90337a24ddb9e72c44b84006cacd57205986a26a3384d6c68653adf2a0d62ba
Status: Downloaded newer image for armhf/alpine:latest

/ # cat /etc/os-release
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.5.2
PRETTY_NAME="Alpine Linux v3.5"
HOME_URL="http://alpinelinux.org"
BUG_REPORT_URL="http://bugs.alpinelinux.org"

/ # exit
```


## 参考にさせて頂いた情報
- [Get Started with Docker 1.12 on Raspberry Pi](http://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/)
- [Docker on Raspberry Pi in 4 Simple Steps -- resin.io](https://resin.io/blog/docker-on-raspberry-pi-in-4-simple-steps/)



- - - -
Docker が Raspbian に 対応してくれていたおかげで簡単に動作させることができました. これで様々な環境を作っては壊すということができ、いろいろと試せそうです. 次はラズパイの魅力である GPIO を Docker の コンテナ側からちゃんと使えるかの確認です.
