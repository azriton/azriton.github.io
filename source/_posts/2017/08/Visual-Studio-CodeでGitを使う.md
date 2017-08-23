---
title: Visual Studio Code で Git を 使う
date: 2017-08-23
comments: true
categories: 開発環境
tags:
- Git
- GitHub
- Visual Studio Code
---

![](/images/vscode/visual-studio-code.png "Visual Studio Code")

[Git for Windows Portable](/2017/08/21/PortableGit-for-Windowsのインストール/) を インストールし Git が 使えるようになったので、さっそく Visual Studio Code から使ってみます.

**作業環境**
- Windows 10 64bit
- Git for Windows Portable 64bit
- Visual Studio Code
- Git Hub


## Visual Studio Code を 使って GitHub からの クローン
Visual Studio Code を 起動します.
※ GitHub の アカウント設定と公開鍵の登録などの設定ができている前提とします.
![](/images/vscode/git/01.png)

統合ターミナル を `Ctrl + @` で 表示します.
![](/images/vscode/git/02.png)

統合ターミナルで以下を実行しクローンします.
※ 下記 `azriton/test` は クローンするリポジトリに置き換えます
```console
PS C:\Users\username> cd C:\Temp\
PS C:\Temp> git clone git@github.com:azriton/test.git
Cloning into 'test'...
The authenticity of host 'github.com (192.30.255.113)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.113' (RSA) to the list of known hosts.
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
PS C:\Temp>
```
![](/images/vscode/git/03.png)

Git の 設定で `user.useConfigOnly true` している場合は、ここでユーザ名とメールアドレスを設定します.
```console
PS C:\Temp> cd test
PS C:\Temp\test> git config --local user.email "you@example.com"
PS C:\Temp\test> git config --local user.name "Your Name"
```
![](/images/vscode/git/04.png)

Visual Studio Code で プロジェクトのように扱うため、 `code` コマンドにクローンしたリポジトリのディレクトリを渡してフォルダを開きます. (前回リポジトリのディレクトリに入っているので、ここでは `.` カレント・ディレクトリを渡しています)
(GUI から開く場合は `Ctrl + K` `Ctrl + O`)
```console
PS C:\Temp\test> code .
```
![](/images/vscode/git/05.png)

クローンしたディレクトリが開いた状態の新しいウィンドウが表示されます.
![](/images/vscode/git/06.png)


## Visual Studio Code から 変更 を プッシュ
`README.md` を ダブルクリックしてエディタに表示しします.
![](/images/vscode/git/07.png)

改行コードがあっていないので `Ctrl + Shift + P` で コマンドパレットを表示し `line s` と 入力すると絞り込みされるので、[改行コードの変更] を 選択し、続い改行コードの選択が表示されるので [LF] を 選択します.
![](/images/vscode/git/08.png)
![](/images/vscode/git/09.png)

ステータス・バー の 改行コード が `LF` に なっていることを確認し `README.md` を 編集
、保存すると ソース管理アイコンに変更されたファイル数がつきます.
![](/images/vscode/git/10.png)

ソース管理画面 を `Ctrl + Shift + G` で 表示します.
![](/images/vscode/git/11.png)

すべての変更をステージしたいので、 `Ctrl + Shift + P` で コマンドパレットを表示、 `stage` と 入力すると絞り込みされるので、[Git: すべての変更のステージング] を 選択します.
※ 個別に指定する場合は、各ファイルにマウス・フォーカスを与えると [＋] アイコンが出るのでクリックして追加します.
![](/images/vscode/git/12.png)
![](/images/vscode/git/13.png)

[Message (press Ctrl+Enter to commit)] の テキスト・ボックスにフォーカスが外れてしまったので、再度 `Ctrl + Shift + G` で フォーカスを与え、コミットメッセージを入力し `Ctrl + Enter` します. これで変更がコミットされます.
![](/images/vscode/git/14.png)
![](/images/vscode/git/15.png)

ステータス・バー に 発信 [0↓ 1↑] が 出ているのでコミット済み、プッシュ前の変更があることが分かります.
`Ctrl + Shift + P` で コマンドパレットを表示、 `push` と 入力すると絞り込みされるので、[Git: プッシュ] を 選択します.
![](/images/vscode/git/16.png)

ステータス・バー が [0↓ 0↑] なので 無事、変更をプッシュすることができました！ (ちょっと、わかりにくいかもｗ)
![](/images/vscode/git/17.png)



- - - -
統合コンソールがあるので、もしかしたら Git コマンドを直接使った方が便利かもしれないですね.
とはいえ、Git の 準備もできました. いよいよ Visual Studio Code で コーディングが始められます. たのみー！
