---
title: Hexo に Google Analytics を 設置する
date: 2016-12-20
updated: 
comments: true
categories: ブログ
tags:
  - Hexo
---

![](/assets/hexo/hexo-3.2.png "Hexo")

ブログを設置するからにはアクセス数などが見れるようにしたくなるものです.
よく [アクセスカウンタ](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%AB%E3%82%A6%E3%83%B3%E3%82%BF) を 設置して訪問していただいた数を表示することをしていましたが、現在では Google Analytics などを使って詳細な分析をするのが定番ですね.
ということで、Google Analytics を 設置したいと思います.

**作業環境**
- Windows 7
- Hexo 3.2
- Hexo Theme Landscape
- Google Analytics


## Google Analytics の アカウント作成
Google Analytics を 使うには、Google の アカウントがっ必要ですが Gmail で よいので、ここでは持っているものとして、Google Analytics の アカウント作成に入りたいと思います.
[Google Analytics](https://www.google.com/intl/ja_jp/analytics/) の ウェブサイトへアクセスし、[アカウントを作成] ボタンをクリックします.
![](/assets/analytics/signup/01.png)

Google アカウント の メールアドレスを入力し、[次へ] ボタンをクリックします.
GoogleApps で 独自ドメインを設定しているメールアドレスでも利用できました.
![](/assets/analytics/signup/02.png)

Google アカウントのパスワードを入力し、[ログイン] ボタンをクリックします.
![](/assets/analytics/signup/03.png)

アカウント作成のステップが表示されるので、画面右の [お申込み] ボタンをクリックします.
![](/assets/analytics/signup/04.png)

アカウントの設定情報を入力する画面が表示されます. 自分のウェブサイトに合わせた情報を入力していきます.
今回は、アカウント名を GitHub Pages の リポジトリに合わせました.
サイト名 や URL は ウェブサイトに合せます.
業種は特に選択する必要はないですが、設定してないと後で設定するようにアラートがでます.
タイムゾーンは日本に合わせ、データ共有設定は初期値のままにしました.
![](/assets/analytics/signup/05.png)

利用規約の確認画面が表示されます. 同意しないと始まりませんが問題があるようでしたら同意せず利用しないという選択になります.
"お住まいの国または地域の利用規約に同意" と あるように、英語で表示されているものを [日本] に 選択しなおし、内容を確認し問題ないようでしたら [同意する] ボタンをクリックします.
![](/assets/analytics/signup/06.png)

Google Analytics の アカウントが作成され、トラッキング ID が 発行されます.
この ID を コピーしておきます.
![](/assets/analytics/signup/07.png)


## Hexo Theme Landscape に 設定
Hexo の デフォルト・テーマ Landscape は Google Analytics に 対応しています.
テーマの設定ファイル `/[username].github.io/themes/landscape/_config.yml` に Google Analytics の トラッキング ID を 設定します.
`google_analytics` は すでに用意されているので、トラッキング ID を 書くだけの簡単設定です. たすかります！
```yaml
# Miscellaneous
google_analytics: UA-7153XXXX-4
```
※ Hexo の 設定ファイル `/[username].github.io/_config.yml` と 異なることに注意. ファイル名が同じですがテーマのフォルダの中になります.


## いざ確認！
`hexo generate` して ローカルサーバで動作確認すると、`<head>` に Google Analytics のためのコードが追加されています.
![](/assets/analytics/signup/08.png)

トラック ID が 正しいことを確認し、`hexo deploy` で GitHub Pages へ アップします.
Google Analytics の リアルタイム・レポートを見ながら、もう一つのブラウザで本サイトへアクセスすると、アクセス数が表示されます.
うん、自分以外居ない... orz
![](/assets/analytics/signup/09.png)



- - - -
無事、Google Analytics を 設置することができました.
トラッキング ID の 設定だけで追加してくれるので簡単ですね. 今後テーマを変えることもあるかと思いますが、できるだけ自動でやってくれるものを使いたいものです.
