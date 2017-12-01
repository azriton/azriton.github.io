---
title: ラズパイ の OS イメージを焼くときは Etcher が 便利 ＆ UI カッコいい
date: 2017-11-12
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
- Google AIY
---

![](/images/raspi/etcher/etcher.png "Etcher")

[Google AIY Voice Kit を 組み立てた](/2017/11/09/ラズパイの簡易スマート・スピーカーGoogle-AIYを組み立てる/) ので、OS の イメージを用意して起動、と行きたいところですが、OS イメージ を マイクロ SD に 焼く方法が気になったので確認と整理します.

**作業環境**
- Windows 10 64 bit
- Etcher for Windows x64
- Raspberry Pi 3 Model B
- Raspbian Jessie Lite
- Google AIY Voice Kit


## 何があった？
Google AIY Voice Kit の 説明書 P. 34 からの Chapter Four / Set up the software で "Burn the image to a microSD card using a program like Etcher(etcher.io) on a Mac, Windows, or Linux computer." という文章がありました.

また Web の リファレンス では [Get the Voice Kit SD Image - Assembly Guide](https://aiyprojects.withgoogle.com/voice/#assembly-guide-1-get-the-voice-kit-sd-image) に "Write the image to an SD card using a card writing utility (Etcher.io is a popular tool for this)" とあり、 **popular tool for this** と 気になる記述もあります.

普段だったら使い慣れているソフトで サクッと SD カードを作ってしまうのですが、せっかく新しいことを始めているのだから、調べてみることにしました.


## Etcher とは？
[Etcher](https://etcher.io/) は [Resin.io](https://resin.io/) さん が OSS として開発しているツールで、トップエージに大きく "Burn. Better." と 書かれている通り、SD カード や USB メモリ に OS イメージを安全で簡単に焼いてくれるツールです.
ところで CD ではないのに、なんで Burn、焼くんだろう...

Resin.io さん は、サービス自体は使ったことないのですが [Raspbian Jessie Lite で Docker する](/2017/03/30/Raspbian-Jessie-LiteでDockerする-インストール編/) 際にも、参考にさせていただいたりとお世話になっております. ありがとうございます.

[Electron](https://electron.atom.io/) で できているので、Windows、Mac、Linux の クロス・プラットフォームで動作します.

そして何より素晴らしいのが、３ステップ完了できるということ.
`img` ファイルだけでなく、 `zip` などにも対応しているので、解凍するという作業無しで直接メディアを作ることができます.

Etcher は Raspbian 公式ドキュメント の [INSTALLING OPERATING SYSTEM IMAGES](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) や The MagPi の [BURN SD CARDS WITH ETCHER](https://www.raspberrypi.org/magpi/pi-sd-etcher/) などでも紹介されている方法になります.
※ なのに、なぜか [INSTALLING OPERATING SYSTEM IMAGES USING WINDOWS](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md) へ 行って、`Win32DiskImager` を 使ってたんだよなぁ...


## インストール
Etcher の ウェブサイト [https://etcher.io/](https://etcher.io/) へ アクセスします.
すでに OS を 判別してダウンロードボタンを作ってくれていますので、ボタンをクリックしてダウンロードします.
![](/images/raspi/etcher/01.png)

チェックサムは見つからなかったので確認できず. 残念.
ダウンロードしたファイルをダブルクリックしてインストーラを起動します.
![](/images/raspi/etcher/02.png)

ライセンスの確認が表示されるので、内容を確認し同意できるようだったら [同意する] ボタンをクリックして進めます. 同意できなかったら利用できません. キャンセルしましょう.
![](/images/raspi/etcher/03.png)

特に選択しなくインストールが進み、Etcher が 自動的に起動します.


## イメージを焼く、前に設定をしておく
さっそく SD カード に Google AIY Voice Kit の イメージを焼きたいところですが、ちょっと設定をしておきます.
ウィンドウ右上、⚙ 歯車 の アイコンをクリックします.
![](/images/raspi/etcher/04.png)

設定が表示されるので、[Eject on success] の チェックを外します.
ここにチェックがついていると、イメージを焼いた後に SD カード の マウントを解除してしまうため、Raspbian の OS イメージを焼いた後に `ssh` ファイルを作成するのに、SD カードの入れ直しが必要になってしまうためです.
チェックを外したらウィドウ右上 [< Back] を クリックして戻ります.
![](/images/raspi/etcher/05.png)

ウィンドウ中段 の 左 [Select image] を クリックし、焼きたい OS イメージを選択します.
`img` や `iso` だけでなく、`zip`, `bz2`, `dmg`, `dsk`, `etch`, `gz`, `hddimg`, `raw`, `rpi-sdimg`, `sdcard`, `xz` などにも対応しています.
今回は `aiyprojects-2017-09-11.img.xz` を 選択しました. `xz` を 解凍する必要がないので助かります.
![](/images/raspi/etcher/04.png)

イメージを選択すると、フォーカス が ウィンドウ中段 の 右 [Flash!] に 移ります.
SD Card が 挿入済みのため、一気に [Flash!] へ 進み、事実上 ２ステップです. 素晴らしい！
なお、ドライブが違う場合は ウィンドウ中央 の [Change] から変更できます.
![](/images/raspi/etcher/06.png)

用意ができたら [Flash!] を クリックし、焼きます.
ところで、なんでここは Burn じゃないんだろう...
![](/images/raspi/etcher/07.png)

しばらく待つと焼き終わります.  フォーマット と 検証 も 自動的に行っておいてくれるので簡単です.
何よりこれ一つで終わりというのがいいですね.
![](/images/raspi/etcher/08.png)


## Raspbian / Google AIY Voice Kit SD image の 初期ファイル配置
SD カード が 焼き終わったら、必要に応じて エクスプローラー で SD カード の ドライブを開き、初期ファイルを配置しておきます.

### SSH 接続を許可する ssh ファイル
SSH で アクセスできるようにするには `ssh` というファイルを置いておく必要があります.
エクスプローラーの右クリック から [新規作成] を 選択し、適当なファイルを選択して、ファイル名を `ssh` に して作成します. 
“拡張子を変更すると、ファイルが使えなくなる可能性があります。” と 警告表示されますが、今回は特に問題ないので [はい] を クリックします.
![](/images/raspi/etcher/09.png)

このあたりにつきましては、下記の記事も合わせてご参照いただければ幸いです.
- [Raspbian Jessie Lite の インストール](/2016/11/19/Raspbian-Jessie-Liteのインストール/)
- [最近インストールした Raspbian Jessie Lite が SSH 接続できない？](/2017/03/07/最近インストールしたRaspbian-Jessie-LiteがSSH接続できない？/)


### Wi-Fi 設定 の wpa_supplicant.conf ファイル
Raspberry Pi 3 や Raspberry Pi Zero W を 使う場合は Wi-Fi が 利用できます.
あらかじめ `wpa_supplicant.conf` ファイルを用意しておくことで、起動時に 所定の場所である `/etc/wpa_supplicant/wpa_supplicant.conf` へ ファイルを移動し Wi-Fi へ 接続しておいてくれます.
![](/images/raspi/etcher/10.png)

ファイルの内容は以下で、 `ssid` と `psk` に 接続する Wi-Fi の 設定を記述します.
ファイルの改行コードは Windows の `CRLF` ではなく `LF` のため、メモ帳ではなく改行コードが設定できるエディタを使います.
```
country=JP
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid="----Your-WiFi-SSID----"
        psk="----PLAIN-PASSPHRASE----"
}
```

このあたりにつきましては、下記の記事も合わせてご参照いただければ幸いです.
- [Raspbian Jessie Lite の Wi-Fi 設定](/2016/11/25/Raspbian-Jessie-LiteのWi-Fi設定/)



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
いままでイメージをダウンロードしてから、解凍して、SDカードをフォーマット、焼き付けと手間がかかってましたが、Etcher の おかげで簡単に SD カードが作れるようになりました. 助かります.
今回はインストーラーで導入しましたが、ポータブル版もあるので開発環境セットとしてまとめおくのも手ですね.

公式サイト の Features に "Beautiful Interface - Who said burning SD cards has to be an eyesore." とあるように、カッコいい UI だと思います. さすが IoT 管理プラットフォーム屋さんです.
