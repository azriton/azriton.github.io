---
title: Raspbian Jessie Lite の SDカード 延命化
date: 2017-03-16
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Raspbian
---

![](/images/raspi/raspbian-jessie-lite/raspbian-jessie-lite.png "Raspbian Jessie Lite")

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
とりあえず考えられそうな書き込み元を止めてみました. それでも Raspberry Pi 3 の ACT LED が かなり点滅しているので、何かしら読み書きしているのかなぁと.
本格的にやるなら監視用のツールでも入れるなり、読み込み専用のファイルシステムにするなりした方がよいのでしょうが、かなりの手間になってしまうので追々と.
