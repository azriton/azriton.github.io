---
title: ラズパイ の 簡易スマート・スピーカー Google AIY の 初期セットアップ
date: 2017-11-15
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Google AIY
---

![](/images/raspi/google-aiy/google-aiy.jpg "Google AIY Voice Kit")

[Google AIY Voice Kit を 組み立てた](/2017/11/09/ラズパイの簡易スマート・スピーカーGoogle-AIYを組み立てる/) ので、さっそく OS の イメージを焼き、初期設定をしたいと思います.

なお 前半 の "OS イメージ の 用意" と "SSH の 有効化 と Wi-Fi の 接続設定"、"スピーカー と マイク の 確認" 以外は、Google AIY Voice Kit とは、直接の関係はありません. ラズパイの基本的な設定をまとめたものになります.

今回は ディスプレイ や キーボード、マウス などが無い環境なので、SSH で セットアップしていきます.
[ドキュメントによると](https://aiyprojects.withgoogle.com/voice/#users-guide) カッコいいデスクトップが立ち上がるようですが見れないです. 残念.
![](https://aiyprojects.withgoogle.com/static/images/aiy-projects-voice/users/log-1.jpg)

ディスプレイなしなので CUI モードにすれば ラズパイ Zero W で も いけるかと思って１台組んでみたのですが、箱の切り欠きと配置が合わないのでケーブル類をちゃんと選ばないと収まらないのと、SD カード は 箱を開けないと抜き差しできなそうで、結局断念してラズパイ３を持ってかれました...

**作業環境**
- Raspberry Pi 3 Model B
- Google AIY Voice Kit


## 主な作業内容
これまで書いてきた記事から必要なものを集めて設定します. 関連する記事は下記になります.
- [ラズパイ の OS イメージを焼くときは Etcher が 便利 ＆ UI カッコいい](/2017/11/12/ラズパイのOSイメージを焼くときはEtcherが便利＆UIカッコいい/)
- [Raspbian Jessie Lite の 初期設定](/2016/11/22/Raspbian-Jessie-Liteの初期設定/)
- [Raspbian Jessie Lite の Wi-Fi 設定](/2016/11/25/Raspbian-Jessie-LiteのWi-Fi設定/)
- [Raspbian Jessie Lite の SSH/公開鍵認証 設定](/2016/11/29/Raspbian-Jessie-LiteのSSH公開鍵認証設定/)
- [Raspbian Jessie Lite の SDカード 延命化](/2017/03/16/Raspbian-Jessie-LiteのSDカード延命化/)
- [Raspbian Jessie Lite その他 設定](/2017/03/19/Raspbian-Jessie-Liteその他設定/)
- [Raspberry Pi 基盤 の LED を 消灯する](/2017/09/20/Raspberry-Pi基盤のLEDを消灯する/)


## OS イメージ の 用意
Google AIY プロジェクト の [Voice Kit の ウェブサイト](https://aiyprojects.withgoogle.com/voice/) の [Get the Voice Kit SD Image](https://aiyprojects.withgoogle.com/voice/#assembly-guide-1-get-the-voice-kit-sd-image) から OS イメージをダウンロードします. ダウンロードしたファイルは `aiyprojects-2017-09-11.img.xz` でした.
※ 同梱の説明書には Get the Voice Kit SD Image への 短縮 URL [https://magpi.cc/2x7JQfS](https://magpi.cc/2x7JQfS) が 記載されてました

`.xz` 形式は Windows の 標準装備では解凍できませんが、こちら [ラズパイ の OS イメージを焼くときは Etcher が 便利 ＆ UI カッコいい](/2017/11/12/ラズパイのOSイメージを焼くときはEtcherが便利＆UIカッコいい/) で 書きました [Etcher](https://etcher.io/) は 解凍する必要なく直接 SD カード へ 書き込んでくれます. 便利だ！

ということで、Etcher を 起動し、上記ファイルを指定して、Flash します.
![](/images/raspi/etcher/06.png)


## SSH の 有効化 と Wi-Fi の 接続設定
SD カード を 取り出す前に、SSH と Wi-Fi の 設定をしておきます.

エクスプローラー で SD カード の ドライブを表示し `ssh` ファイルを作成します. 空のファイルで大丈夫です.
![](/images/raspi/etcher/09.png)

同じく SD カードのドライブに `wpa_supplicant.conf` ファイルを作成します.
![](/images/raspi/etcher/10.png)

`wpa_supplicant.conf` ファイルの内容は以下で、 ssid と psk に 接続する Wi-Fi の 設定を記述します.
ファイルの改行コードは Windows の CRLF ではなく LF のため、メモ帳ではなく改行コードが設定できるエディタを使います.
```
country=JP
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid="----Your-WiFi-SSID----"
        psk="----PLAIN-PASSPHRASE----"
}
```

※ ここでは `psk` が 平文となっていますが、起動後に接続して `wpa_passphrase [SSID] [PASSPHRASE]` コマンドを実行した結果の暗号化された psk に 置き換えておくとよいです.
またラズパイを よくセットアップする場合は 暗号化された psk で 作ったファイルをコピーしておいて、OS イメージを作った際にコピーできるようにしておくと便利です.


## 起動！
Google AIY Voice Kit の Raspberry Pi 3 に SD カードを差し、電源を投入します.


## SSH で 接続
上記の Wi-Fi 設定ができていたら Wi-Fi 経由で SSH が つながるはずです. つながらなかったら 有線 LAN を つなぐか、ディスプレイ＆キーボード...

Windows 10 は mDNS が 入っていて ホスト名.local で 接続を受け付けることはできるが、接続にはいけないらしい. なんでやねん！ってところですが、仕方がないので [iTunes](http://www.apple.com/jp/itunes/download) か [Bonjour Print Services](https://support.apple.com/kb/DL999) を インストールするか、頑張って IP アドレスから探しましょう.

SSH クライアント は Microsoft さん が 開発している [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases) を 使いました.

接続すると、ホスト鍵の確認があるので `yes` を 入力します.
続いてパスワードを聞かれるので、初期パスワード の `raspberry` を 入力します.
```console
c:\> ssh pi@raspberrypi.local
The authenticity of host 'raspberrypi.local (fd91::3f13:2b1:fa24:XXXX)' can't be established.
ECDSA key fingerprint is SHA256:HDYEUoWS6x4RsD63mQgqbEdFsvnKyPBhT4forJCXXXX.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'raspberrypi.local,fd91::3f13:2b1:fa24:XXXX' (ECDSA) to the list of known hosts.
pi@raspberrypi.local's password: [raspberry]
```

ログインできるとデフォルト・パスワードを変更するように警告が表示されます.
こちらは後に [公開鍵認証に変えておく](/2017/11/15/%E3%83%A9%E3%82%BA%E3%83%91%E3%82%A4%E3%81%AE%E7%B0%A1%E6%98%93%E3%82%B9%E3%83%9E%E3%83%BC%E3%83%88%E3%83%BB%E3%82%B9%E3%83%94%E3%83%BC%E3%82%AB%E3%83%BCGoogle-AIY%E3%81%AE%E5%88%9D%E6%9C%9F%E3%82%BB%E3%83%83%E3%83%88%E3%82%A2%E3%83%83%E3%83%97/#SSH-と-公開鍵認証-の-設定) と よいでしょう.
```console
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Build info: Tue Sep 12 00:36:20 UTC 2017 @ 3955cac
Last login: Mon Mar 19 14:40:39 2018 from 10.1.0.105

SSH is enabled and the default password for the 'pi' user has not been changed.
This is a security risk - please login as the 'pi' user and type 'passwd' to set a new password.
```

OS の バージョンなどを確認すると、Raspbian 8、Debian Jessie ベース であることが分かります.
```console
pi@raspberrypi:~ $ cat /etc/issue
Raspbian GNU/Linux 8 \n \l

pi@raspberrypi:~ $ uname -a
Linux raspberrypi 4.9.35-v7+ #1014 SMP Fri Jun 30 14:47:43 BST 2017 armv7l GNU/Linux
```


## スピーカー と マイク の 確認
説明書 P.38, 39 の STEP 09 Check speaker と STEP 10  check microphone では、デスクトップに配置されたアプリケーションを使って確認をしています.
※ オンライン・ドキュメントでは [ Verify it works](https://aiyprojects.withgoogle.com/voice#assembly-guide-6-verify-it-works)

今回は SSH で 接続しているのでダブルクリックで実行できませんが、ホームディレクトリ の `Desktop` ディレクトリにあるアプリのファイルを確認すると Python スクリプトを実行していることがわかります. SSH から Python スクリプトを実行することで、同じことが確認できます.
```console
pi@raspberrypi:~ $ ls -l /home/pi/Desktop/
合計 16
-rw-r--r-- 1 pi pi 214  9月 12 09:36 check_audio.desktop
-rw-r--r-- 1 pi pi 194  9月 12 09:36 check_cloud.desktop
-rw-r--r-- 1 pi pi 179  9月 12 09:36 check_wifi.desktop
-rw-r--r-- 1 pi pi 217  9月 12 05:50 dev_terminal.desktop

pi@raspberrypi:~ $ cat /home/pi/Desktop/check_audio.desktop 
[Desktop Entry]
Encoding=UTF-8
Type=Application
Name=Check audio
Comment=Check that the voiceHAT audio input and output are both working.
Exec=/home/pi/AIY-voice-kit-python/checkpoints/check_audio.py
Terminal=true
```

`check_audio.py` スクリプトを実行します.
実行パーミッションも立っていますし、スクリプト・ファイルの先頭行に `#!/usr/bin/env python3` とあり Python で 実行できるようになっています. (そういえば この先頭行、 [シバン](https://ja.wikipedia.org/wiki/%E3%82%B7%E3%83%90%E3%83%B3_%28Unix%29) って いうんですね)
```console
pi@raspberrypi:~ $ /home/pi/AIY-voice-kit-python/checkpoints/check_audio.py
```

実行すると、正しくスピーカー が 接続できていると スピーカー から "Front, Center" と 音声が流れます.
正しく聞こえたら、 `y` を 入力して次へ進めます.
```console
Playing a test sound...
Did you hear the test sound? (y/n) y
```

続いて、 `When you're ready, press enter and say 'Testing, 1 2 3'...` と 表示されるので、Enter キーを押してから "てすてぃんぐ わん、つー、すりー" って 話しかけます. いや、なんでも大丈夫ですが...
マイクを正しく接続できていれば、話しかけ時間３秒ぐらいで、すぐにスピーカーから話した内容が再生されます.
正しく聞こえたら、 `y` を 入力して次へ進めます.
```console
When you're ready, press enter and say 'Testing, 1 2 3'...
Recording...
Playing back recorded audio...
Did you hear your own voice? (y/n) y
```

２回とも `y` でしたら、たぶん正しく動いているでしょうってことで、 Enter キー を 押下して終了します.
```
The audio seems to be working.
Press Enter to close...
```

ということで、初期セットアップ 完了です！


- - - -
以下、Google AIY Voice Kit に直接は関係ないのですが、設定のまとめとして載せておきます. (主に自分用)

## ホスト名 と ロケール＆タイムゾーン を 設定
デフォルトのホスト名 `raspberrypi` は 他のラズパイを起動した際に被ると困るので必要に応じて変更しておきます. またロケールとタイムゾーンも併せておきます.
設定は `sudo raspi-config` コマンドを実行します.

ホスト名
- `2 Hostname` を 選択
- 設定したいホスト名を入力し、 `<Ok>` を 選択

ロケール
- `4 Localisation Options` を 選択
- `I1 Change Locale` を 選択
- `[*] en_GB.UTF-8 UTF-8` で スペースを押して `*` を 外す
- `[ ] ja_JP.UTF-8 UTF-8` で スペースを押して `*` を 付ける
- `<Ok>` を 選択
- Default locale for the system environment: と 聞かれるので、`ja_JP.UTF-8` を 選択し `<Ok>`

タイムゾーン
- `4 Localisation Options` を 選択
- `I2 Change Timezone` を 選択
- `Asia` → `Tokyo` と 選択

Wi-Fi の 国コード
- `4 Localisation Options` を 選択
- `I4 Change Wi-Fi Country` を 選択
- `<Select>` を 選択
- `JP Japan` を 選択
- `<Ok>` を 選択

最後に `Finish` を 選択し、再起動します.


## パッケージ の アップデート
```console
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get dist-upgrade -y
```

※ あまりに遅かったり、接続が悪いようでしたらミラー・サイトを設定します.
ミラー・サイトは こちら [RaspbianMirrors - Raspbian](https://www.raspbian.org/RaspbianMirrors) から確認できます. JAIST を 例に以下のように設定します.
```console
pi@raspberrypi:~ $ sudo nano /etc/apt/sources.list
# deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi
deb http://ftp.jaist.ac.jp/raspbian/ jessie main contrib non-free rpi
```


## SSH と 公開鍵認証 の 設定
ざっと抜粋になります. 詳しくは [Raspbian Jessie Lite の SSH/公開鍵認証 設定](/2016/11/29/Raspbian-Jessie-LiteのSSH公開鍵認証設定/) を ご参照ください.

まずは Windows 側でキー・ペアを生成します.
```console
c:\> ssh-keygen -t ed25519 -C ""
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\[username]/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\[username]/.ssh/id_ed25519.
Your public key has been saved in C:\Users\[username]/.ssh/id_ed25519.pub.
The key fingerprint is:
SHA256:T69X1O58qiKv7Sp/aPJTI+PpzUdOZDO+EcD5ldc8ON8
The key's randomart image is:
+--[ED25519 256]--+
|         . .  .o.|
|          +  oo.+|
|           o .ooo|
|            B ..E|
|        S .+ = . |
|        ooo.= . .|
|       . *.=.+ o |
|      o B+o.=   +|
|       B*OB+...XX|
+----[SHA256]-----+
```

続いて公開鍵を Raspberry Pi へ 送ります.
```console
c:\> scp %USERPROFILE%\.ssh\id_ed25519.pub pi@raspberrypi.local:~
pi@raspberrypi.local's password: [PASSWORD]
id_ed25519.pub                     100%   83     0.1KB/s   00:00
```

Raspberry Pi 側で、ホーム・ディレクトリに所定のディレクトリとファイル配置をします.
```console
pi@raspberrypi:~ $ mkdir ~/.ssh
pi@raspberrypi:~ $ chmod 700 ~/.ssh
pi@raspberrypi:~ $ cat ~/id_ed25519.pub >> ~/.ssh/authorized_keys
pi@raspberrypi:~ $ chmod 600 ~/.ssh/authorized_keys
pi@raspberrypi:~ $ rm -f ~/id_ed25519.pub
```

SSH サーバ で 公開鍵認証だけの設定にし、パスワード認証を禁止します.
`sudo systemctl reload ssh` した時の SSH 接続は残しておき、新しい SSH 接続で公開鍵認証が通ることを確認しておきます. 失敗すると作り直し or ディスプレイ/キーボードが必要 になるため注意が必要です.
```console
pi@raspberrypi:~ $ sudo nano /etc/ssh/sshd_config
# Authentication:
PermitRootLogin no
RSAAuthentication no

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no

pi@raspberrypi:~ $ sudo systemctl reload ssh
```

最後に pi ユーザ の パスワードを削除します.
```console
pi@raspberrypi:~ $ sudo passwd -d pi
```


## Wi-Fi パワーマネジメント の 設定
Wi-Fi の パワーマネージメントによってネットワークアクセスが不安定になったり、遅延が生じるケースがあります. その場合は `/etc/network/interfaces` の `iface wlan0` に `wireless-power off` を 追加しておきます.
```console
pi@raspberrypi:~ $ sudo nano /etc/network/interfaces
allow-hotplug wlan0
iface wlan0 inet manual
    wireless-power off
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```

※ IP アドレスの固定化については、こちら [固定 IP アドレス を 設定する](/2016/11/25/Raspbian-Jessie-LiteのWi-Fi設定/#固定 IP アドレス を 設定する) を ご参照ください.


## SDカード の 延命化
あまり気にすることないとの情報もありますが、手間がかけられるようでしたらやっておいてもいいかもしれません.
やることがいろいろあるので、ここでは まとめた手順で記載します. 詳細は こちら [Raspbian Jessie Lite の SDカード 延命化](/2017/03/16/Raspbian-Jessie-LiteのSDカード延命化/) を ご参照いただければ幸いです.

Swap 領域を削除し、テンポラリ領域 を RAM へ 配置します.
```console
pi@raspberrypi:~ $ sudo apt-get purge -y --auto-remove dphys-swapfile

pi@raspberrypi:~ $ sudo nano /etc/fstab
tmpfs           /tmp            tmpfs   defaults,size=32m,noatime,mode=1777  0       0
tmpfs           /var/tmp        tmpfs   defaults,size=16m,noatime,mode=1777  0       0
tmpfs           /var/log        tmpfs   defaults,size=32m,noatime,mode=0755  0       0
```

起動時に `/var/log` の ディレクトリ構成を復元するようにスクリプトを配置します.
`/etc/rc.local` の `exit 0` の 前に 下記を追記します.
```console
pi@raspberrypi:~ $ sudo nano /etc/rc.local
mkdir -p /var/log/apt
mkdir -p /var/log/fsck
mkdir -p /var/log/ntpstats
mkdir -p /var/log/samba
chmod 750 /var/log/samba
chown ntp:ntp /var/log/ntpstats
chown root:adm /var/log/samba
```

rsyslog の ログ出力を抑制します. 以下の部分をコメントアウトします.
```console
pi@raspberrypi:~ $ sudo nano /etc/rsyslog.conf
#daemon.*                       -/var/log/daemon.log
#kern.*                         -/var/log/kern.log
#lpr.*                          -/var/log/lpr.log
#mail.*                         -/var/log/mail.log
#user.*                         -/var/log/user.log
#mail.info                      -/var/log/mail.info
#mail.warn                      -/var/log/mail.warn
#mail.err                       /var/log/mail.err
#news.crit                      /var/log/news/news.crit
#news.err                       /var/log/news/news.err
#news.notice                    -/var/log/news/news.notice
#*.=debug;\
#       auth,authpriv.none;\
#       news.none;mail.none     -/var/log/debug
```

RAM へ 配置したディレクトリを削除し、再起動します.
```console
pi@raspberrypi:~ $ sudo rm -fr /tmp
pi@raspberrypi:~ $ sudo rm -fr /var/tmp
pi@raspberrypi:~ $ sudo rm -fr /var/swap
pi@raspberrypi:~ $ sudo reboot
```

ext4 ファイルシステム の ジャーナル を 無効化します.
```console
pi@raspberrypi:~ $ sudo umount /dev/mmcblk0p2
pi@raspberrypi:~ $ sudo tune2fs -O ^has_journal /dev/mmcblk0p2
tune2fs 1.43.3 (04-Sep-2016)
The has_journal feature may only be cleared when the filesystem is
unmounted or mounted read-only.

pi@raspberrypi:~ $ sudo reboot
```


## NTP に 日本のサーバを設定
```console
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


## CPU の 動作クロック設定
通常は問題ないですが、常にハイ・パフォーマンスで動作させたり、省エネで動かす場合に設定します.
下記は常にハイ・パフォーマンスの例です. 詳しくは こちら [CPU の 動作クロック設定](/2017/03/19/Raspbian-Jessie-Liteその他設定/#CPU の 動作クロック設定) を ご参照ください.
```console
pi@raspberrypi:~ $ sudo apt-get install cpufrequtils -y --no-install-recommends
pi@raspberrypi ~ $ sudo cpufreq-set -g performance

pi@raspberrypi:~ $ sudo cp /usr/share/doc/cpufrequtils/examples/cpufrequtils.sample /etc/default/cpufrequtils
pi@raspberrypi:~ $ sudo nano /etc/default/cpufrequtils
ENABLE="true"
GOVERNOR="performance"
MAX_SPEED=1200000
MIN_SPEED=600000
```

※ `MAX_SPEED` と `MIN_SPEED` は `cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_frequencies` から出てきた数字を設定します. 上記は Raspberry Pi 3 の ものになります.


## .bashrc の 設定
ここは完全にお好みなので、ご参考までに. (最小限の設定ですが)
```console
pi@raspberrypi:~ $ nano ~/.bashrc
alias ls='ls --color=auto'
alias ll='ls --color=auto --format=verbose'
alias la='ls --color=auto --format=verbose --all'
alias cp='cp --interactive'
alias mv='mv --interactive'
alias rm='rm --interactive'
alias free='free -h'
alias grep='grep --color=auto'

pi@raspberrypi:~ $ source ~/.bashrc
```


## 基盤 の LED を 消灯する
基板上の ACT 緑 と PWR 赤 の LED が 気になる場合は消すことができます. 消してしまうと通電しているのかわからないという欠点もありますが、夜に赤く光らないので目に優しいかもしれません. `led0` PWR で、`led1` が ACT です.
```console
pi@raspberrypi:~ $ echo 0 | sudo tee /sys/class/leds/led0/brightness
pi@raspberrypi:~ $ echo 0 | sudo tee /sys/class/leds/led1/brightness

pi@raspberrypi:~ $ echo "dtparam=act_led_trigger=none,act_led_activelow=on" | sudo tee -a /boot/config.txt
pi@raspberrypi:~ $ echo "dtparam=pwr_led_trigger=none,pwr_led_activelow=on" | sudo tee -a /boot/config.txt
```


## 最後にリブートしておく
必要はないと思いますが、いろいろ設定したのでリブートしておきます.
```console
pi@raspberrypi:~ $ sudo reboot
```



## Google AIY Voice Kit に 必要なもの

### Raspberry Pi 3
ラズパイがないと始められないので必要になります. 箱の中に入れてしまうので、ほぼ占有されます. 
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41zcKgUQXtL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" >Raspberry Pi 3 MODEL B 【RS正規流通品】</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Raspberry Pi 2016-01-28    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Raspberry Pi 3 の 電源
Raspberry Pi 3 は 5V/3A の 電源が必要になります. スマホの充電アダプタでは出力が足りない場合もあるので確認が必要です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41p5wekKaIL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" >Raspberry Pi用電源セット(5V 3.0A)－Pi3フル負荷検証済</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> TechShare     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### マイクロ SD カード
ラズパイ の OS や ストレージに必要です. 16GB あれば十分だと思いますが、用途次第なので お好みのサイズで用意します.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51JBMptiJgL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" >【Amazon.co.jp限定】Transcend microSDHCカード 16GB Class10 UHS-I対応 無期限保証 Nintendo Switch / 3DS 動作確認済 TS16GUSDU1PE (FFP)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> トランセンド・ジャパン 2015-10-02    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### ドライバーセット
スピーカー配線のコネクタをしめるのに使います. 細いドライバーであればなんでも大丈夫です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0016VCJLU%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/512vdu5RFFL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0016VCJLU%2Fref%3Dnosim" target="_blank" >ベッセル(VESSEL) ドライバーセット ファミドラ8 TD-800</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> ベッセル     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%25E3%2583%2589%25E3%2583%25A9%25E3%2582%25A4%25E3%2583%2590%25E3%2583%25BC%25E3%2582%25BB%25E3%2583%2583%25E3%2583%2588%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2F%25E3%2583%2589%25E3%2583%25A9%25E3%2582%25A4%25E3%2583%2590%25E3%2583%25BC%25E3%2582%25BB%25E3%2583%2583%25E3%2583%2588%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### 両面テープ
マイクを箱の天板に貼り付けるのに使います. 持っていそうで意外となかったりも. (今回は無かった...)
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB000IGZZ4M%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51n8VDAfiyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB000IGZZ4M%2Fref%3Dnosim" target="_blank" >ニチバン 両面テープ ナイスタック 一般タイプ 15mm×20m NW-15</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> ニチバン     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%25E3%2583%258B%25E3%2583%2581%25E3%2583%2590%25E3%2583%25B3%2520%25E4%25B8%25A1%25E9%259D%25A2%25E3%2583%2586%25E3%2583%25BC%25E3%2583%2597%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2F%25E3%2583%258B%25E3%2583%2581%25E3%2583%2590%25E3%2583%25B3%2520%25E4%25B8%25A1%25E9%259D%25A2%25E3%2583%2586%25E3%2583%25BC%25E3%2583%2597%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Google  AIY
Google  AIY Voice Kit そのもの. 一応載せておきますが 出始めなので Amazon.co.jp さん だと、高いですね. [KSY](https://raspberry-pi.ksyic.com/news/page/nwp.id/65) さん 待ちでしょうか. [Pimoroni](https://shop.pimoroni.com/products/google-aiy-voice-kit) さん は 2017年11月現在 入荷待ちで ￡20.83 + 送料 ￡5.50 ＝ 約4,000円です.
Pimoroni さん での お買い物方法は、こちら「[Raspberry Pi Zero の 購入](/2016/11/13/Raspberry-Pi-Zeroの購入/)」も ご参照いただければ幸いです.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0779BJY1N%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/411Ax0K6zqL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0779BJY1N%2Fref%3Dnosim" target="_blank" >Google AIY Voice Kit - 日本語補足説明書付き</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Google     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DGoogle%2520AIY%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FGoogle%2520AIY%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
セットアップ系はコツコツ積み重ねてきたので記事が分散してしまったので、どこかで整理したいなぁ.
