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

![](/assets/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

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
### Raspberry Pi 3
ラズパイを始めるには 全部入りの Raspberry Pi 3 が 手ごろではないでしょうか. <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB018K9NNJW%2Fref%3Dnosim" target="_blank" >Raspberry Pi Zero - ラズベリー・パイ ゼロ</a> や <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01GFAIKMI%2Fref%3Dnosim" target="_blank" >Raspberry Pi Zero W - ラズベリー・パイ ゼロ W</a> は 国内では入手しずらいため値上がりしてしてますし、GPIO ピン も 自分で付ける必要があったりと色々と手がかかります. その分楽しいというのもありますが.
届くまで時間がかかってもよい場合は こちら [Raspberry Pi Zero の 購入](/2016/11/13/Raspberry-Pi-Zeroの購入/) で 記事にしました [Pimoroni](https://pimoroni.com/) さん から購入する手もあります.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41zcKgUQXtL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" >Raspberry Pi 3 MODEL B 【RS正規流通品】</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Raspberry Pi 2016-01-28    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Raspberry Pi 3 の 電源
Raspberry Pi 3 は 5V/3A の 電源が必要になります. スマホの充電アダプタでは出力が足りない場合もあるので確認が必要です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41p5wekKaIL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" >Raspberry Pi用電源セット(5V 3.0A)－Pi3フル負荷検証済</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> TechShare     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### マイクロ SD カード
ラズパイ の OS や ストレージに必要です. 16GB あれば十分だと思いますが、用途次第なので お好みのサイズで用意します.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51JBMptiJgL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" >【Amazon.co.jp限定】Transcend microSDHCカード 16GB Class10 UHS-I対応 無期限保証 Nintendo Switch / 3DS 動作確認済 TS16GUSDU1PE (FFP)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> トランセンド・ジャパン 2015-10-02    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
これで、だいリモートアクセスを保護できるようになりました. ラズパイ自体が小さく(特に Zero)、また IoT デバイス のように使ったりするつもりなので シングルモード や SD カードが心配なので、追々調べていきます.
