---
title: Raspbian Jessie Lite その他 設定
date: 2017-03-19
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/assets/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspberry Pi の [SD 延命化対策](/2017/03/16/Raspbian-Jessie-LiteのSDカード延命化/)もでき、だいぶ設定ができました. 最後に細かい設定をしておきたいと思います.

**作業環境**
- Raspbian Jessie Lite


## 不要なパッケージの削除
今回は Raspbian Jessie Lite なので X Window System などは入っていないので もともと軽量ですが、使っていないパッケージが入っているとアップデート時に時間がかかったりするので、不要なパッケージは削除しておきます... が、あんまり不要なパッケージがなかったです. 一応メモ.

現在入っているパッケージの一覧を取得するには `dpkg -l` コマンドを使用します.
```shell-session
pi@raspberrypi:~ $ dpkg -l
要望=(U)不明/(I)インストール/(R)削除/(P)完全削除/(H)保持
| 状態=(N)無/(I)インストール済/(C)設定/(U)展開/(F)設定失敗/(H)半インストール/(W)トリガ待ち/(T)トリガ保留
|/ エラー?=(空欄)無/(R)要再インストール (状態,エラーの大文字=異常)
||/ 名前          バージョン    アーキ   説明
+++-=============-=============-========-========================================
ii  acl           2.2.52-2      armhf    Access control list utilities
ii  adduser       3.113+nmu3    all      add and remove users and groups
ii  alsa-utils    1.0.28-1      armhf    Utilities for configuring and using ALSA
...(省略)
```

とりあえず削除できそうなのは `triggerhappy` ぐらいでしょうか. 音声が不要な場合は `alsa-utils`、固定 IP 化 するなどして Bonjour による `hostname.local` で アクセスする費用がなければ `avahi-daemon` も 削除できます. 今回は音声 と Bonjour は 残したいと思います.
`dbus` は 削除してよいという話がありましたが、`avahi-daemon*` `bind9-host*` `bluez*` `bluez-firmware*` `pi-bluetooth*` あたりが巻き込まれて消えるので削除しないほうがよさそうです.

ということで、削除のメモ. (1つだけなら削除しなくても...)
```shell-session
pi@raspberrypi:~ $ sudo apt-get purge -y --auto-remove triggerhappy
```


## 不要なデーモンの停止
使っていないサービスは停止することでメモリを有効活用！と、思い立つのですが、こちらも、あまり無くメモ.
まずは、稼働しているサービスの一覧を取得. 以下は Raspberry Pi 3 で `triggerhappy` 削除前の結果. Raspberry Pi Zero では `bluetooth.service` `hciuart.service` が なく、`serial-getty@ttyAMA0.service` が 増えてました. 環境に合わせて不要なら止めておいてくれるみたいですね.
```shell-session
pi@raspberrypi:~ $ systemctl | grep running
avahi-daemon.service              loaded active running   Avahi mDNS/DNS-SD Stack
bluetooth.service                 loaded active running   Bluetooth service
cron.service                      loaded active running   Regular background program processing daemon
dbus.service                      loaded active running   D-Bus System Message Bus
dhcpcd.service                    loaded active running   dhcpcd on all interfaces
getty@tty1.service                loaded active running   Getty on tty1
hciuart.service                   loaded active running   Configure Bluetooth Modems connected by UART
ntp.service                       loaded active running   LSB: Start NTP daemon
rsyslog.service                   loaded active running   System Logging Service
ssh.service                       loaded active running   OpenBSD Secure Shell server
systemd-journald.service          loaded active running   Journal Service
systemd-logind.service            loaded active running   Login Service
systemd-udevd.service             loaded active running   udev Kernel Device Manager
triggerhappy.service              loaded active running   LSB: triggerhappy hotkey daemon
avahi-daemon.socket               loaded active running   Avahi mDNS/DNS-SD Stack Activation Socket
dbus.socket                       loaded active running   D-Bus System Message Bus Socket
syslog.socket                     loaded active running   Syslog Socket
systemd-journald-dev-log.socket   loaded active running   Journal Socket (/dev/log)
systemd-journald.socket           loaded active running   Journal Socket
systemd-udevd-control.socket      loaded active running   udev Control Socket
systemd-udevd-kernel.socket       loaded active running   udev Kernel Socket
```

