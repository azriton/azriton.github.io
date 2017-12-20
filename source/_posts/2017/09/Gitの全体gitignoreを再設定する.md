---
title: Git の 全体 gitignore を 再設定する
date: 2017-09-13
comments: true
categories: 開発環境
tags:
- Git
- GitHub
- Visual Studio Code
- Eclipse
- Windows
---

![](/assets/vscode/visual-studio-code.png "Visual Studio Code")

Visual Studio Code で 使うために [PortableGit を インストール](/2017/08/21/PortableGit-for-Windowsのインストール/)しました. その際に 全体 gitignore を 設定したのですが、よくよく調べると、もっとよい設定方法がったので再設定します.

**作業環境**
- Windows 10 64bit
- PortableGit 64bit
- Git Hub


## github/gitignore リポジトリ
GitHub 社 が 公開している github/gitignore というリポジトリ [https://github.com/github/gitignore](https://github.com/github/gitignore) というのがあります.
様々なプログラム言語やフレームワークなどに応じた gitignore の テンプレートを提供してくれています. GitHub.com で リポジトリを作成する際や、作成した後から Web UI で gitignore を 追加する機能がありますが、その gitignore ファイルは このリポジトリから作られているとのことです.

アプリケーションを作る際などに、いつも参照させていただくのですが、よくよく見ていると [Global](https://github.com/github/gitignore/tree/master/Global) というディレクトリがあり、こちらは OS や エディタなどのテンプレートが入っているとのことです. 知らなかった...

ちゃんと説明 [Globally Useful gitignores - gitignore/Global at master · github/gitignore](https://github.com/github/gitignore/tree/master/Global#globally-useful-gitignores) を 読まないとですね. ということで、こちらをもとに グローバル の gitignore を **再設定** します.

まず [Windows.gitignore](https://github.com/github/gitignore/blob/master/Global/Windows.gitignore) を 参照してみます. 2017年9月現在、以下の内容でした. [前回設定](/2017/08/21/PortableGit-for-Windowsのインストール/#全体の-gitignore-設定)した `Thumbs.db` だけとは大違いですね. 勉強になります.

```gitignore
# Windows thumbnail cache files
Thumbs.db
ehthumbs.db
ehthumbs_vista.db

# Dump file
*.stackdump

# Folder config file
Desktop.ini

# Recycle Bin used on file shares
$RECYCLE.BIN/

# Windows Installer files
*.cab
*.msi
*.msm
*.msp

# Windows shortcuts
*.lnk
```

Visual Studio Code の 設定もありました. `.vscode/` 以下の Visual Studio Code 設定ファイル以外を ignore ですが、これでいいのかな？ちょっと意図を知りたいかも.

```gitignore
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
```

Eclipse の 設定もあります. Eclipse で 関しているわけではないのですが、Eclipse エディタで編集したい部分があるなどで、Eclipse プロジェクトにしている場合があります. その場合は `.project` や `.settings` などの Eclipse 関連ファイルはコミットしたくないので、グローバル gitignore するのもよさそうです.

```gitignore
.metadata
bin/
tmp/
*.tmp
*.bak
*.swp
*~.nib
local.properties
.settings/
.loadpath
.recommenders

# External tool builders
.externalToolBuilders/

# Locally stored "Eclipse launch configurations"
*.launch

# PyDev specific (Python IDE for Eclipse)
*.pydevproject

# CDT-specific (C/C++ Development Tooling)
.cproject

# Java annotation processor (APT)
.factorypath

# PDT-specific (PHP Development Tools)
.buildpath

# sbteclipse plugin
.target

# Tern plugin
.tern-project

# TeXlipse plugin
.texlipse

# STS (Spring Tool Suite)
.springBeans

# Code Recommenders
.recommenders/

# Scala IDE specific (Scala & Java development for Eclipse)
.cache-main
.scala_dependencies
.worksheet
```


## curl コマンド で グローバル gitignore を 作る
利用する gitignore を 決めたら、グローバル gitignore を 作ります.
Powershell 3.0 から `curl` こと `Invoke-WebRequest` が 使えます. これでダウンロードすれば OK！ (ただし使い方は Linux のとは異なるので注意)

対象が ひとつ の ファイルの場合は、コマンドプロンプトから以下のように実行します. ここでは `Windows.gitignore` を `~/.gitignore` へ ダウンロードしました.

```console
c:\Temp> powershell curl -Uri https://raw.githubusercontent.com/github/gitignore/master/Global/Windows.gitignore -OutFile ~/.gitignore
```

複数のファイルを結合する場合は、PowerShell を 起動してからコマンドを実行します. ここでは `Windows.gitignore` と `Eclipse.gitignore` と `VisualStudioCode.gitignore` を 結合しました.

```console
c:\Temp> powershell
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Temp>
PS C:\Temp> curl -Uri https://raw.githubusercontent.com/github/gitignore/master/Global/Windows.gitignore -UseBasicParsing | Select-Object -ExpandProperty Content >> ~/.gitignore
PS C:\Temp> curl -Uri https://raw.githubusercontent.com/github/gitignore/master/Global/Eclipse.gitignore -UseBasicParsing | Select-Object -ExpandProperty Content >> ~/.gitignore
PS C:\Temp> curl -Uri https://raw.githubusercontent.com/github/gitignore/master/Global/VisualStudioCode.gitignore -UseBasicParsing | Select-Object -ExpandProperty Content >> ~/.gitignore
```


## gitignore.io で 自動生成
複数ファイルを結合して使う場合に、コマンドを複数発行すればよいのですが ちょっと面倒だなぁという時は [gitignore.io](https://www.gitignore.io/) というサービスが自動で作ってくれます. なんと [名だたる企業が使っている](https://github.com/joeblau/gitignore.io#companies) ようです(が、企業ロゴが１枚画像で企業へのリンク無しなんだよなぁ...)

Web から使う場合は、 [https://www.gitignore.io/](https://www.gitignore.io/) へ アクセスします.
![](/assets/vscode/install-git/04.png)

画面中央のテキストボックスに gitignore したいテンプレート名前を入れていき、[Create] ボタンをクリックします.
![](/assets/vscode/install-git/05.png)

自動生成された gitignore の 内容が出力されるので、コピー＆ペーストします.
![](/assets/vscode/install-git/06.png)


また、自動生成された gitginore 画面 の URL を 使うことでコマンドラインからも取得できます. キーワードをカンマでつなぐのですが、URL Encode するので `%2C` で つなぎます.

```console
c:\Temp> powershell curl -Uri https://www.gitignore.io/api/windows%2Ceclipse%2Cvisualstudiocode -OutFile ~/.gitignore
```

ヘルプや、指定できるキーワードのリストは PowerShell から 以下のように実行します.

```console
c:\Temp> powershell

PS C:\Temp> curl -Uri https://www.gitignore.io/api/ -UseBasicParsing | Select-Object -ExpandProperty Content
gitignore.io help:
  list    - lists the operating systems, programming languages and IDE input types
  :types: - creates .gitignore files for types of operating systems, programming languages or IDEs

PS C:\Temp> curl -Uri https://www.gitignore.io/api/list -UseBasicParsing | Select-Object -ExpandProperty Content
1c-bitrix,a-frame,actionscript,ada,adobe
advancedinstaller,agda,alteraquartusii,altium,android ...(省略)
```


さらに [Command Line Docs - gitignore.io](https://www.gitignore.io/docs) には、公式のコマンドライン実行法について説明があります. スクリプト化しておくと便利なのかもしれませんが、 `curl` だけでも大変ありがたいです.

gitignore.io-san, thank you for the wonderful service!!



- - - -
github/gitignore リポジトリの Global ディレクトリ発見から、便利なサービスにまでたどり着くことができ、開発環境を作る際の幅広がりました. 自分が知っていることをすぐに使えることも必要ですが、他にも良いやり方は無いのかと疑問を持って調べる習慣も大事ですね. Global ディレクトリに気づかなかったのはホント不覚である...

今回は グローバル gitignore の 自動生成でしたが、アプリケーション用の gitignore も 同じことができるので、これからは自動生成でやった方が良いですね.

実際に使うにあたっては、グローバル gitignore は 自動生成後に編集.
アプリケーション用にはプロジェクトなり組織共通の gitignore を 管理するリポジトリを作って、先にそのファイルをダウンロードして、gitignore.io 自動生成を追記させるような感じでしょうか.

言語やフレームワークの gitignore を 自動生成させるとすると、独自追加部分だけが残り、だいたい どのリポジトリも同じものを追加すると思われるので、Git で 管理・共用化して、自動生成だけにするのがよいのかもと思ったりも... 今度やってみよう.
