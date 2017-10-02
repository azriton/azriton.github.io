---
title: PortableGit for Windows 64bit の インストール
date: 2017-08-21
updated: 2017-09-10
comments: true
categories: 開発環境
tags:
- Git
- GitHub
- Visual Studio Code
---

![](/images/vscode/visual-studio-code.png "Visual Studio Code")

[Visual Studio Code の 開発環境を整理](/2017/08/18/Visual-Studio-Codeのインストール/)し始めました. Visual Studio Code は Git を サポートしていますが別途インストールする必要があります. 今回は Git for Windows Portable を 導入します.

**作業環境**
- Windows 10 64bit
- PortableGit 64bit
- Git Hub


## Git for Windows Portable ("thumbdrive edition") とは
[Git](https://git-scm.com/) 公式パッケージの１つで、ポータブル・アプリケーションと呼ばれる特定の環境にインストールする必要がなく、USB メモリーなどで持ち運び、任意の環境(OS は Windows版なら、Windows の 必要はありますが)で利用できるパッケージ の Git に なります.
今回は PC に インストールするので、Git の 実行環境だけを持ち出すことは無いのですが、git コマンドぐらいしか使わないので、いろいろインストールせずに済ませたいことからポータブル版にしました.


## インストール手順
git-for-windows の GitHub 最新のリリース [https://github.com/git-for-windows/git/releases/latest](https://github.com/git-for-windows/git/releases/latest) へ アクセスします. Git の ウェブサイト [https://git-scm.com/](https://git-scm.com/) の ダウンロード・ページへ行ってもよいのですが、自動ダウンロードが動作することと、チェックサムが無いので、ダウンロード・ページが見ている GitHub の リリース・ページからダウンロードしました.
![](/images/vscode/install-git/01.png)

例によって Windows PowerShell の `Get-FileHash` コマンドでチェックサムを確認します.
先のリリースページに記載されている PortableGit-2.14.0.2-64-bit.7z.exe の チェックサムは `5236c21de3cdf52b538322de0b0444f6cd49a5bae6006ea89f0683598cbda7ac` でした.
```console
c:\> powershell Get-FileHash -Algorithm SHA256 c:\temp\PortableGit-2.14.0.2-64-bit.7z.exe

Algorithm  Hash
---------  ----
SHA256     5236C21DE3CDF52B538322DE0B0444F6CD49A5BAE6006EA89F0683598CBDA7AC
```

ダウンロードした [PortableGit-2.14.0.2-64-bit.7z.exe] を ダブルクリックして、7-Zip の 自己解凍ウィザードを起動します.
![](/images/vscode/install-git/02.png)

解凍先を聞かれるので任意のフォルダを指定します. 今回は `C:\Develop\tool\PortableGit` に しました.
![](/images/vscode/install-git/03.png)

環境変数 PATH を 設定します.  システムのプロパティから追加してもよいですし、以下のコマンドからも実行可能です.
```console
powershell [Environment]::SetEnvironmentVariable('PATH', [Environment]::GetEnvironmentVariable('PATH', 'User') + ';' + 'C:\Develop\tool\PortableGit\cmd', 'User')
```


## .gitconfig の 設定
とりあえず最小限の `.gitconfig` の 設定
- `core.autocrlf` は、改行コードをエディタで明示するので変換なしにしました
- `core.excludesfile` は、全体の `.gitignore` ファイルの指定で、先に作成したファイルを指定します
- `user.useConfigOnly` は、リポジトリごとに `user.name` や `user.email` を 指定する設定です
チェックアウトした後に設定するのが面倒ではありますがアカウントが別なリポジトリがあったりすると便利です
- `credential.helper` は、HTTPS で クローンする際にクレデンシャル情報を記憶してくれます ([Caching your GitHub password in Git - User Documentation](https://help.github.com/articles/caching-your-github-password-in-git/))
```console
c:\> git config --global core.autocrlf false
c:\> git config --global core.excludesfile ~/.gitignore
c:\> git config --global user.useConfigOnly true
c:\> git config --global credential.helper wincred
```


## 全体の .gitignore 設定
`.gitignore` は 各リポジトリに配置するのとは別に、全体の設定ができます. 先の `core.excludesfile` で 設定したファイルが それにあたり、今回は `~/.gitignore` ホームディレクトリにあるファイルに設定しています.

まずはファイルを作成します. Windows には `touch` コマンドが無いのですが、 `type nul > ファイル名` で 空のファイルが作れます. つい `null` と 指が動きたくなりますが `l` は １つになります. `nul` は Linux などで言うところの `/dev/null` でしょうかね. [NUL デバイスにリダイレクトされたシェルは、MS-DOS のメッセージを表示しません。 - Microsoft サポート](https://support.microsoft.com/ja-jp/help/40592/shell-redirected-to-nul-device-suppresses-ms-dos-message).

また、エクスプローラーから作成する場合は 自分のホーム・フォルダーを表示し [右クリック] - [新規作成] - [テキスト ドキュメント] から、ファイル名を `.gitignore.` と 最後に `.` を 付けて作成します. 拡張子に関する警告が出ますが [はい] を クリックすると、最後の `.` が 消えて `.gitignore` に なります.

余談ですが [日付とファイルのタイムスタンプを更新 - Microsoft サポート](https://support.microsoft.com/ja-jp/help/69581/updating-the-date-and-time-stamps-on-files) は `COPY /B ファイル名 +,,` らしいです.
```console
type nul > %USERPROFILE%\.gitignore
```

作成したファイルに Git で 管理しないファイルの設定をします.
リポジトリにコミットしないので自分の環境ならではの指定もできるのでうれしいです. Windows なので、とりあえず Thumbs.db を. ([このブログは GitHub Pages で 作っている](/2016/11/01/HexoとGitHub-Pagesでブログ環境の構築/) ので、画像がソースツリーにあるから必要)
その他、ちょっとしたスニペットを入れておく自分用フォルダなどを追加してもよさそうです.
```.gitignore
Thumbs.db
```

** 2017年9月10日 追記 **
もっと良い設定方法があったので、全体 .gitignore の 設定 を 見直しました.
詳しくは、こちら [Git の 全体 gitignore を 再設定する](/2017/09/13/Gitの全体gitignoreを再設定する/) を ご参照ください.


## Proxy が 必要な環境での設定
たまに必要になってハマることがあるのでメモ.
※ 以下の例の `[hostname]:[port]` は 利用する HTTP Proxy の ホスト名 と ポート番号に置き換えてください.


### `https://` で クローンする場合
```console
c:\Temp> git config --global http.proxy http://[hostname]:[port]
c:\Temp> git clone https://github.com/azriton/test.git
```


### `git://` で クローンする場合
ホーム・ディレクトリの `.ssh` ディレクトリに以下の内容で `config` ファイルを作成します.
```
Host github.com
  HostName ssh.github.com
  Port 443
  ProxyCommand connect.exe -H [hostname]:[port] %h %p
```

後はいつも通りクローンします.
```console
c:\Temp> git clone git@github.com:azriton/test.git
```


### Eclipse と 混在して利用する場合
片方は HTTPS を 使うのが無難な気がしますが、両方とも `git://` を 使うには細工が必要です. Eclipse が `config` を 参照して うまく動かなくなるので、 `git` コマンドで利用する側の設定を変更します.
`Host` の 文字列を任意の文字列に変更します. 今回は `proxy.github.com` としました.
```
Host proxy.github.com
  HostName ssh.github.com
  Port 443
  ProxyCommand connect.exe -H [hostname]:[port] %h %p
```

クローンする際に、`git@github.com:azriton/test.git:` の `github.com` を `Host` に 設定した文字列に置き換えます. 今回の例では `proxy.github.com` に なります.
```console
c:\Temp> git clone git@proxy.github.com:azriton/test.git
```

クローンする際に GitHub からコピーした文字列を変更する必要があり、ひと手間かかりますがクローン後は特に意識することは無いです.



- - - -
Git の 準備ができました. Visual Studio Code で Git を 使って開発の準備に取り掛かりたいと思います.
