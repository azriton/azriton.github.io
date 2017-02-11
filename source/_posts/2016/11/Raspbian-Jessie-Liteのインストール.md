---
title: Raspbian Jessie Lite の インストール
date: 2016-11-19
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


## Raspbian Jessie Lite 起動！
OS を 書き込んだ SD カード を Raspberry Pi へ セットし、電源を接続して起動します.
HDMI を ディスプレイ や テレビ に 接続することで、起動画面を確認することができます.
各種デーモンが [OK] で 起動していき、最後に [raspberrypi login: ] が 表示されたら起動成功です.
![](/images/raspi/raspbian-jessie-lite/10.png)



- - - -
つきにラズパイを起動することができました！！
まだ初期設定などをしていく必要がありますが、まずは立ち上がったことに感動です. 引き続き設定していって、いろいろと楽しみたいところです.
