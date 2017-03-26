---
title: Raspberry Pi 3 を WiFi アクセス・ポイント化する
date: 2017-03-22
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspberry Pi 3 には、有線 LAN と 無線 LAN の 両方が搭載されています. これの両方を活用して 有線 LAN を 既存ネットワークへ接続し、無線 LAN を アクセスポイント化してみたいと思います.
今回は 有線 LAN を 接続する側 の 既存ネットワーク(光ルーターとか)で DHCP などを提供しているものとして、ブリッジ接続の形にしたいと思います.

**作業環境**
- Raspberry Pi 3 Model B
- Raspbian Jessie Lite


## ネットワーク・インターフェース の ブリッジ作成
Raspberry Pi 3 に 搭載されている 無線 LAN と 有線 LAN を つなぐブリッジを作成します. これによって 無線 LAN を アクセス・ポイント化した際に、そのまま 有線 LAN 側のネットワークに接続することができます.

ブリッジを作成するには `bridge-utils` を インストールして設定します. ネットワーク・インターフェースの設定に、ブリッジ・インターフェースを追加して `eth0` と `wlan0` を ブリッジさせる設定をします.
設定は `/etc/network/interfaces` で 下記のように 3行を追加します. 今回の設定は最小限でしました. 設定の詳細は `man bridge-utils-interfaces` で 確認できます.
```shell-session
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get install bridge-utils -y --no-install-recommends

pi@raspberrypi:~ $ sudo nano /etc/network/interfaces
auto br0
iface br0 inet dhcp
    bridge_ports eth0 wlan0
```

設定後にブリッジのインターフェースをアップさせると状態を確認できます. `ifup` したら SSH (というか、ネットワーク) が 切断されましたが、問題なく再接続できました. なんだろ...
```shell-session
pi@raspberrypi:~ $ sudo ifup br0

pi@raspberrypi:~ $ brctl show
bridge name     bridge id               STP enabled     interfaces
br0             8000.13f62a24d4XX       no              eth0
```


## アクセス・ポイント の 作成
いよいよ 無線 LAN に アクセス・ポイントを作成します. これを稼働させることで WiFi 機器 が 接続できるようになり、上記設定のブリッジを通して既存ネット側につながります.

アクセス・ポイントを作成するには [hostapd](https://w1.fi/hostapd/) を インストールして設定します. 設定はサンプルが用意されているのでコピーして変更します. 設定ファイルに説明が書かれているので詳細が確認しながら設定できます. また日本語に訳してくださった方がいらっしゃり、こちら [hostapd.conf 覚書 - Qiita @JhonnyBravo](http://qiita.com/JhonnyBravo/items/5df2d9b2fcb142b6a67c) も 参考にさせて頂きました. 約 1,700行にものぼるファイルを丁寧に翻訳してくださり、ありがとうございます！
```shell-session
pi@raspberrypi:~ $ sudo apt-get install hostapd -y --no-install-recommends
pi@raspberrypi:~ $ sudo bash -c "zcat /usr/share/doc/hostapd/examples/hostapd.conf.gz > /etc/hostapd/hostapd.conf"
pi@raspberrypi:~ $ sudo chmod 600 /etc/hostapd/hostapd.conf

pi@raspberrypi:~ $ sudo nano /etc/hostapd/hostapd.conf
interface=wlan0
bridge=br0
driver=nl80211

ssid=[SSID]
country_code=JP
hw_mode=g
channel=7
auth_algs=1
ieee80211n=1

wpa=2
wpa_passphrase=[PASS]
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
```

設定が完了したら起動して確認します. 無事起動したら接続する事ができるので、スマホなどで接続して確認します.
```shell-session
pi@raspberrypi:~ $ sudo hostapd /etc/hostapd/hostapd.conf
Configuration file: /etc/hostapd/hostapd.conf
Failed to create interface mon.wlan0: -95 (Operation not supported)
wlan0: interface state UNINITIALIZED->COUNTRY_UPDATE
wlan0: Could not connect to kernel driver
Using interface wlan0 with hwaddr f9:15:ac:e4:XX:XX and ssid "[SSID]"
wlan0: interface state COUNTRY_UPDATE->ENABLED
wlan0: AP-ENABLED
```

起動と接続確認ができたら、ラズパイ起動時に自動的にアクセス・ポイントを起動できるようにします.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/default/hostapd
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```


## クリーンアップ と 再起動
```shell-session
pi@raspberrypi:~ $ sudo apt-get autoremove
pi@raspberrypi:~ $ sudo apt-get autoclean
pi@raspberrypi:~ $ sudo apt-get clean
pi@raspberrypi:~ $ sudo rm -rf /var/lib/apt/lists/*
pi@raspberrypi:~ $ sudo rm -rf /var/cache/apt/archives/*

pi@raspberrypi:~ $ sudo reboot
```



- - - -
Raspberry Pi 3 を アクセス・ポイントにすることができました！ 二つのネットワーク・インターフェースを持っているので、こんなことも簡単にできるのが助かります.
