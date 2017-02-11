---
title: Raspberry Pi Zero の 購入
date: 2016-11-13
comments: true
categories: 電子工作
tags:
- Raspberry Pi
---

![](/images/raspi/zero-v1.3.jpg "Raspberry Pi Zero")

[The Raspberry Pi Foundation](https://www.raspberrypi.org/) より、”The $5 computer” と、謳われる $5 で 買えるミニマムな [Zero](https://www.raspberrypi.org/products/pi-zero) が 登場したとのことで、さっそく試してみたいと思い、Amazon.co.jp で 探してみたところ なんと 4,000円近い金額からとなっています…(2016年11月現在)
何とか妥当な金額で買えないかを探してみます.
- - - -


## 公式の販売店を確認する
どのサイトも１人１つまでの制限があるようで、まとめ買いができません. 少し前までは品薄で手に入らなかったようなので、これでもだいぶ改善されたのでしょう. とはいえ、普通に $5 の ふれこみ通りで買うことができそうです.

| 販売店 | 価格 | 　備考 |
|:------:|:----:|:-----|
| [Pimoroni](https://shop.pimoroni.com/products/raspberry-pi-zero) | £4.00 | 　１人１つの制限あり、日本への送料 £5.50～. |
| [The Pi Hut](http://thepihut.com/products/raspberry-pi-zero) | £4.00 | 　１人１つの制限あり、日本への輸出できず. |
| [Adafruit](https://www.adafruit.com/products/2885) | $5.00 | 　１人１つの制限あり、日本への送料 $30～. |
| [CanaKit](http://www.canakit.com/raspberry-pi-zero.html) | $5.00 | 　１人１つの制限あり、日本への送料 $16.95～ |
| [Micro Center](http://www.microcenter.com/product/457746/Zero_Development_Board) | ? | 　買い方が分からず... |


## いざ購入！
さっそく £4 の Pimoroni にて購入してみます.

Pimoroni の Raspberry Pi Zero の ページ [https://shop.pimoroni.com/products/raspberry-pi-zero](https://shop.pimoroni.com/products/raspberry-pi-zero) へ アクセスします.
“Max 1 Pi Zero Per Customer!” と あるように、１人１つしか購入できません. この画面上では何個でもカートに追加できますが、チェックアウト時にエラーになります. ちなみに１回に１個ずつであれば何回でも買えます. 送料が毎回かかってしまいますが…
在庫切れの場合は [In stock] の 部分が [Out of stock] となり購入できませんが、クリック後にメールアドレスを入力することで入荷時に通知がもらえます.

購入するセットを選び [Add to basket] を クリックします.
Raspberry Pi Zero は ミニ HDMI のため変換ケーブルをセットで買ったり、ケースとのセットや、電源、ブレッドボードなどの各種セットを選ぶことができます. 今回 は [Pi Zero only] を 選びました.
![](/images/raspi/pimoroni/01.png)

その後、右上 の [Checkout] を クリックします.
![](/images/raspi/pimoroni/02.png)

カート画面に進み、購入内容が正しいかを確認し、ページ右下 の [Checkout now] を クリックします.
![](/images/raspi/pimoroni/03.png)

ここで、関連製品のお勧めを紹介されます. ケース単体で追加するなどがある場合は、ここでも追加できます.
追加等が終わったら画面を下の方までスクロールし、[Take me the checkout!] を クリックします. ボタンんがグレーで一瞬、押せない or 違うボタン に 思えますが、普通にクリックできます.
※ £4 の Camera Cable は カメラっぽい画像がありますが、ケーブルなので注意が必要です. カメラ本体 は £27.50 で こちら → [Raspberry Pi Camera v2.1 with mount](https://shop.pimoroni.com/products/raspberry-pi-camera-module-v2-1-with-mount)
![](/images/raspi/pimoroni/04.png)

支払方法の選択 と 送付先の入力画面 に 進みます.
今回は PayPal を 使うので、[PayPal] を クリックします.
PayPal は 持っていると海外での買い物用などに便利なことが多いので、この際作っておくのも手です. ここで PayPal か Amazon を 選択しないで進んだ場合は最後に支払情報を選択します. クレジットカード(VISA, Master, Maestro)、Bitcoin が 選べます.
※ Amazon は EU の サイトにつながり、日本 の Amazon.co.jp の アカウントでは使用できませんでした.
![](/images/raspi/pimoroni/05.png)

PayPal の 認証画面へリダイレクトされるので、メールアドレス と パスワードを入力しログインします.
![](/images/raspi/pimoroni/06.png)

支払方法の確認画面が表示されます.
£4 が £3.33 に なっているのは、日本への輸出には 付加価値税(VAT) が かからないため、その分値引きされているからです.
内容を確認し、[同意して続行] を クリックします. ここで続行しても支払は確定しません. PayPal を 使うということの確認になります.
※ 配送先の住所はサンプル用に東京駅を借りてます.
![](/images/raspi/pimoroni/07.png)

Pimoroni の 支払確認画面に戻ります.
PayPal から渡された情報が入力されますが、念のため半角のアルファベットのローマ字住所に直した方が良いでしょう. 画像は東京駅から借りた情報をサンプルにしています. また [Company] と [Apt, suite, etc.] 以外はすべて必須入力です.
入力後 [Continue to payment method] を クリックします.
![](/images/raspi/pimoroni/08.png)

配送方法の選択画面が表示されます.
通常の郵便なら [International Standard] を 選択します. 今回はこちらを選びました.
[Royal Mail Tracked & Signed] は 輸送状況を確認できます.
[UPS Sever] は １日、さすが早いですね！
選択したら [Continue to payment method] を クリックします. 支払方法をあらかじめ選択している場合は、ここで完了となります.
![](/images/raspi/pimoroni/09.png)

注文の確定画面が表示され、送付先情報で入力したメールアドレスにメールが届きます. これで無事に注文が完了し、後は届くのを待つのみです.
Google Maps で 届け先が表示されるなど、ちょっと洒落てますね.
※ サンプル用で某駅を指してます.
![](/images/raspi/pimoroni/10.png)


## 到着♪
International Standard で 注文すると、５～７日で届きます. 以下のようなパッケージで届きました.
今回 は ７日フルでかかりました. しかも、その後に追加注文したものが５日で同時に到着…
パッケージ右側のモザイク部分が住所ですがシールが貼ってあるのは、うっかり日本語で住所入力したら、日本語が出力できなかったのか、Japan と 番地の数値だけが元の台紙に印刷されており、上からシールで日本語の住所が貼られていました. 日本語でも届きましたが、ちゃんと半角のアルファベットのローマ字で住所を入力したほうがよいですね.
![](/images/raspi/pimoroni/11.jpg)

外装の中はこんな感じのグレーの透明な袋に入っています.
![](/images/raspi/pimoroni/12.jpg)



- - - -
英語のサイトで、海外からの輸入となるので少しドキドキしますが、特に難しいことはなく簡単に購入することができました.

Raspberry Pi Zero に 限らず、ケースも安く買えますし、HAT/pHAT などの Raspberry Pi の 上につける拡張ボード的なものも手に入ります. よほど急ぎでない限り Pimoroni で 買おうかなと思います.

microSD カード や SD Adapter と 並べてみると、よりはっきりしますが Raspberry Pi Zero は 小さいですね. これから色々なもの作りにチャレンジしてみたいと思います.
![](/images/raspi/zero-v1.3.jpg "Raspberry Pi Zero")
