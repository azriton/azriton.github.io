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



- - - -
### Raspberry Pi 3
ラズパイを始めるには 全部入りの Raspberry Pi 3 が 手ごろではないでしょうか. <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB018K9NNJW%2Fref%3Dnosim" target="_blank" >Raspberry Pi Zero - ラズベリー・パイ ゼロ</a> や <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01GFAIKMI%2Fref%3Dnosim" target="_blank" >Raspberry Pi Zero W - ラズベリー・パイ ゼロ W</a> は 国内では入手しずらいため値上がりしてしてますし、GPIO ピン も 自分で付ける必要があったりと色々と手がかかります. その分楽しいというのもありますが.
届くまで時間がかかってもよい場合は こちら [Raspberry Pi Zero の 購入](/2016/11/13/Raspberry-Pi-Zeroの購入/) で 記事にしました [Pimoroni](https://pimoroni.com/) さん から購入する手もあります.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41zcKgUQXtL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" >Raspberry Pi 3 MODEL B 【RS正規流通品】</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Raspberry Pi 2016-01-28    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Raspberry Pi 3 の 電源
Raspberry Pi 3 は 5V/3A の 電源が必要になります. スマホの充電アダプタでは出力が足りない場合もあるので確認が必要です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41p5wekKaIL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" >Raspberry Pi用電源セット(5V 3.0A)－Pi3フル負荷検証済</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> TechShare     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### マイクロ SD カード
ラズパイ の OS や ストレージに必要です. 16GB あれば十分だと思いますが、用途次第なので お好みのサイズで用意します.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51JBMptiJgL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" >【Amazon.co.jp限定】Transcend microSDHCカード 16GB Class10 UHS-I対応 無期限保証 Nintendo Switch / 3DS 動作確認済 TS16GUSDU1PE (FFP)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> トランセンド・ジャパン 2015-10-02    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### USB の WiFi モジュール
内臓 WiFi が 無い Raspberry Pi 2 や Raspberry Pi Zero の 場合は、USB の WiFi モジュール を 使いすることで使えます. いろいろな種類がありますが PLANEX さん のがよさそうです.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB00ESA34GA%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/31IF86QEZIL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB00ESA34GA%2Fref%3Dnosim" target="_blank" >PLANEX 無線LAN子機 (USBアダプター型) 11n/g/b 150Mbps MacOS X10.10対応 GW-USNANO2A (FFP)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> プラネックス 2013-09-06    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DPLANEX%2520GW-USNANO2A%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FPLANEX%2520GW-USNANO2A%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### USB の 変換アダプタ
Raspberry Pi Zero で USB 機器を使う場合に、マイクロ USB の 製品があると便利ですが、上記 WiFi モジュール のように マイクロ USB ではない場合は、この製品が便利です.
なんと USB の コネクタ内に入ってしまうので、通常の変換製品より圧倒的に出っ張りが少ないです！
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01GFOOXO8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41rqVckFbrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01GFOOXO8%2Fref%3Dnosim" target="_blank" >Mumiji クリニーク超ミニDMマイクロUSB5ピンOTGアダプタコネクタ携帯電話タブレット＆USBケーブル・アンド・フラッシュディスク用</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Mumiji     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DOTG%25E3%2582%25A2%25E3%2583%2580%25E3%2583%2597%25E3%2582%25BF%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FOTG%25E3%2582%25A2%25E3%2583%2580%25E3%2583%2597%25E3%2582%25BF%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
Raspberry Pi 3 を アクセス・ポイントにすることができました！ 二つのネットワーク・インターフェースを持っているので、こんなことも簡単にできるのが助かります.
