---
title: Windows 7 で OpenSSL を 使えるようにする
date: 2017-01-27
comments: true
categories: 開発環境
tags:
- Windows
---

![](/assets/openssl/openssl.png "OpenSSL")

ちょっと OpenSSL が 必要なことってありませんか？(いや、そうそう無いか...)
普段使用している環境は Windows で、OpenSSL を 簡単に使うことができません. ちょうど OpenSSL が 必要な困った事案が発生、また環境構築にはまったので記録を残しておきたいと思います.

**作業環境**
- Windows 7 64bit
- Microsoft Windows SDK v7.1
- Strawberry Perl 5.24.0.1 64bit
- OpenSSL 1.0.2j LTS


## ビルド環境の準備
OpenSSL は バイナリを配布していません. 自分でビルドしないと使えません. インターネットは広いので[親切な方がビルドしたものを配布](https://www.openssl.org/community/binaries.html)してくれていますが、今回は自前でビルドする環境を用意したいと思います.


### C++ コンパイラ の 用意
まず C++ の コンパイラが必要です. "April 2005 Platform SDK is equipped -- OpenSSL/INSTALL.W64" と なっていますが、"[古い SDK が必要な場合は、以下の MSDN サブスクリプションのダウンロードをご検討ください -- JAPAN Platform SDK（Windows SDK） Support Team Blog](https://blogs.msdn.microsoft.com/japan_platform_sdkwindows_sdk_support_team_blog/2011/04/21/windows-sdk/)" とのことで、有償です...
というか、ちゃんと現在の OS に 合わせた SDK を 使う必要があるので、Windows 7 の SDK を 取ってきます.
こちらから [Microsoft Windows SDK for Windows 7 and .NET Framework 4
](https://www.microsoft.com/en-us/download/details.aspx?id=8279)からダウンロードしてインストールします.
最低限のインストール・オプション は 以下となります.
- Windows Headers and Libraries
- Visual C++ Compilers
- Microsoft Visual C++ 2010 (Redistributable)
![](/assets/openssl/01.png)



### Perl の 用意
続いて、Perl が 必要となります. これまた Windows ユーザ としては困るところです...
ドキュメントでは "You can run under Cygwin or you can download
 ActiveState Perl -- OpenSSL/INSTALL.W64" となっています. [ActivePerl(http://www.activestate.com/activeperl)] は 沢山お世話になり今回もお世話になるところですが、最近は [Strawberry Perl](http://strawberryperl.com/) なるものもあるようです.

`cpan` や `ppm` などの 違いがあったりするようですが、今回 Strawberry Perl を 選択したのは、PortableZIP edition が あったからになります.
OpenSSL の `Configure` を 実行したいだけなので、Zip を 解凍するだけで使えるはとてもありがたいです. できればインストーラであれこれ入れてほしくないし、フォルダの削除だけで全て無かったことにできるのは助かります. ということで、Strawberry Perl で 行きます.

ウェブサイトからダウンロードして解凍して終了です.
今回は `C:\Develop\sdk\strawberry-perl-5.24.0.1-64bit-portable` に 解凍したものとします. README.txt に 書かれていますが、スペースや日本語が含まれないディレクトリにするとのことです.

*参考情報*
Perl の 選択肢については、以下のサイトを参考にさせて頂きました. すばらしい情報ありがとうございます！
- [Windows で Perl をはじめよう！ - JPerl Advent Calendar 2010 Win32 Track](http://perl-users.jp/articles/advent-calendar/2010/win32/1)
- [ActivePerl以外の選択肢 - StrawberryPerlを使う - Network Tactics](http://www.nwt.jp/document/strawberryperl.php)


## OpenSSL を ビルド
OpenSSL の ソース を ダウンロードします. [OpenSSL の ウェブサイト](https://www.openssl.org/) から取得しますが、Web サーバ で 使うわけではないので、安定版ということで 今回は LTS(Long Term Support) の   openssl-1.0.2j.tar.gz を 選択しました.

ダウンロードしたアーカイブを解凍します.
今回は `C:\Develop\tool\openssl-1.0.2j` に 解凍したものとします.

続いて `Windows SDK 7.1 Command Prompt` を 起動します. (通常 の コマンド プロンプト ではないことに注意)
![](/assets/openssl/02.png)

```shell-session
c:\Develop\tool\openssl-1.0.2j> set PATH=%PATH%;c:\Develop\sdk\strawberry-perl-5.24.0.1-64bit-portable\perl\bin
c:\Develop\tool\openssl-1.0.2j> perl Configure VC-WIN64A
c:\Develop\tool\openssl-1.0.2j> ms\do_win64a
c:\Develop\tool\openssl-1.0.2j> nmake -f ms\ntdll.mak
c:\Develop\tool\openssl-1.0.2j> cd out32dll
c:\Develop\tool\openssl-1.0.2j> ..\ms\test
...(省略)
passed all tests

c:\Develop\sdk\openssl-1.0.2j\out32dll>openssl version
OpenSSL 1.0.2j  26 Sep 2016
```

無事、ビルドできました！


## 鍵生成 や 自己署名証明書発行 の テスト
OpenSSL の 利用にあたっては、通常 の コマンド プロンプト で 大丈夫です.
実行に当たっては環境変数の設定が必要なものがあったりしますので注意が必要です. (ちゃんとインストールしようよということでもあるのですが、ちょっと使うだけだから... と 言い訳してみる)
```shell-session
c:\Temp> set PATH=%PATH%;c:\Develop\tool\openssl-1.0.2j\out32dll

c:\Temp> set RANDFILE=%TEMP%\.rnd
c:\Temp> set OPENSSL_CONF=c:\Develop\tool\openssl-1.0.2j\apps\openssl.cnf

c:\Temp> openssl genrsa -aes256

c:\Temp> openssl genrsa -out test.key 4096
c:\Temp> openssl req -x509 -new -key test.key -out test.pem
```


## はまったところ

### Visual C++ Compilers が 選択できない
.NET 4 (4.x ではなく **4**) が 必要になります. しかも、新しいバージョンが入っているとインストールできないというトラップがありました... 新しいバージョンが入っている場合はアンインストールして、古いバージョンから順番に入れなおす必要があります.
.NET の バージョンについては、こちら [Tech TIPS：.NET Frameworkのバージョンを整理する - ＠IT](http://www.atmarkit.co.jp/ait/articles/1211/16/news093.html) が 詳しいです.
![](/assets/openssl/03.png)
![](/assets/openssl/04.png)


### インストーラは正常終了しているのに、インストールされていない
何が起こったのかよくわからない事象で、見事にどはまりしました orz
インストーラは正常終了したように見えているのに、肝心のプログラムが入っていない状況が起こりました. 1 GB の インストールにしては、やたら早く終わったなぁというのが気になったぐらいです.
![](/assets/openssl/05.png)

どうやら、こちら [Windows SDK for Windows 7.1 をインストールするとエラーが発生する - Windows - Project Group](http://www.projectgroup.info/tips/Others/comm_0004.html) の 現象だったようです. "A problem occurred..." なんて表示されなかったようにも思いますが、色が付いているわけでもないので...
上記サイトの情報をもとに、Microsoft Visual C++ 2010 x64/x86 Redistributable を 確認したところ、見事に x64 の方が入っていました. アンインストールしてから再度 SDK を 入れたところ、無事にインストールができました. こちらのサイトの情報がなかったら完全にアウトだったかもしれません. 助かりました. 有益な情報ありがとうございます！
![](/assets/openssl/06.png)



- - - -
いろいろと難所はありましたが、無事に OpenSSL を Windows に 入れることができました.
Windows 10 の Bash on Ubuntu on Windows なら、こんな苦労をしなくても良いのかなぁと思いつつも、まだまだ Windows 7 を 使うのでした...
