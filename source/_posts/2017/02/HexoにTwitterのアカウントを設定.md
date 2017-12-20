---
title: Hexo に Twitter の アカウント を 設定
date: 2017-02-25
comments: true
categories: ブログ
tags:
- Twitter
- Hexo
- Disqus
---

![](/assets/hexo/hexo-3.2.png "Hexo")

[Twitter の アカウントを開設した](/2017/02/16/Twitterのアカウント作成/) ので、いよいよコメント欄 Disqus の 設置！と、行きたいところですが、ちょっと寄り道. Hexo の デフォルト・テーマ Landscape に Twitter の 設定項目があるので設定したいと思います.

**作業環境**
- Hexo 3.2
- Twitter


## Landscape の Twitter 設定項目
デフォルト・テーマ Landscape の 設定ファイルは `/themes/landscape/_config.yml` に なります. 以下に抜粋したとおり `twitter:` が あります.
```yaml
# Miscellaneous
google_analytics:
favicon: /favicon.png
twitter:
google_plus:
fb_admins:
fb_app_id:
```

## 設定値 と 効果 は？
Landscape の ドキュメント [Configuration -- hexo-theme-landscape](https://github.com/hexojs/hexo-theme-landscape#configuration) によると、"twitter - Twiiter ID" と あっさりしすぎてて、何のことか、また何を入れるかわかりません. 困った...

情報がないか探してみると、[Configuration -- hexo-theme-bootstrap-blog](https://github.com/bryik/hexo-theme-bootstrap-blog#configuration)
 に "twitter_id - Twitter ID of the author (ie. @c_g_martin)" が ありました.

異なるテーマなので同じで大丈夫かと思いましたが、"The default Landscape Hexo theme was used as the starting point - [Development](https://github.com/bryik/hexo-theme-bootstrap-blog/blob/master/README.md#development)" と あるので信じて設定したところ、どうやら動作したようです.

なお、設定値は以下のように、アットマークを付けシングルクォートで囲む必要があります.
```yaml
# Miscellaneous
google_analytics:
favicon: /favicon.png
twitter: '@username'
google_plus:
fb_admins:
fb_app_id:
```

さっそくローカルで確認したのですが、特に変化はなかったようです... あれ？
Twitter の アイコンが増えて、リンクしてくれたりするのかなぁぐらいに思っていたのですが、どうやら違うようです. では、何が設定されたのか...


## Twitter Cards !
とりあえず生成されたサイトのソースを確認します. すると `head` に Twitter 関連のメタ情報が出力されていました.
![](/assets/hexo/hexo-twitter.png)

この出力内容は [Twitter Cards](https://dev.twitter.com/cards/) に 関するもので、Twitter に サイトのリンクをツイートされた際に、以下のようにサイトの画像サムネイルやタイトル、先頭分のサマリが表示されます.
![](/assets/twitter/twitter-cards.png)



- - - -
Twitter Cards の 説明によると、"リッチメディアをツイートに添付してウェブサイトへのトラフィックを促進できます -- Twitterカード — Twitter Developers" とのこと.
ただの文字列リンクより、サマリが表示されるので設定しておくに越したことはないので、このまま設定しておきたいと思います.

Twitter や GitHub の アカウントへのリンクを貼る機能と勝手に思っていましたが早とちりでした. しまった...
とはいえ、リンクは貼りたいので、どっかでテーマを変えるなり、カスタマイズしないとだなぁ～
