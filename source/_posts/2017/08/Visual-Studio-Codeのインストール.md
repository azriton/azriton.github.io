---
title: Visual Studio Code の インストール
date: 2017-08-18
updated: 2017-09-19
comments: true
categories: 開発環境
tags:
- Visual Studio Code
---

![](/images/vscode/visual-studio-code.png "Visual Studio Code")

最近 [Python に 手を出し始め](/2017/07/25/Python-3.6-64bitのインストール/)ました. 以前でしたら何でも Eclipse で～っと Plug-in を 入れるのですが、最近は [Atom](https://atom.io/) や [Visual Studio Code](https://code.visualstudio.com/) といった高機能エディターの流行が気になります. 今回は Visual Studio Code で Python を 始めてみようと思うので、さっそくセットアップをしたいと思います. (セットアップ話が続くなぁ...)

**作業環境**
- Windows 10 64bit
- Visual Studio Code


## Visual Studio Code とは
マイクロソフト社によって開発された高機能エディタです. Windows だけではなく、Linux や Mac OS X でも動作します.
インテリセンスという高性能なコード入力補完機能があり、加えて Git クライアント統合 や ターミナル統合(PowerShell、コマンドプロンプト、WLS Bash、Git Bash)、リファクタリング、デバッグなどが行え、ソースコード向けの機能がそろっています.
多くの機能が GUI による メニューがなく、コマンドパレット から使うようになっています. マウスを使用せずにコーディングを継続するように操作できるのだとか.

[Visual Studio Code](https://code.visualstudio.com/) の ウェブサイトの他に、マイクロソフト社のウェブサイトに日本語の製品紹介のサイト [Visual Studio Code - Visual Studio](https://www.microsoft.com/ja-jp/dev/products/code-vs.aspx) もあります. こちらから Visual Studio Code Preview ファースト ステップ ガイド が ダウンロードできます. 2015年7月17日版なので、少し古いですが概要を理解するにはよいかもしれません.


## インストール手順
Visual Studio Code の ウェブサイト [https://code.visualstudio.com/](https://code.visualstudio.com/) へ アクセスします.
[Download for Windows] の 右にある [∨] を クリックし、OS の 一覧が表示されるので、[Windows x32] の 下にある [64bit version] リンクをクリックして、[Windows x64] を 追加します.
[Windows x64] の 右にある [Installer] の [Stable] の [↓] リンクをクリックしてインストーラーをダウンロードします.
※ [Download for Windows] を 直接クリックすると 32bit 版がダウンロードされたので、プラットフォームを直接指定しました.
![](/images/vscode/install/01.png)

*2017年9月19日 追記*
[https://code.visualstudio.com/download](https://code.visualstudio.com/download) の 下に [See SHA-256 Hashes] が ありました.
~~チェックサムが見つからなかったので、そのままダウンロードされた [VSCodeSetup-x64-1.16.1.exe] を ダブルクリックして、インストーラーを起動します.~~
~~チェックサム、確認したいんだけどなぁ...~~

いつもの Windows PowerShell の `Get-FileHash` コマンドでチェックサムを確認します.
先のダウンロード・ページに記載されている Windows 64 bit の チェックサムは `62b6f28d0bd02c0193389fb1b716fed1a2e8d5aea7d38ed39c70e2c2d0b20797` でした.
```console
c:\> powershell Get-FileHash -Algorithm SHA256 c:\temp\VSCodeSetup-x64-1.16.1.exe

Algorithm  Hash
---------  ----
SHA256     62b6f28d0bd02c0193389fb1b716fed1a2e8d5aea7d38ed39c70e2c2d0b20797
```

ダウンロードされた [VSCodeSetup-x64-1.16.1.exe] を ダブルクリックして、インストーラーを起動します.
![](/images/vscode/install/02.png)

セットアップウィザードが表示されるので、[次へ] ボタンをクリックして進めます.
![](/images/vscode/install/03.png)

ライセンスの確認が表示されます. 内容を確認し同意できるようでしたら [同意する] を 選択して [次へ] ボタンをクリックします.
同意できなかったら終了なので、あきらめて同意できるエディタを探します.
![](/images/vscode/install/04.png)

インストール先を指定します.
本当は `C:\Develop` 以下にまとめたいところですが、インストーラー・モノなので、指定されたまま進めました.
![](/images/vscode/install/05.png)

プログラムグループを指定します. 特に何もせず進めます.
![](/images/vscode/install/06.png)

追加タスクを選択します. 以下を設定しました.
- エクスプローラー の ファイル コンテキスト メニュー に [Code で 開く] アクションを追加する
- エクスプローラー の ディレクトリ コンテキスト メニュー に [Code で 開く] アクションを追加する
- サポートされているファイルの種類のエディターとして Code を 登録する

デフォルトのエディタとして使わない場合は、[ファイル コンテキスト] と [サポートされいるファイルの種類] は チェックしない方が良いです. 特に関連付けを持っていかれると大変です.

一方で Visual Studio Code は ディレクトリ を プロジェクトのように扱うので、[ディレクトリ コンテキスト] は、どのような使い方にしてもチェックしておいた方が便利です.
![](/images/vscode/install/07.png)

インストールの設定確認が表示されるので、確認し、問題なければ [インストール] ボタンをクリックします.
![](/images/vscode/install/08.png)

Visual Studio Code が インストールされました. せっかくなので [Visual Studio Code を 実行する] に チェックしたまま [完了] します.
![](/images/vscode/install/09.png)

Visual Studio Code が 起動しました！
「ようこそ」画面が表示されています. 次回から表示しないようにするには [起動時にウェルカム ページを表示] の チェックを外します. また明示的に自分で表示するにはメニューの [ヘルプ] - [ようこそ] を 選択します.
![](/images/vscode/install/10.png)



- - - -
まずはインストールだけですが、使い心地がよさそうなのでデフォルトのエディタとして使ってみることにしました.
エディタとしては若干起動が遅いので、メモ用途にも使うデフォルトのエディタという観点からは他のエディタの方が良いかもしれないと思いつつも、まずは手足のようになるまで使い込んでみてから改めて考えてみます. (新しいものは、これまでの記憶、特にマッスル・メモリーが上書きされるまで使い込んでこそ、ようやく良し悪しについて考えられると思ったりも)
