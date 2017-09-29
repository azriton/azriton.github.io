---
title: Raspberry Pi の CPU 温度 を 記録する
date: 2017-09-29
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Crystal Signal Pi
- Raspbian
---

![](/images/raspi/crystal-signal-pi/crystal-signal-pi.jpg "Crystal Signal Pi")

[OSMC](https://osmc.tv/) として使っている Raspberry Pi Zero が よくフリーズするようになり、ケースをさわってみると暖かいことから熱対策をすることにしました. そのための [ヒートシンク付きケースを C4 Labs さん から購入](/2017/09/26/C4-LabsでRaspberry-Pi-Zeroのヒートシンク付きケースを購入/) しました.
そのままヒートシンクを付けたいところ、ちょっと待って簡易的ではありますが Raspberry Pi の 温度を取得して変化を見てみたいと思います.

**作業環境**
- Raspberry Pi Zero
- OSMC rbp1
- Crystal Signal Pi
- Raspbian Jessie Lite


## 現在の温度を取得する
Raspberry Pi の 温度は `/sys/class/thermal` あたりに格納されています.
そこからファイルを参照することで取得できます. 温度は 1,000倍 になっています.
```console
pi@raspberrypi:~ $ cat /sys/class/thermal/thermal_zone0/temp
34166
```

上記の場合は 34.166 ℃ ということになります.
切り捨てにはなりますが `expr` コマンドと組み合わせると、こんな感じにもできます.
```console
pi@raspberrypi:~ $ expr `cat /sys/class/thermal/thermal_zone0/temp` / 1000
34
```

上記は OSMC で 使っている Raspberry Pi Zero で、Crystal Signal Pi の Raspberry Pi 3 は 57996 でした.


## vcgencmd で 温度を取得する
また `vcgencmd` というコマンドを使っても温度を取得することができます.
こちらは記号も入っています. (2バイト文字がないから 'C で ℃ を 表現するんですね)
```console
$ vcgencmd measure_temp
temp=34.2'C
```


## 温度を定期的に取得して記録する
ローカルファイルに取得した温度を追記していき、どのように変化しているのかが分かるようにします.
ヒートシンクの有無で温度の変化が出るかを見たいだけなので、単純に `cron` で 上記コマンドを仕掛けておくようにします.

OSMC は `cron` が 入っていなかったので、インストールします.
```console
pi@raspberrypi:~ $ sudo apt-get update
pi@raspberrypi:~ $ sudo apt-get install cron -y --no-install-recommends
```

10分に1回なので `*/10 * * * *` で 指定し、日付 と 温度 を `/tmp/thermal.txt` に 追記します.
※ `/tmp` は リブートするとファイルが消えるので恒久的に取っておく場合は他のディレクトリにします.
```console
pi@raspberrypi:~ $ crontab -e
*/10 * * * * /bin/echo -e "`date`\\t`cat /sys/class/thermal/thermal_zone0/temp`" >> /tmp/thermal.txt
```

こんな感じで出力されます. 特に処理していない状態で 10分間なので温度変化は無いようです.
```console
pi@raspberrypi:~ $ cat /tmp/thermal.txt
Thu Sep 28 15:30:01 JST 2017    37932
Thu Sep 28 15:40:01 JST 2017    37932
```



- - - -
10分間隔で取得するようにしたので、これで温度の変化を知ることができます. しばらく記録しておき、ヒートシンクを付けてから違いを確認したいと思います.
本来はちゃんとした監視ツールなどでデータ収集してグラフ化などしたいところですが、直近の問題と対策を検討するようなのでスクリプトで逃げました. ラズパイの台数が多いので、ちゃんと監視できるようにしたいところです. ラズパイの監視は何のツールがいいんだろう...
