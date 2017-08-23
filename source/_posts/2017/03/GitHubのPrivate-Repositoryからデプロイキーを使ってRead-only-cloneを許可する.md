---
title: GitHub の Private Repository から デプロイキーを使って Read-only clone を 許可する
date: 2017-03-13
categories: 開発環境
tags:
- Git
- GitHub
---

![](/images/github/github.png "GitHub")

GitHub の Private Repository に 置いてあるソース を clone させて使いたいケースがあります. そんな場合はデプロイキーを使うことで Read-only な clone を 許せます.

**作業環境**
- Windows 7
- GitHub


## 背景
今回は Slack の ボット開発で使っているリポジトリから、稼働環境 で clone して実行したいというものになります. Slack の ボット開発では Node.js を 使っています. Node.js は JavaScript なのでリポジトリから clone して直接実行することができ、とても手軽です.
Public Repository でしたら直接ダウンロードできるので特に問題は無いのですが、Private Repository の場合はアクセスするための権限が必要となります.
通常、開発者は自分のアカウントがあり自前の SSH Key を 登録しています. とはいえ稼働環境で、この開発者のキーを使うわけにはいきません. 開発者がアクセスできる全ての権限を稼働環境に置いてしまうことになります.
そこでリポジトリごとに Read-only で clone できる方法が必要となります.

※ 多くの場合は CI と 組み合わせて、自動ビルド・テストからのデプロイを実現しているかと思いますが、今回は簡易ボットの運用なので CI 無しの、clone - 直実行 で 行っているものになります.


## SSH Key の 生成
`ssh-keygen` は Microsoft が 開発している [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases) を 使いました. 生成する鍵の種類と強度は [Raspbian Jessie Lite の SSH/公開鍵認証 設定](/2016/11/29/Raspbian-Jessie-LiteのSSH公開鍵認証設定/) での調査に則り `ed25519` に したいと思います.
```shell-session
c:\> ssh-keygen -t ed25519 -C ""
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\[username]/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\[username]/.ssh/id_ed25519.
Your public key has been saved in C:\Users\[username]/.ssh/id_ed25519.pub.
The key fingerprint is:
SHA256:T69X1O58qiKv7Sp/aPJTI+PpzUdOZDO+EcD5ldc8ON8
The key's randomart image is:
+--[ED25519 256]--+
|         . .  .o.|
|          +  oo.+|
|           o .ooo|
|            B ..E|
|        S .+ = . |
|        ooo.= . .|
|       . *.=.+ o |
|      o B+o.=   +|
|       B*OB+...XX|
+----[SHA256]-----+
```


## GitHub の リポジトリ に Deploy Key を 追加
Deploy Key を 登録するリポジトリの設定 `https://github.com/[username]/[repository]/settings/keys` に アクセスします. ウェブからは [Settings] - [Deploy keys] を たどります.
画面から [Add deploy key] ボタンをクリックします.
![](/images/github/deploy/01.png)

Deploy Key 追加画面が表示されるので、[Title] に この鍵のタイトルを付けます. [Key] に `id_ed25519.pub` の 内容(※ `id_ed25519`**(.pub なし) ではない**ことに注意)を貼り付けます.
今回は Read-only の Deploy Key にするため、[Allow write access] は チェックせず、[Add key] を クリックします.
![](/images/github/deploy/02.png)

GitHub に Deploy Key が 追加されました. `Read-only` が ついているところがポイントになります.
![](/images/github/deploy/03.png)


## Deploy Key の 利用
```shell-session
user@host:/tmp$ mkdir ~/.ssh
user@host:/tmp$ chmod 700 ~/.ssh
user@host:/tmp$ mv /mnt/id_ed25519 ~/.ssh
user@host:/tmp$ chmod 600 ~/.ssh/id_ed25519

user@host:/tmp$ echo -e "Host github.com\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config

user@host:/tmp$ git clone git@github.com:[username]/[repository].git
Cloning into '[repository]'...
Warning: Permanently added 'github.com,192.30.253.112' (RSA) to the list of known hosts.
remote: Counting objects: 216, done.
remote: Compressing objects: 100% (95/95), done.
remote: Total 216 (delta 56), reused 0 (delta 0), pack-reused 121
Receiving objects: 100% (216/216), 54.22 KiB | 0 bytes/s, done.
Resolving deltas: 100% (123/123), done.
Checking connectivity... done.

user@host:/tmp$ ls -l
total 8
drwxr-xr-x.  6 user user 4096 Mar 13 04:53 [repository]
```

まずは生成した Deploy Key の Private Key `id_ed25519` を 使えるようにします. 今回は `/mnt/id_ed25519` に 配置しておいたとして、ホームディレクトリに `.ssh` ディレクトリを作成して、Private Key を 移動します. パーミッションが正しく設定できていないと鍵が読み込めない場合があるので `chmod` を 忘れないようにします.

続いて、`~/.ssh/config` に github.com への接続はホストキーの確認をしない設定します. これを行わない場合 `~/.ssh/known_hosts` に github.com の ホストキーが登録されていない場合、以下のようにホストキーの確認を求められ入力待ちとなります. Shell Script などで 自動化する場合に都合が悪いのでホストキーの確認をしないようにしています.
```shell-session
user@host:/tmp$ git clone git@github.com:[username]/[repository].git
Cloning into '[repository]'...
The authenticity of host 'github.com (192.30.253.113)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```

後は `git clone git@github.com:[username]/[repository].git` で clone できるので、Node.js の 実行をするなり自由にできます.


## ホントに Read-only なの？
```shell-session
user@host:/tmp/[repository]$  git push origin master
ERROR: The key you are authenticating with has been marked as read only.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

リポジトリに Push したところ `marked as read only` と言ってエラーが返るので Read-only に なっているようです.


- - - -
これで Private Repository でも 稼働環境で `git clone` で ソースを取得して実行できるようになりました. スクリプト言語などで CI を 組み合わせずに簡易的に動作させる場合は、Deploy Key を 使うことで、ユーザ追加などをせずにアクセス権を絞って clone できるので便利ですね.
本当は CI を 使って、ちゃんと自動テストをしてからデプロイしたほうが良いのですが、お遊びのボットで多少壊れてても許されるような場合には使っていきたいところです.
