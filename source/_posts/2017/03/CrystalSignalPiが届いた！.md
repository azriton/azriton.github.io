---
title: Crystal Signal Pi が 届いた！
date: 2017-03-04
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Crystal Signal Pi
---

![](/images/raspi/crystal-signal-pi/crystal-signal-pi.png "Crystal Signal Pi")

Raspberry Pi に 約 7cm ぐらいの 四角柱 を 立て、マルチカラー の LED を で光らせることができるモジュール Crystal Signal Pi が 届き、ようやく組み立てができました. 本来はサーバなどの監視を行い、警告をわかりやすく表示してくれるものですが、いろいろな使い方に挑戦してみたいと思います. まずは、早速組み立てから.

**作業環境**
- Raspberry Pi 3 Model B
- Crystal Signal Pi


## Crystal Signal Pi とは？
[Crystal Signal Pi](http://crystal-signal.com/) は、Raspberry Pi に 約 7cm ぐらいの 四角柱 を 立て、マルチカラー の LED を で光らせることができるモジュールです.
クラウド・ファンディングで購入者を募集されていることを知って、興味を持ち応募しました. クラウド・ファンディングの仕組みを使っていたとはいえ、製造は確定していたので普通に購入する形です.

"[光で監視するソリューション](https://www.infiniteloop.co.jp/blog/2016/11/release-crystal-signal-pi/)" を キーワードに、2016年11月 の オープンソースカンファレンス 2016 Tokyo／Fall で デモをされたようです.
その際の資料はこちら、でしょうか. → "[あらゆるイベントを可視化する! RaspberryPiで作るLED警告灯ソリューション](https://www.slideshare.net/infinite_loop/raspberrypiled)"


## Crystal Signal Pi 到着 ＆ 組立
開発元のインフィニットループさんの封筒で届きました. 写真では宛名部分を折り返していますが、 2つ購入で A4 の 封筒半分ちょっとぐらいです.
![](/images/raspi/crystal-signal-pi/build/01.jpg)

中身は緩衝材でしっかり保護されています.
![](/images/raspi/crystal-signal-pi/build/02.jpg)

1つ 1つ パッケージングされています. 丸穴をあければお店に そのまま吊るせる感じです. こんな感じで手軽に購入できるようになってほしいですね.
![](/images/raspi/crystal-signal-pi/build/03.jpg)

パッケージの中身は以下になります. 丁寧に部品ごとにも袋に入っています.
- 説明書など書類 3枚
- 基盤 1枚
- ケースのアクリル板 2枚 と ネジ、滑り止め
- LED で 発光する 四角柱 と ゴムバンド
![](/images/raspi/crystal-signal-pi/build/04.jpg)

説明書があるので、そう迷わず組立できました. 今回は Raspberry Pi 3 Model B を 使いました. なお組み立ての説明書 は [PDF で 公開](http://crystal-signal.com/other/Crystal_Signal_Pi_construction.pdf) されています. PDF は カラーなのでイメージがつかみやすいかもしれません.
まずケースのアクリルにネジとスペーサー入れ、ラズパイを載せ、続いてラズパイとCrystal Signal Pi 基盤の間用のスペーサーで留めます. ネジ穴が切ってあるのでクルクルして簡単に仮留めできます.
![](/images/raspi/crystal-signal-pi/build/05.jpg)

続いて Crystal Signal Pi の 基盤を載せます. GPIO に合わせたソケットが取り付けられているので、そのまま差し込みます.
![](/images/raspi/crystal-signal-pi/build/06.jpg)

最後にカバーのアクリル板をかぶせますが、四角柱を通してからカバーを載せます. 後は しっかり、ねじ止めして完成！
![](/images/raspi/crystal-signal-pi/build/07.jpg)


## いざ、点灯！
Crystal Signal Pi の ソフトウェアが用意されていますが、インストールしなくても通電すると、緑とオレンジに交互に点滅した後、緑で光り続けます.
明るいところでは四角柱の側面部分は光がとおっている感じで発光感は強くないです.
![](/images/raspi/crystal-signal-pi/build/08.jpg)

天頂部は強く光っており斜めから見てもかなり眩しいです.
![](/images/raspi/crystal-signal-pi/build/09.jpg)

明るさ調整するにはソフトウェアが必要ですね. ソフトウェアを入れていないので、こちらも当然ですが、オレンジ色の丸ボタンも反応しません. また、`shutdown` した後も光続けています.


- - - -
ラズパイで簡単な状態通知をできる Crystal Signal Pi、面白いプロダクトだと思います. API も 用意されているのでアイデア次第でいろいろな使い方ができそう.

2017年 3月現在、初期ロットのクラウド・ファンディングが終わったところで、次回ロットについては、[オフィシャル・サイト](http://crystal-signal.com/)でメール登録することで連絡をもらえるとのことです. 思った以上にすごくよかったので、さっそくメール登録しました！
