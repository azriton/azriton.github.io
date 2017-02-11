---
title: Raspbian Jessie Lite の Wi-Fi 設定
date: 2016-11-25
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspbian Jessie Lite に Wi-Fi の 設定を行います. Raspberry Pi へは SSH で 接続しているものとします. 今回は Raspberry Pi Zero ではなく、Wi-Fi が ついている Raspberry Pi 3 で 設定していきます.

**作業環境**
- Windows 7
- Raspbian Jessie Lite
- Raspberry Pi 3 Model B


## Wi-Fi の 国コード を 設定
設定画面の説明に `Set the legal channels used in your country` と あるように、国ごとに利用できるチャンネルが異なるために設定を行います. 各国の利用できるチャンネルは [List of WLAN channels](https://en.wikipedia.org/wiki/List_of_WLAN_channels) に まとまっています.
`sudo raspi-config` コマンドから、以下の順で設定します.
1. `5 Internationalisation Options` を 選択
2. `I4 Change Wi-fi Country` を 選択
3. `JP Japan` を 選択

最後に `Finish` を 選択し、再起動します.


## Wi-Fi へ 接続
まずは現在の設定とアクセスポイントの一覧を見てみます.
設定ファイルは `/etc/wpa_supplicant/wpa_supplicant.conf` で、先に設定した Wi-Fi の 国コード `JP` が 反映されているのが分かります.
また iwlist scan で アクセスポイントの一覧を見ることができます. `iwconfig wlan0` で `IEEE 802.11bgn` と 出ているように `IEEE 802.11n/g/b` で 接続できるアクセスポイントとなります. (ちゃんとリスト確認しないで、`IEEE 802.11n/a` の SSID を 入力して接続できずにはまりました...)
```shell-session
pi@raspberrypi:~ $ sudo cat /etc/wpa_supplicant/wpa_supplicant.conf
country=JP
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

pi@raspberrypi:~ $ iwconfig wlan0
wlan0     IEEE 802.11bgn  ESSID:off/any
          Mode:Managed  Access Point: Not-Associated   Tx-Power=31 dBm
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:on

pi@raspberrypi:~ $ sudo iwlist wlan0 scan | grep ESSID
                    ESSID:"ABC-1234-56789X"
                    ESSID:"DEF-9876-54321X"
                    ESSID:...
```

接続するアクセスポイントが無事にリストに表示されていたら、`wpa_passphrase` コマンドで設定ファイルに接続情報を追記します.
```shell-session
pi@raspberrypi:~ $ sudo sh -c 'wpa_passphrase [SSID] [PASSPHRASE] >> /etc/wpa_supplicant/wpa_supplicant.conf'
```

その後、設定ファイルに書かれている平文のパスワードを消します. テキストエディタで編集します. Raspbian の デフォルト・エディタ `nano` で 開きます.
普段は `vim` なので使い慣れないですが、デフォルトとのことなので使ってみます. `vim` と 異なりファイルを開いた直後から編集ができます. 行削除はなさそうなので、`#psk="xxx"` の 行を地道に削除していきます. (範囲を指定しない切り取り `^K` で 一行切り取りもありでした. 使い慣れてないので手間がかかってしまった.)
保存 は `^O` で、終了 は `^X` です. 画面下に表示されています. Windows では、それぞれ `Ctrl + O` と `Ctrl + X` に なります.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
country=JP
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid="ABC-1234-56789X"
        #psk="----PLAIN-PASSPHRASE----"  <- この行を削除します
        psk=055f68fd257ed0b7d41c6adb757a0a44fe1400737cac7f0fbefd6196ac66bf9a
}
```

ネットワーク・インターフェースを再起動し、アクセスポイントへ接続できているか確認します.
```shell-session
pi@raspberrypi:~ $ sudo ifdown wlan0
pi@raspberrypi:~ $ sudo ifup wlan0
pi@raspberrypi:~ $ iwconfig wlan0
wlan0     IEEE 802.11bgn  ESSID:"ABC-1234-56789X"
          Mode:Managed  Frequency:2.462 GHz  Access Point: 21:5E:4E:97:XX:XX
          Bit Rate=65 Mb/s   Tx-Power=31 dBm
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:on
          Link Quality=69/70  Signal level=-41 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```


## パワーマネジメント の 設定
Wi-Fi の パワーマネージメントによってネットワークアクセスが不安定になったり、遅延が生じるケースがあります. その際にはパワーマネージメントを切ることで改善されることがあります.
パワーマネージメントを切るということは、電力消費が上がったり、Wi-Fi モジュールの熱を逃がす時間が得られないなどのトレードオフが発生しますが、必要に応じて設定をします.

設定は `/etc/network/interfaces` に `wireless-power off` を 追記します.
以下に `wireless-power off` を 記述した部分を抜粋します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/network/interfaces
allow-hotplug wlan0
iface wlan0 inet manual
    wireless-power off
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```

再起動し `iwconfig wlan0` コマンドで `Power Management:off` に なっていることを確認します.
```shell-session
pi@raspberrypi:~ $ sudo reboot
pi@raspberrypi:~ $ iwconfig wlan0
wlan0     IEEE 802.11bgn  ESSID:"ABC-1234-56789X"
          Mode:Managed  Frequency:2.462 GHz  Access Point: 21:5E:4E:97:XX:XX
          Bit Rate=65 Mb/s   Tx-Power=31 dBm
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:off
          Link Quality=69/70  Signal level=-41 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```

設定については、こちらの [Disable power management for Ralink RT5370](https://www.raspberrypi.org/forums/viewtopic.php?t=46569&p=647343) を 参考にさせて頂きました.


## 固定 IP アドレス を 設定する
avahi-daemon が 動作しているため Bonjour の クライアントが入っていれば、LAN 内では `ホスト名.local` で Raspberry Pi へ アクセスすることができます.
そのため 固定 IP を 設定しなくても困らないケースが多いとは思いますが、備忘録として 固定 IP の 設定方法を残しておきます. (ミニマム構成にするために avahi-daemon を 止めるというケースは想定されますね.)

環境
- IP アドレス: 192.168.0.100
- ネットマスク: 255.255.255.0
- デフォルト GW: 192.168.0.1

設定は `/etc/dhcpcd.conf` の 一番最後に以下の内容を記述します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/dhcpcd.conf
interface wlan0
static ip_address=192.168.0.100/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1
```
※ `interface wlan0` は 無線 LAN の ネットワーク・インターフェース、有線 LAN で IP アドレス を 固定する場合は `wlan0` を `eth0` にします.

DHCP の クライアント を 再起動し、設定した IP アドレス で アクセスできるか確認します.
```shell-session
pi@raspberrypi:~ $ sudo service dhcpcd reload
```



- - - -
無事 Wi-Fi へ 接続できるようになりました. 小型なのがラズパイの魅力だから Wi-Fi で ネットに接続できることで更に取り回しが楽になりますね.