不要なデーモンを停止する場合は下記. ここでは `triggerhappy` を 例に.
```shell-session
pi@raspberrypi:~ $ sudo systemctl disable triggerhappy
```


## NTP に 日本のサーバを設定
時刻合わせの NTP サーバ、当然近い場所にあるサーバの時刻に合わせたほうが良いので設定... たぶん、しなくて大丈夫だと思われます. `pool.ntp.org` が 使われており、近い場所の NTP サーバを返してくれるためです. 最近のディストリビューションはいろいろと設定済みで助かりますね.

一応 `ntpq` で 見てみると下記でした. US の NIST に 行っているのもありますが NTT も あるので、まぁよさそうです.
```shell-session
pi@raspberrypi:~ $ ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*timpany.srv.jre 133.243.238.244  2 u  431 1024  377    8.083   -0.138   0.527
+7.83.198.104.bc 169.254.169.254  3 u 1488 1024  372    8.453    0.292   0.833
+153-128-30-125. 133.243.238.244  2 u  357 1024  377   10.422   -0.117   5.420
-y.ns.gin.ntt.ne 249.224.99.213   2 u  470 1024  377   14.798   -4.433   1.423
```

とはいえ、[pool.ntp.org](http://www.pool.ntp.org) で "pool.ntp.orgは世界中全てのタイムサーバを指定しているので、時間の正確さは理想的ではありません。 もしもうちょっと良い時刻精度 大陸毎のゾーン名や国のゾーン名を指定するとより正確な時刻が得られます。 -- [pool.ntp.org: プールを利用するためにNTPを設定する方法は?](http://www.pool.ntp.org/ja/use.html)" と 説明されている通り日本に明示してもよさそうです. 2017年3月現在で 日本 は 66 サーバ なので、使うのはアジアではなく日本でよさそうです.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/ntp.conf
#server 0.debian.pool.ntp.org iburst
#server 1.debian.pool.ntp.org iburst
#server 2.debian.pool.ntp.org iburst
#server 3.debian.pool.ntp.org iburst
server 0.jp.pool.ntp.org iburst
server 1.jp.pool.ntp.org iburst
server 2.jp.pool.ntp.org iburst
server 3.jp.pool.ntp.org iburst

pi@raspberrypi:~ $ sudo systemctl restart ntp
```


## カーネルのログを抑制
コンソールに表示されるカーネルのログを抑制します. コンソール出力の抑制であり、ログ自体は `/var/log/syslog` へ 出力されています. これにより、若干起動速度が上がることが期待できます.
`/boot/cmdline.txt` の 末尾に `quiet` を 追記します.
```shell-session
pi@raspberrypi:~ $ sudo nano /boot/cmdline.txt
dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet
```


## Raspberry Pi 起動時 に HDMI 接続 の テレビを連動させない
Raspberry Pi を HDMI で テレビにつないでいると、Raspberry Pi の 再起動に応じてテレビの電源が入ったり、テレビ番組を見ていたのに HDMI に 切替ったりします. 常時つないでいるわけでなければよいのですが、常時つないでいると不便な面もあったりします.
`/boot/config.txt` に `hdmi_ignore_cec_init=1` を 追記します.
```shell-session
pi@raspberrypi:~ $ sudo nano /boot/config.txt
hdmi_ignore_cec_init=1
```


## CPU の 動作クロック設定
たまたま何かの調べて物をしている際に [俺のラズパイ2がこんなに遅いわけがない - 電子計算記](http://fujish.hateblo.jp/entry/2015/12/15/225807) さん の 記事を発見し、気になったことから設定しました.
ちなみに、今回の環境での初期設定は `ondemand` だったので あえて変更する必要はないかもしれないですが...

`cpufrequtils` を インストールしコマンドラインから設定します.
```shell-session
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get install cpufrequtils -y --no-install-recommends
pi@raspberrypi ~ $ sudo cpufreq-set -g performance
```

続いて再起動しても設定が反映されるよう設定ファイルを作成します. `MAX_SPEED` と `MIN_SPEED` は `cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_frequencies` から出てきた数字を設定します. 下記は Raspberry Pi 3 の ものになります.

```shell-session
pi@raspberrypi:~ $ sudo cp /usr/share/doc/cpufrequtils/examples/cpufrequtils.sample /etc/default/cpufrequtils
pi@raspberrypi:~ $ sudo nano /etc/default/cpufrequtils
ENABLE="true"
GOVERNOR="performance"
MAX_SPEED=1200000
MIN_SPEED=600000
```

ここで指定する Governor については、以下のようです.

| Governor     | 説明                                             |
|:-------------|:-------------------------------------------------|
| ondemand     | cpu の負担が 95% の時に CPU を動的に切り替えます |
| performance  | 最大周波数で cpu を動作させます                  |
| conservative | 負担が 75% の時に CPU を動的に切り替えます       |
| powersave    | 最小周波数で cpu を動作させます                  |
| userspace    | ユーザーが指定した周波数で cpu を動作させます    |
※ 参考 [CPU 周波数スケーリング - ArchWiki](https://wiki.archlinuxjp.org/index.php/CPU_%E5%91%A8%E6%B3%A2%E6%95%B0%E3%82%B9%E3%82%B1%E3%83%BC%E3%83%AA%E3%83%B3%E3%82%B0)

設定に当たり以下のサイトを参考にさせて頂きました. 有益な情報ありがとうございます！
- [俺のラズパイ2がこんなに遅いわけがない - 電子計算記](http://fujish.hateblo.jp/entry/2015/12/15/225807)
- [LinuxでCPUの動的クロック変更を無効にする方法 | Carpe Diem](https://www.sssg.org/blogs/naoya/archives/2416)
- [CPUの動作周波数をコントロールする - Linux Mintのメモ](https://sites.google.com/site/linuxnomemo/mint-setting/cpufrequtils)
- [CPUの動作クロックを設定する [MA-E/SA Developers' Wiki]](http://ma-tech.centurysys.jp/doku.php?id=mae3xx_tips:cpufreq:start)


## .bashrc
最後に `.bashrc`、こちらは、各々 の お好みなので...
今回はラズパイ環境なので、あまり手を入れず最低限の設定をしました.
```shell-session
pi@raspberrypi:~ $ nano ~/.bashrc
alias ls='ls --color=auto'
alias ll='ls --color=auto --format=verbose'
alias la='ls --color=auto --format=verbose --all'
alias cp='cp --interactive'
alias mv='mv --interactive'
alias rm='rm --interactive'
alias free='free -h'
alias grep='grep --color=auto'

pi@raspberrypi:~ $ sudo reboot
```

いろいろな環境ごとに `.bashrc` はじめ、設定ファイルのばらつきがあるので、いずれは本格的に取り組みたいとは思いますが、なかなか手が出せず...

興味津々ではあるが、いつかホンキ出すときに参考にさせて頂きます.
- [最強の dotfiles 駆動開発と GitHub で管理する運用方法](http://qiita.com/b4b4r07/items/b70178e021bef12cd4a2?utm_content=buffera1736&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer)

どうでもいい余談ですが、Windows で ドット・ファイル や ドット・フォルダを作るには、エクスプローラーの新規作成で `.name.` と ドットでファイル名、フォルダ名を挟むと作れます.


## クリーンアップ
```shell-session
pi@raspberrypi:~ $ sudo apt-get autoremove
pi@raspberrypi:~ $ sudo apt-get autoclean
pi@raspberrypi:~ $ sudo apt-get clean
pi@raspberrypi:~ $ sudo rm -rf /var/lib/apt/lists/*
pi@raspberrypi:~ $ sudo rm -rf /var/cache/apt/archives/*
```



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



- - - -
細かい設定を施そうと思っていましたが、思いのほか そのままで良さそうだったのでビックリしました. 世界中の凄い人たちが、より良い設定・環境を作ってくださっているおかげですね. 感謝感謝です！
