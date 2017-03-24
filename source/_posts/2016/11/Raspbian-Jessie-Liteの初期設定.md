---
title: Raspbian Jessie Lite の 初期設定
date: 2016-11-22
updated: 2017-03-22
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspbian Jessie Lite に 最小限の初期設定を行います.

**作業環境**
- Windows 7
- Raspbian Jessie Lite


## Raspberry Pi へ SSH 接続
Raspbian Jessie は avahi-daemon という [Avahi](http://avahi.org) の ソフトウェアが自動的に起動しています.
Avahi は Apple の [Bonjour](https://support.apple.com/ja-jp/bonjour) という ネットワークを自動設定するための仕様を実装したソフトウェアです. これにより PC に Bonjour の クライアントが入っていれば、LAN 内では `ホスト名.local` で Raspberry Pi へ アクセスすることができます.

Windows 7 は Bonjour を 利用することができないため、[iTunes](http://www.apple.com/jp/itunes/download) か [Bonjour Print Services](https://support.apple.com/kb/DL999) を インストールする必要があります.

SSH クライアントは Microsoft が 開発している [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases) を 使いました.

では、SSH で Raspberry Pi へ 接続します.
コマンド プロンプト から `ssh pi@raspberrypi.local` を 実行します.
初めてアクセスするためホスト鍵の確認がありますので、`yes` を 入力します.
最後にパスワードを聞かれるので、初期パスワード の `raspberry` を 入力します.
```
c:\> ssh pi@raspberrypi.local
The authenticity of host 'raspberrypi.local (fd91::3f13:2b1:fa24:XXXX)' can't be established.
ECDSA key fingerprint is SHA256:HDYEUoWS6x4RsD63mQgqbEdFsvnKyPBhT4forJCXXXX.
Are you sure you want to continue connecting (yes/no)? [yes]
Warning: Permanently added 'raspberrypi.local,fd91::3f13:2b1:fa24:XXXX' (ECDSA) to the list of known hosts.
pi@raspberrypi.local's password: [raspberry]
```

無事に接続ができ、Raspberry Pi へ SSH 接続できました. bash が 起動していることが確認できます.
```shell-session
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

pi@raspberrypi:~ $ echo $SHELL
/bin/bash

pi@raspberrypi:~ $
```


## ファイルシステム の 拡張
Raspbian インストール直後は、2GB 程度のディスク容量しかなく `raspi-config` で ファイルシステムを拡張する必要があるらしいのですが、`df` や `fdisk` で 確認したところ、なぜか SD カード の 16GB が ちゃんと認識されているようでした...
```shell-session
pi@raspberrypi:~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        15G  840M   14G   6% /
devtmpfs        459M     0  459M   0% /dev
tmpfs           463M     0  463M   0% /dev/shm
tmpfs           463M   12M  451M   3% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           463M     0  463M   0% /sys/fs/cgroup
/dev/mmcblk0p1   63M   21M   43M  33% /boot
```

**2017年3月22日 追記**
リリース・ノート 2016-05-10 に 下記の記述がありました.
ファイルシステムは自動的に拡張されているので下記手順は不要でした.
"File system automatically expanded on first boot -- [Release notes](http://downloads.raspberrypi.org/raspbian/release_notes.txt)"

~~よくわかりませんが、念のためファイルシステムの拡張を実行しておきます.~~
~~`sudo raspi-config` コマンドから実行します.~~
~~1. `1 Expand Filesystem` を 選択~~
~~2. `OK` する~~
~~3. `Finish` して再起動~~
~~再起動後 の `df -h` に 変化がないので 16GB が ちゃんと使えているようです.~~


## ホスト名 の 設定
デフォルトのホスト名は `raspberrypi` に なっています. 複数 の Raspberry Pi が ある場合に、Bonjour の `ホスト名.local` で アクセスする際には、適切なホスト名を設定しておく必要があります.
`sudo raspi-config` コマンドから設定します.
1. `7 Advanced Options` を 選択
2. `A2 Hostname` を 選択
3. 設定したいホスト名を入力し、`了解` を 選択


## ロケール と タイムゾーン の 設定
`sudo raspi-config` コマンドから設定します.


以下の順でロケールを設定します.
1. `5 Internationalisation Options` を 選択
2. `I1 Change Locale` を 選択
3. `[*] en_GB.UTF-8 UTF-8` で スペースを押して `*` を 外す
4. `[ ] ja_JP.UTF-8 UTF-8` で スペースを押して `*` を 付ける
5. `OK` する
6. `Default locale for the system environment:` と 聞かれるので、`ja_JP.UTF-8` を 選択

最初のメニューに戻るので、以下の順でタイムゾーンを設定します.
1. `5 Internationalisation Options` を 選択
2. `I2 Change Timezone` を 選択
3. `Asia` → `Tokyo` と 選択

最後に `Finish` を 選択し、再起動します.
SSH で 使っている分にはキーボード設定やフォントの追加は不要ですが、直接利用する場合は設定が必要ですが、PC ではないので SSH 接続だけで十分なので、そのままにしました.


## パッケージ の アップデート
`apt-get` を 使って、パッケージをアップデートします.
```shell-session
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get dist-upgrade -y
```

続いて `rpi-update` コマンドで、ファームウェアの更新をします. ファームウェアの更新というと ROM を 更新するようなイメージがありますが、Raspberry Pi の 場合は SD カード にある OS の Kernel を 更新します. SD カード を 初期化すると元に戻ります. ファームウェア更新後は再起動します.
```shell-session
pi@raspberrypi:~ $ uname -a
Linux raspberrypi 4.4.21-v7+ #911 SMP Thu Sep 15 14:22:38 BST 2016 armv7l GNU/Linux

pi@raspberrypi:~ $ apt-cache search rpi-update
rpi-update - Raspberry Pi firmware updating tool

pi@raspberrypi:~ $ sudo apt-get install rpi-update -y --no-install-recommends
pi@raspberrypi:~ $ sudo rpi-update

pi@raspberrypi:~ $ sudo reboot

pi@raspberrypi:~ $ uname -a
Linux raspberrypi 4.4.33-v7+ #927 SMP Sat Nov 19 18:15:38 GMT 2016 armv7l GNU/Linux
```

最後にクリーンアップします.
```shell-session
pi@raspberrypi:~ $ sudo apt-get autoremove
pi@raspberrypi:~ $ sudo apt-get autoclean
pi@raspberrypi:~ $ sudo apt-get clean
pi@raspberrypi:~ $ sudo rm -rf /var/lib/apt/lists/*
pi@raspberrypi:~ $ sudo rm -rf /var/cache/apt/archives/*
```



- - - -
以上で、最小限の初期設定ができました. これから、いろいろと細かい設定をしていきますが、まずはこれで使い始められますね. ラズパイでの開発、楽しみ.
