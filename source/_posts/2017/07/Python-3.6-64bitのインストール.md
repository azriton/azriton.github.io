---
title: Python 3.6 64bit の インストール
date: 2017-07-25
comments: true
categories: 開発環境
tags:
- Python
---

![](/assets/python/python3.png "Python 3")

Python を 使うことになり、初めてなので インストール・メモ. ちょっとしたメモでも仲間が増えたときに、こんな感じで～って説明できるので残しておくことにしたことはないですね.

**作業環境**
- Windows 10 64bit
- Python 3.6 64bit


# バージョン と インストールする環境について検討
今回は Windows 10 に Python を インストールします. Windows 10 だと、Bash on Ubuntu on Windows で Linux 環境に Python を 入れられるはずですが、いきなり複雑なことをするとトラブル時に対応できなくなりそうなので、まずは Windows 10 に 直接インストールし、Python に 慣れてきたら Bash on Ubuntu on Windows を 使ったりとアレンジしたいと思います.

Pyhton は 2.x と 3.x があります. 今回はまったく新しく使い始めるため、ありがたいことに過去のしがらみなし、ということで 2017年7月現在 最新 の 3.6.2 を 使いたいと思います. また、開発・実行環境の Windows 10 が 64bit なので、Python も 64bit を 選択したいと思います. Pyhton の ライブラリ によっては 32bit しか用意がないなど、64bit を 使うと困ることもあるみたいですが、これは切り替えが簡単なので、困るまでは 64bit で 行ってみます.


## インストール手順
Python の トップページ や Downloads の メニュー、Downloads ページ にある [Download Python 3.6.2] は、OS を 自動判定して適切なインストーラーをダウンロードさせてくれるのですが、こちらからだと 32bit 版 が ダウンロードされます. そのため こちらからはダウンロードはせず、Windows 版のダウンロード・ページから 64bit 版 を 選択してダウンロードします.
![](/assets/python/install-3.6/01.png)

