---
title: Raspbian Jessie Lite の SDカード 延命化
date: 2017-03-16
updated: 2017-03-16
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/assets/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

Raspberry Pi を サーバのように連続稼働させておくとなると、SD カードの寿命が気になってきます. ちょっと心配なので SD カードへの書き込みを減らして、少しでも延命させたいと思います.

**作業環境**
- Raspbian Jessie Lite


## Swap 領域 を 無効化する
Raspberry Pi 2 と 3 は メモリが 1GB あり、Zero と Zero W は 512MB あります. 十分な量とは言い難いかもしれませんが、こんな小さなデバイスに様々な機能を押し込めるよりも、機能を絞って乗っているメモリで間に合うようにするのも手です.
そのため、まずは Swap 領域を無効にして SD カードへの退避書き込みをなくしたいと思います.

まず、Swap で 確保されている量を確認します. 約 100MB Swap に 割り当てられてるようです.
```shell-session
pi@raspberrypi:~ $ free -h
             total       used       free     shared    buffers     cached
Mem:          923M        77M       846M       6.2M       6.9M        39M
-/+ buffers/cache:        30M       892M
Swap:          99M         0B        99M
```

続いて、現在使用している Swap 領域を無効化し、また Swap 領域のマネージャをパケージごと削除して完全に使用しないようにします.
```shell-session
pi@raspberrypi:~ $ sudo swapoff --all
pi@raspberrypi:~ $ sudo apt-get purge -y --auto-remove dphys-swapfile
pi@raspberrypi:~ $ sudo rm -fr /var/swap
```


## テンポラリ領域 を RAM へ 配置する
テンポラリ領域 `/tmp` と `/var/tmp`、そうしょっちゅう置かれている感じは無いのですが、わずかとはいえ SD カードへの書き込みは減らしたいところ. これらは自動的に削除されることが前提の場所なので、電源を切った際に消えても問題は無いでしょう.

`/etc/fstab` で `/tmp` と `/var/tmp` に それぞれ `tmpfs` で メモリ上のファイルシステム を割り当てます. エディタで `/etc/fstab` を 開き、下記の 2行を追加します. 追加後は `sudo reboot` して新しいファイルシステムで起動します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/fstab
tmpfs           /tmp            tmpfs   defaults,size=32m,noatime,mode=1777  0       0
tmpfs           /var/tmp        tmpfs   defaults,size=16m,noatime,mode=1777  0       0

pi@raspberrypi:~ $ sudo rm -fr /tmp
pi@raspberrypi:~ $ sudo rm -fr /var/tmp
pi@raspberrypi:~ $ sudo reboot
```


## rsyslog の ログ出力を抑制する
続いてログ出力を抑制します. Raspberry Pi の 用途にもよりますが、必要なログだけに絞り込むことで SD カードへの書き込みを減らすことができます.
`/etc/rsyslog.conf` を エディタで編集します. 編集箇所は `#### RULES ####` 以降で、下記のように先頭に `#` を 付けてコメントアウトします. 保存後は rsyslog を 再起動します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/rsyslog.conf
#daemon.*                       -/var/log/daemon.log
#kern.*                         -/var/log/kern.log
#lpr.*                          -/var/log/lpr.log
#mail.*                         -/var/log/mail.log
#user.*                         -/var/log/user.log

#mail.info                      -/var/log/mail.info
#mail.warn                      -/var/log/mail.warn
#mail.err                       /var/log/mail.err

#news.crit                      /var/log/news/news.crit
#news.err                       /var/log/news/news.err
#news.notice                    -/var/log/news/news.notice

#*.=debug;\
#       auth,authpriv.none;\
#       news.none;mail.none     -/var/log/debug

pi@raspberrypi:~ $ sudo systemctl restart rsyslog
```


## ログ出力ディレクトリ を RAM へ 配置する
ログ出力ディレクトリ を RAM へ 配置し SD カードへの書き込みを抑制します. なお、これをやると電源を切るとログがなくなってしまうので、必要なログがある場合は SD カード側に出すようにするか、定期的＆シャットダウン時に退避する仕掛けが必要となります.

まずは `/etc/fstab` に `/var/log` を `tmpfs` で マウントするように設定します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/fstab
tmpfs           /var/log        tmpfs   defaults,size=32m,noatime,mode=0755  0       0
```

起動時に `/var/log` の ディレクトリ構成を復元するスクリプトを設置します. これを忘れるとエラーが発生したり、起動できないプロセスがあったりするので、忘れないようにします.
`/etc/rc.local` の `exit 0` の 手前に下記を追加します.
```shell-session
pi@raspberrypi:~ $ sudo nano /etc/rc.local
mkdir -p /var/log/apt
mkdir -p /var/log/fsck
mkdir -p /var/log/ntpstats
mkdir -p /var/log/samba

chmod 750 /var/log/samba
chown ntp:ntp /var/log/ntpstats
chown root:adm /var/log/samba

touch /var/log/btmp
touch /var/log/lastlog
touch /var/log/wtmp

chmod 600 /var/log/btmp
chmod 664 /var/log/lastlog
chmod 664 /var/log/wtmp

chown root:utmp /var/log/btmp
chown root:utmp /var/log/lastlog
chown root:utmp /var/log/wtmp

exit 0
```

最後にリブートします.
```shell-session
pi@raspberrypi:~ $ sudo rm -fr /var/log
pi@raspberrypi:~ $ sudo reboot
```


## ext4 ファイルシステム の ジャーナル を 無効化する
ファイルの変更ログの記録で SD カードへ書き込みが発生するので無効化し、抑制します. 本格的なサーバ運用でしたら必須の機能ですが、そうゆのはキチンとしたサーバ用 H/W で 稼働させるとして、ラズパイにはカジュアルな感じで動いてもらおうと思い止めます.
```shell-session
pi@raspberrypi:~ $ sudo umount /dev/mmcblk0p2
pi@raspberrypi:~ $ sudo tune2fs -O ^has_journal /dev/mmcblk0p2
tune2fs 1.42.12 (29-Aug-2014)

pi@raspberrypi:~ $ sudo reboot
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
とりあえず考えられそうな書き込み元を止めてみました. それでも Raspberry Pi 3 の ACT LED が かなり点滅しているので、何かしら読み書きしているのかなぁと.
本格的にやるなら監視用のツールでも入れるなり、読み込み専用のファイルシステムにするなりした方がよいのでしょうが、かなりの手間になってしまうので追々と.
