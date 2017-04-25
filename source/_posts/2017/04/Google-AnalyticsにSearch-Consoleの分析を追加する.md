---
title: Google Analytics に Search Console の 分析を追加する
date: 2017-04-17
comments: true
categories: ブログ
tags:
  - Hexo
---

![](/images/hexo/hexo-3.2.png "Hexo")

このブログにアクセスしてくださる方々も増えてまいりました. ありがとうございます！
[Google Analytics を 設置](/2016/12/20/HexoにGoogle-Analyticsを設置する/) していましたので、アクセスいただいている数値を知ることができますが、一番のアクセス元である検索エンジンから、どのような検索ワードで来てくださっているのかはわかりません. ということで、Google Search Console を 設置したいと思います. (今まで設定してなかったんかい...)

**作業環境**
- Google Analytics
- Google Search Console


## Google Analytics から、Google Search Console の 設定
Google Analytics へ サインアップ＆ログインしていることを前提に、Google Search Console の 設定をしたいと思います. まだサインアップしていない場合は、[こちら](/2016/12/20/HexoにGoogle-Analyticsを設置する/#Google-Analytics-の-アカウント作成) を ご参照ください.

[Google Analytics](https://www.google.com/intl/ja_jp/analytics/) の ウェブサイトへアクセスし、左のメニュー から、[Search Console] - [ランディングページ] を クリックし、右のコンテンツから [Search Console の データ共有を設定] ボタンをクリックします.
![](/images/analytics/search/01.png)

各種設定のような画面が表示されますので **一番下** まで行き、[Search Console を 調整] ボタンをクリックします.
![](/images/analytics/search/02.png)

画面 **一番上** まで行きます. (画面が書き換えられたのに、スクロール位置が固定されているようで一番下の何もない状態で描画されるので焦りました)
[Search Console の サイト] に ある [編集] の リンク を クリックします.
![](/images/analytics/search/03.png)

新しいタブ(or ウィンドウ) が 開き、Search Console の 連携画面が表示されます. [Search Console に サイトを追加] ボタン を クリックします.
![](/images/analytics/search/04.png)

さらに新しいタブが開き、Search Console の ようこそ画面が表示されます. ここにウェブサイトの URL を 入力し、[プロパティを追加] ボタンをクリックします.
今回は、これまでのブログ設置の流れから GitHub Pages に あるものとして https://[username].github.io/ と しました.
![](/images/analytics/search/05.png)

入力したウェブサイトの所有権があるかを確認されます. Google Analytics 連携しているので、推奨されている Google アナリティクス で 進めます. そのまま [確認] ボタンをクリックします.
![](/images/analytics/search/06.png)

確認が取れたこと表示されるので、[続行] リンクをクリックしして進めます.
![](/images/analytics/search/07.png)

Search Console の ダッシュボードへ戻ります.
![](/images/analytics/search/08.png)

これで、Google Search Console の 設定 が できました. Search Console として利用することはできるようになりましたが、Google Analytics で 一元的に見たいので、続いて Google Analytics との連携を進めます.


## Google Analytics と Search Console の 連携設定
Search Console の 連携画面のタブに戻ります. ここで `F5` キー もしくは、`Ctrl + R` を 押して画面をリロードします. そうすると、先ほど Google Search に 設定した URL が 表示されます.
追加した URL の ラジオボタンを選択し、[保存] ボタンをクリックします.
![](/images/analytics/search/09.png)

Google Analytics への関連付けを行ってよいかの確認ダイアログが表示されるので [OK] ボタンをクリックします.
![](/images/analytics/search/10.png)

これで、Google Analytics と Google Search の 連携が設定できました.


## いざ確認！
さぁ Google Analytics に 戻り リロード！ どのような検索ワードでいらっしゃってくださっているのか、いざ...
表示されるまで、２～３日かかるらしいです. はい.
![](/images/analytics/search/11.png)



- - - -
無事、Search Console を 設置することができました.
結果は ２～３日ごとのことで、すぐに見れないのは残念ですが、まぁ そうですよね. もっと早くから設定しないとですね. いろいろ書いてみたいことが多くて、ブログ環境回りがついつい後回しになってしまいました...
とりあえず表示されるのを楽しみに待ちたいと思います.