Windwos 版 の ダウンロード・ページ [Python Releases for Windows | Python.org](https://www.python.org/downloads/windows/) [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/) へ アクセスします.
こちらから `Windows x86-64 executable installer` を ダウンロードします.
![](/assets/python/install-3.6/02.png)

チェックサムの確認は [Python 3.6.2 - 2017-07-17](https://www.python.org/downloads/release/python-362/) リンクから [Python Release Python 3.6.2 | Python.org](https://www.python.org/downloads/release/python-362/) 画面へ行き、画面中段の Files に MD5 Sum が 記載されています. 今回ダウンロードした `Windows x86-64 executable installer` は `4377e7d4e6877c248446f7cd6a1430cf` です.
確認方法は Windows PowerShell 4.0 以降の `Get-FileHash` コマンドを使います.
```console
c:\> powershell Get-FileHash -Algorithm MD5 c:\python-3.6.2-amd64.exe

Algorithm  Hash
---------  ----
MD5        4377E7D4E6877C248446F7CD6A1430CF
```

ダウンロードした `python-3.6.2-amd64.exe` を ダブルクリックして、インストーラーを起動します.
![](/assets/python/install-3.6/03.png)

`PATH` は 追加しておいた方が便利なので、[Add Python 3.6 to PATH] に チェックをつけます.
インストール・オプションを指定したいので [Customize installation] を クリックしてインストールを進めます.
![](/assets/python/install-3.6/04.png)

Optional Features では、特に変更せず [Next] ボタンをクリックして進みます.
![](/assets/python/install-3.6/05.png)

Advanced Options では、以下を設定し、[Install] ボタンをクリックします.
- [Install for all users] を チェック
- [Precompile standard library] を チェック (Install for all users チェックすると自動で付く)
- [Customize install location] を 必要に応じて設定 (私は C:\Develop 以下にまとめるのが好きなので設定)
![](/assets/python/install-3.6/06.png)


インストールが完了しました.
[Disable path length limit] を すると、[Windows の パスの長さ(ドライブ・レター、フォルダ、ファイル名) が 260文字に制限されている](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx#maxpath) のを解除できます. Windows 10 で、この MAX_PATH が 解除できるようになったため Python 3.6 で 追加された機能([Issue 27731: Opt-out of MAX_PATH on Windows 10 - Python tracker](https://bugs.python.org/issue27731)) になります.
パスが長くなるようでしたら設定します. 今回は長くなる想定が無いので選択しませんでした.
![](/assets/python/install-3.6/07.png)


## 動作確認
コマンド プロンプト で バージョンを出力してみます. コマンドは `python --version` もしくは `python -V` です. 大文字で `-V` であることに注意です. インストール・オプションで [Add Python 3.6 to PATH] に チェックしたので、パスが通っているため、直接 `python` コマンドが実行できます.
```console
c:\> echo %PATH%
C:\Develop\sdk\Python36\;C:\Develop\sdk\Python36\Scripts\; ...(省略)

c:\> python --version
Python 3.6.2

c:\> python -V
Python 3.6.2
```

小文字の場合は 詳細報告 (verbose) モード の オプション指定になり、インタプリタに入ります. 突然大量の出力があり、その後は プロンプトが `c:\` のような Windows から `>>>` に 変わり、Python 命令を受け付ける状態になります.
`1 + 1` のような命令を入力すると、実行結果が返ってきます.
一方で命令にないことをすると、エラーが返ります.
終了するには `Ctrl + Z` キーを押す、もしくは `exit()` を 実行します. `exit` で 実行すると `Use exit() or Ctrl-Z plus Return to exit` と返ってきます.
なお、`Ctrl + C` は `KeyboardInterrupt` です.
突然の出力とインタプリタの起動で思わぬ動きをしてビックリしますが、落ち着いて `exit()` しましょう.
```console
c:\> python -v
import _frozen_importlib # frozen
import _imp # builtin
import sys # builtin
... (省略)
>>>
>>> 1 + 1
2
>>>
>>> python -V
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'python' is not defined
>>>
>>> (※ Ctrl+C)
KeyboardInterrupt
>>>
>>> exit()
```


<a href="//af.moshimo.com/af/c/click?a_id=871746&p_id=1296&pc_id=2120&pl_id=19703&guid=ON" target="_blank" rel="nofollow"><img src="//image.moshimo.com/af-img/0453/000000019703.jpg" width="300" height="250" style="border:none;"></a><img src="//i.moshimo.com/af/i/impression?a_id=871746&p_id=1296&pc_id=2120&pl_id=19703" width="1" height="1" style="border:none;">



- - - -
主にクロール系の処理になるので、こちらで勉強しています.
最初の言語として勉強するための本には向いていませんが、Python の 言語使用の話もあるので他の言語を知ってる場合には、これ１冊でも十分ではないでしょうか.
またクローラーを作るという観点では、Python の 基本機能だけではなく Shell や Python の 外部ライブラリを使ったりと、バリエーションを持たせて解説があるので とても分かりやすいほか、法律に関する話などもあるのが素晴らしいです！
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51MK3rXBRRL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P" target="_blank" >Pythonクローリング＆スクレイピング ―データ収集・解析のための実践開発ガイド―[Kindle版]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">加藤 耕太 技術評論社 2016-12-16    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774183679%2F" target="_blank" >Amazon[書籍版]で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div>                                                  </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
インストーラーのダウンロードで 32bit が 自動選択されるという現象がありましたが、それ以外は特に難しいことなくインストールができますね. 後はバージョン情報を出力させる際に、つい `python -v` と 小文字を使う癖があるようで、いきなりの詳細報告モードのインタプリタで毎回焦ります (;^_^A
何はともあれインストールができたので、バリバリ開発にいそしみ、面白いものができたらアップしていけたらと思います.
