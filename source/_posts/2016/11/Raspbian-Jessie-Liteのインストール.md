---
title: Raspbian Jessie Lite の インストール
date: 2016-11-19
updated: 2017-11-12
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspberry Pi に 公式の OS である Raspbian の 最小構成 Lite を インストールしたいと思います.
NOOBS(New Out Of the Box Software) という OS の インストーラーが用意されており、これを使うことで簡単に Raspberry Pi へ OS を インストールすることができますが、ディスク容量を圧迫する、USB の キーボード と マウス が 必須であることから、OS イメージ を 直接 SD カードへ書き込む方法を取ることにします.

**作業環境**
- Windows 7
- Raspbian Jessie Lite


## SD カード の 準備
*2017.03.07 追記*
*[ラズパイ の OS イメージを焼くときは Etcher が 便利 ＆ UI カッコいい](/2017/11/12/ラズパイのOSイメージを焼くときはEtcherが便利＆UIカッコいい/) の 記事を追加しました. こちらの手順の方が簡単なので、よろしければ こちらも ご参照ください.*


使用する Raspberry Pi の モデル に 合わせた SD カード を 用意します.
今回 は Raspberry Pi Zero を 使いますので、microSD カード と PC で 使うための SD Adapter を 用意しました.

続いて、SD カード を フォーマットします.
OS の ツールではうまくいかないケースがあるので、SD カード フォーマッター を ダウンロードして使いました.
ダウンロードは、こちらから → [https://www.sdcard.org/jp/downloads](https://www.sdcard.org/jp/downloads)

SD カード フォーマッター を 起動し、[オプション設定] を クリックします.
![](/images/raspi/raspbian-jessie-lite/01.png)

論理サイズ調整 を [ON] に 設定し、[OK] を クリックします.
通常は OFF で 問題ないとのことですが、OS 付属のツールでフォーマットしたりすると論理サイズが異なったりと問題が出るケースがあるらしいです. その場合は ON に することで解決できるとのことですが、後でトラブルがあった際に問題の切り分けをするのもめんどくさいので ON に しました.
論理サイズ調整については、SD カード フォーマッター の [マニュアルの記載](https://www.sdcard.org/jp/downloads/formatter_4/SDFormatter_4jp.pdf) や [Raspberry Pi フォーラム の ポスト](https://www.raspberrypi.org/forums/viewtopic.php?f=91&t=83372&p=651745#p677748) などに情報があります.
![](/images/raspi/raspbian-jessie-lite/02.png)

元のウィンドウに戻るので、[Drive] と [Volume Label] が 正しいかを確認し、フォーマットを実行してよければ [フォーマット] を クリックします.
クイックフォーマットと、フォーマット中に関する注意事項のダイアログが表示されます. 内容を確認し問題なければ、それぞれ [OK] を クリックして作業を進めます.
![](/images/raspi/raspbian-jessie-lite/03.png)

フォーマットが終わったらダイアログが出るので、SD カード を 取り出し、[OK] を クリックし、また元のウィンドウ の SD カード フォーマッター も [終了] を クリックして閉じます.
![](/images/raspi/raspbian-jessie-lite/04.png)


## OS イメージ の 書き込み
Raspbian の サイト [https://www.raspberrypi.org/downloads/raspbian](https://www.raspberrypi.org/downloads/raspbian) から Raspbian Jessie Lite を ダウンロードします.
ここでは、[C:\Develop\images] に ダウンロードしたものとします.
![](/images/raspi/raspbian-jessie-lite/05.png)

ダウンロードしたファイルが正しいかハッシュ値を確認します.
Windows PowerShell 4.0 以降は、Get-FileHash が あるので、それを使います.
Raspbian の ウェブサイトに記載されているハッシュ値と同じなので正しくダウンロードできたようです.
```
c:\> powershell Get-FileHash -Algorithm SHA1 "C:\Develop\images\2016-09-23-raspbian-jessie-lite.images"

Algorithm  Hash
---------  ----
SHA1       3A34E7B05E1E6E9042294B29065144748625BEA8
```

続いて、OS イメージ を SD カード に 書き込みます.
今回は [公式ドキュメント](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md) で あげられていた [Win32 Disk Imager] を 使います.
ダウンロードは、こちらから → [https://sourceforge.net/projects/win32diskimager/](https://sourceforge.net/projects/win32diskimager/)

Image File に ダウンロードして ZIP を 解凍した images ファイルを指定し、[Write] を クリックします.
ここでは、[C:\Develop\images] に 解凍したものとします.
![](/images/raspi/raspbian-jessie-lite/06.png)

SD カード と ドライブを確認し、問題なければ [Yes] を クリックします.
![](/images/raspi/raspbian-jessie-lite/07.png)

書き込みが完了したら、Complete の ダイアログが出るので、[OK] を クリックし、元のウィンドウ の Win32 Disk Imager から [Exit] を クリックして閉じます.
![](/images/raspi/raspbian-jessie-lite/08.png)
![](/images/raspi/raspbian-jessie-lite/09.png)


## SSH 有効化 の ファイル作成 (2017.03.07 追記)
本ポストを書いていた 2016.11.19 現在 [`2016-11-25-raspbian-jessie-lite.zip`](https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2016-11-29) の OS イメージ を 使用していましたが、次のリリース `2016-11-25-raspbian-jessie-lite.zip` からは SSH が デフォルトで無効になりました. そのため 2017年3月 現在、以下の手順も必要となり追記します.

ブート・パーティション に "ssh" というファイルを作成します.
Windows からは、エクスプローラー で SD カード の ドライブを開いて、"ssh" というファイルを作成します.

作るファイルのもとは何でもよく拡張子を削除して作るだけになります. 今回はビットマップ イメージを選び、最初から入力されていた `新しいビットマップ イメージ.bmp` を 消して `ssh` としました. "拡張子を変更すると、ファイルが使えなくなる可能性があります。" と 警告表示されますが、今回は特に問題ないので [はい] を クリックして進めます.
![](/images/raspi/raspbian-jessie-lite/11.png)

"ssh" というファイルが置かれました. これで完了、後は起動するだけです.
![](/images/raspi/raspbian-jessie-lite/12.png)

このあたりについては [最近インストールした Raspbian Jessie Lite で SSH 接続できない？](/2017/03/07/最近インストールしたRaspbian-Jessie-LiteがSSH接続できない？/) の ポストも、もしよろしければご覧ください.


## SD カード 作成時 に Wi-Fi 設定をしておく (2017.03.07 追記)
Raspberry Pi 3 や Raspberry Pi Zero W の OS イメージ を Raspbian で 作る際に、あらかじめ `wpa_supplicant.conf` を 作っておくことで、初回起動時から Wi-Fi へ 接続しておくことができます.

詳しくは、こちら [ラズパイ の OS イメージを焼くときは Etcher が 便利 ＆ UI カッコいい](/2017/11/12/ラズパイのOSイメージを焼くときはEtcherが便利＆UIカッコいい/) を ご参照ください.


## Raspbian Jessie Lite 起動！
OS を 書き込んだ SD カード を Raspberry Pi へ セットし、電源を接続して起動します.
HDMI を ディスプレイ や テレビ に 接続することで、起動画面を確認することができます.
各種デーモンが [OK] で 起動していき、最後に [raspberrypi login: ] が 表示されたら起動成功です.
![](/images/raspi/raspbian-jessie-lite/10.png)



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
つきにラズパイを起動することができました！！
まだ初期設定などをしていく必要がありますが、まずは立ち上がったことに感動です. 引き続き設定していって、いろいろと楽しみたいところです.
