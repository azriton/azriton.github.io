---
title: Raspbian Jessie Lite の SSH/公開鍵認証 設定
date: 2016-11-29
updated: 2017-03-13
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspbian Jessie Lite に SSH 公開鍵認証 の 設定を行います.

**作業環境**
- Windows 7
- Raspbian Jessie Lite


## 使用するアルゴリズムの選択
なるべく強度が高く安全なものを使いたいので、今回は Ed25519、[エドワーズ曲線デジタル署名アルゴリズム](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%89%E3%83%AF%E3%83%BC%E3%82%BA%E6%9B%B2%E7%B7%9A%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E7%BD%B2%E5%90%8D%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0) を 使うことにします.

アルゴリズムや強度については、こちらの記事を参考にさせて頂きました. RSA で 鍵長を増やすことばかり考えていましたが、勉強になりました. ありがとうございます.
- [GitHubユーザーのSSH鍵6万個を調べてみた - hnwの日記](http://d.hatena.ne.jp/hnw/20140705)
- [GitHubでEd25519鍵をつかう - lnrvrs](http://jnst.hateblo.jp/entry/2014/12/15/200542)
- [お前らのSSH Keysの作り方は間違っている - Qiita](http://qiita.com/suthio/items/2760e4cff0e185fe2db9)


## キー・ペア の 生成
`ssh-keygen` を 使い、キー・ペアを生成します. `ed25519` は 鍵長 が 256 bit 固定とのことなので、`-t ed25519` のみの指定となります.
ファイルの場所は特に問題ないのでデフォルトのままにしました. パスフレーズは設定したほうが安全です. 秘密鍵のファイルをとられてしまうと、守るものがなくなり素通しとなってしまいます.
`ssh-keygen` は Microsoft が 開発している [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases) を 使いました. (2017年3月13日 追記)
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


## 公開鍵 を Raspberry Pi へ 送る
生成したキーペアで認証ができるように公開鍵を Raspberry Pi へ 送ります.
`ssh-copy-id` コマンド が あると、簡単に転送できるのですが Windows 7 および Microsoft の [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases) では `ssh-copy-id` が ないので、`scp` で 転送してから、SSH 接続して鍵登録をします.
```shell-session
c:\> scp %USERPROFILE%\.ssh\id_ed25519.pub pi@raspberrypi.local:~
pi@raspberrypi.local's password: [PASSWORD]
id_ed25519.pub                     100%   83     0.1KB/s   00:00
```

SSH で Raspberry Pi で接続し、公開鍵の設定をします.
```shell-session
pi@raspberrypi:~ $ mkdir ~/.ssh
pi@raspberrypi:~ $ chmod 700 ~/.ssh
pi@raspberrypi:~ $ cat ~/id_ed25519.pub >> ~/.ssh/authorized_keys
pi@raspberrypi:~ $ chmod 600 ~/.ssh/authorized_keys
pi@raspberrypi:~ $ rm -f ~/id_ed25519.pub
```


## Raspberry Pi の SSH サーバ 設定
公開鍵で認証できるようになったので、パスワード認証を止めます.
合わせて `root` ユーザ の ログインも禁止します. (パスワードも公開鍵もないのでログインのしようもないのですが明示的に止めておきます)
以下に設定部分を抜粋します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/ssh/sshd_config
# Authentication:
PermitRootLogin no
RSAAuthentication no

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no
```

設定後 SSH サーバ を 再起動します. 再起動する前に公開鍵認証でログインできていることを再度確認しておきます. パスワード認証が行えなくなるため、公開鍵認証で入れないと SSH で ログインできなくなります.
```shell-session
pi@raspberrypi:~ $ sudo systemctl reload ssh
```


## pi ユーザ の パスワード削除
ついでに pi ユーザ の パスワードを削除します. これにより、Raspberry Pi へのアクセスは SSH からの 公開鍵認証 に しぼることができます. (ネットワーク障害などで完全に入れなくなることもありますが、その際には諦めるということで...)
とはいえ シングル・ユーザ・モード や 本体ごと or SD カード を 持っていかれれば簡単に入られてしまいますが、デフォルトで誰でも知っているパスワードを持っている必要もないので消しておきます.
```shell-session
pi@raspberrypi:~ $ sudo cat /etc/shadow | grep pi
pi:$7$anh205TS$KBB826BAAEqF/kSsT71NkzkaBAjh8bbKrnn6b4oikeABwQTPHHoGRnaXXX.KX9MEn1eDm9XMSurRWiqg1pVXXX/:17067:0:99999:7:::

pi@raspberrypi:~ $ sudo passwd -d pi

pi@raspberrypi:~ $ sudo cat /etc/shadow | grep pi
pi::17067:0:99999:7:::
```



- - - -
これで、だいリモートアクセスを保護できるようになりました. ラズパイ自体が小さく(特に Zero)、また IoT デバイス のように使ったりするつもりなので シングルモード や SD カードが心配なので、追々調べていきます.
