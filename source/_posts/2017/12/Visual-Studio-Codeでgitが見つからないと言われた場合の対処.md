---
title: Visual Studio Code で git.exe が 見つからないと言われた場合の対処
date: 2017-12-18
comments: true
categories: 開発環境
tags:
- Git
- Visual Studio Code
---

![](/assets/vscode/visual-studio-code.png "Visual Studio Code")

[Git for Windows Portable を インストールし](/2017/08/21/PortableGit-for-Windowsのインストール/) Git が 使えるようになったのですが、最近 Visual Studio Code を アップデートしたら `git.exe` が 見つからないという警告が表示されるようになりました. 通常は出ないのでしょうが、 [PortableGit を 使っている](/2017/08/21/PortableGit-for-Windowsのインストール/) ので、そのあたりから警告が出たのでしょうか. 何にしろ対処したいと思います.

**作業環境**
- Windows 10 64bit
- Git for Windows Portable 64bit
- Visual Studio Code


## 警告が発生している状況
まずは状況確認です.
Visual Studio Code, November 2017 (version 1.19) を 起動すると下図のような警告が表示されるようになりました. リリースノートには特に変更についての記載はないようなので不思議です...
![](/assets/vscode/git/21.png)

`git.exe` への パスを確認しますが、通っているようです.
![](/assets/vscode/git/22.png)


## git.exe へのパスを設定
考えても仕方がないので、警告の文章でサジェストされている通り `git.path` の 設定をします.
ウィンドウ左下 の ⚙ 歯車 の アイコンをクリックし、[設定] を 選択します.
![](/assets/vscode/git/23.png)

画面上部の検索ボックスへ `git.path` を 入力し、左側に `git.path` の 設定項目が出てきたら 設定項目の左 ✎ 鉛筆 アイコンをクリックして、右側のユーザー設定に追加します.
追加された設定項目値を `git.exe` までのパスに設定します. ディレクトリ区切りの `\` 円マーク(バックスラッシュ) は `\\` と ２回書いてエスケープするようにします.
今回は [PortableGit for Windows 64bit の インストール](/2017/08/21/PortableGit-for-Windowsのインストール/) で 設定したように `C:\\Develop\\tool\\PortableGit\\cmd\\git.exe` としました.
![](/assets/vscode/git/24.png)

Visual Studio Code を 再起動すると、無事に警告が表示されなくなりました.
![](/assets/vscode/git/25.png)



- - - -
いろいろ探してみたのですが、結局何だったのかよくわかりませんでした. `git.path` の 設定項目自体も以前からある設定のようで、2017.12 とか関係なさそうですし. 何だったんだろう...
普段から git コマンドで操作しているので、あまり Visual Studio Code の Git 統合は使っていないから [今後は表示しない] でもよかったのかなと主思いますが Git 統合の機能がさらに良くなったら使うかもしれないので、パス設定にしました.
