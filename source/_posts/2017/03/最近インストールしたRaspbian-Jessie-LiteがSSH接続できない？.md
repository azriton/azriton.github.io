---
title: 最近インストールした Raspbian Jessie Lite が SSH 接続できない？
date: 2017-03-07
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

[Crystal Signal Pi が 届いた](/2017/03/04/CrystalSignalPiが届いた！/) ので、さっそく Raspbian を セットアップし、Crystal Signal Pi の ソフトウェアを入れて発光色を変えてみたいと思ったところ、まさかの SSH ログインができないというトラブルがあり、意外とはまったのでメモしておきます.

**作業環境**
- Windows 7
- Raspbian Jessie Lite (Release date: 2017-03-02)


## トラブルの状況
新規に Raspbian Jessie Lite を SD カードに焼いて(いや、SD だから焼かないですがイメージとして...)、Crystal Signal Pi を 搭載した Raspberry Pi 3 Model B を 起動しました.
そして初期設定のために SSH を したところ、どうしてもつながらないという状況が発生.

```shell-session
c:\Temp> ssh pi@raspberrypi.local
ssh: connect to host raspberrypi.local port 22: Unknown error

c:\Temp> ssh -v pi@raspberrypi.local
OpenSSH_7.3p1 Microsoft_Win32_port_with_VS, OpenSSL 1.0.2d 9 Jul 2015
debug1: Connecting to raspberrypi.local [ea13::2f1:0d1:adXX:dab%10] port 22.
debug1: socket:444, io:000000000030EE60, fd:3
debug1: finish_connect - ERROR: async io completed with error: 107, io:000000000030EE60
debug1: connect to address ea13::2f1:0d1:adXX:dab%10 port 22: Unknown error
debug1: close - io:000000000030EE60, type:1, fd:3, table_index:3
debug1: Connecting to raspberrypi.local [192.168.0.1XX] port 22.
debug1: socket:404, io:000000000030EE60, fd:3
debug1: finish_connect - ERROR: async io completed with error: 107, io:000000000030EE60
debug1: connect to address 192.168.0.1XX port 22: Unknown error
debug1: close - io:000000000030EE60, type:1, fd:3, table_index:3
ssh: connect to host raspberrypi.local port 22: Unknown error
```

デバッグ・ログを出してみましたが私のレベルではよくわからず... SSH の デバッグログに出ている IP が 取れているのは ルーター側でも確認できていますし、Ping も 届いていました. ネットワーク的には正しく接続できているようです.


## これまでのラズパイは？
久々にインストールから作業したので、だいぶ間が空いてましたが当時使っていた古いイメージ `2016-09-23-raspbian-jessie-lite.img` が 残っていたので、こちらを新規で使ったところ SSH 接続できました. あれ？

ということは、これまでのバージョンアップの中で何かが変わったのかもしれません. ということで、ようやくリリースノートをあたります. (もっと早く見ようよ、自分 orz)
以下 [http://downloads.raspberrypi.org/raspbian/release_notes.txt](http://downloads.raspberrypi.org/raspbian/release_notes.txt) より抜粋.
```Text
2017-03-02:
  * Updated kernel and firmware (final Pi Zero W support)
2017-02-16:
  * Chromium browser updated to version 56
  * Adobe Flash Player updated to version 24.0.0.221
  * RealVNC Server and Viewer updated to version 6.0.2 (RealVNC Connect)
  * Sonic Pi updated to version 2.11
  * Node-RED updated to version 0.15.3
  * Scratch updated to version 120117
  * Detection of SSH enabled with default password moved into PAM
  * Updated desktop GL driver to support use of fake KMS option
  * Raspberry Pi Configuration and raspi-config allow setting of fixed HDMI resolution
  * raspi-config allows enabling of serial hardware independent of serial terminal
  * Updates to kernel and firmware
  * Various minor bug fixes and usability and appearance tweaks
2017-01-11:
  * Re-release of the 2016-11-25 image with a FAT32-formatted boot partition
2016-11-25:
  * SSH disabled by default; can be enabled by creating a file with name "ssh" in boot partition
  * Prompt for password change at boot when SSH enabled with default password unchanged
  * Adobe Flash Player included
  * Updates to hardware video acceleration in Chromium browser
  * Greeter now uses background image from last set in Appearance Settings rather than pi user
  * Updated version of Scratch
  * Rastrack option removed from raspi-config and Raspberry Pi Configuration
  * Ability to disable graphical boot splash screen added to raspi-config and Raspberry Pi Configuration
  * Appearance Settings dialog made tabbed to work better on small screens
  * Raspberry Pi Configuration now requires current password to change password
  * Various small bug fixes
  * Updated firmware and kernel
```

ありました！ ちょうど、これまで使っていた `2016-09-23` の 次のリリース **`2016-11-25`** で変わったようです.
SSH は デフォルトで起動しないようになっていて、ブート・パーティション に "ssh" というファイルを置くことで有効にできるようです. これによって、接続ができなかったのですね...
```Text
2016-11-25:
  * SSH disabled by default; can be enabled by creating a file with name "ssh" in boot partition
```


## これからのセットアップ
セットアップについては、これまで通り SD カードをフォーマットして、イメージを書き込むところまでは同じになります. 最後に "ssh" というファイルを置くという手順が増えました.

Windows からは、エクスプローラー で SD カード の ドライブを開いて、"ssh" というファイルを作ります.

作るファイルのもとは何でもよく拡張子を削除して作るだけになります. 今回はビットマップ イメージを選び、最初から入力されていた `新しいビットマップ イメージ.bmp` を 消して `ssh` としました. "拡張子を変更すると、ファイルが使えなくなる可能性があります。" と 警告表示されますが、今回は特に問題ないので [はい] を クリックして進めます.
![](/images/raspi/raspbian-jessie-lite/11.png)

"ssh" というファイルが置かれました. これで完了、後は起動するだけです.
![](/images/raspi/raspbian-jessie-lite/12.png)


## 関連する記事の更新
- [Raspbian Jessie Lite の インストール](/2016/11/19/Raspbian-Jessie-Liteのインストール/)


- - - -
ファイルを置いたら何時もと変わりなくアクセスできるようになりました. SSH 接続できないと各種設定ができないように思いますが、どうして SSH を デフォルトで無効にしたのだろう...
