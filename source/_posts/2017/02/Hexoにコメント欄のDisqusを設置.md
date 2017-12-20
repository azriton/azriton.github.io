---
title: Hexo に コメント欄 の Disqus を 設置
date: 2017-02-26
comments: true
categories: ブログ
tags:
- Hexo
- Disqus
- Twitter
---

![](/assets/hexo/hexo-3.2.png "Hexo")

[Twitter の アカウントを開設した](/2017/02/16/Twitterのアカウント作成/) し、ちょっと[寄り道](/2017/02/25/HexoにTwitterのアカウントを設定/)しましたが、いよいよコメント欄 Disqus を 設置したいと思います.

**作業環境**
- Hexo 3.2
- Disqus
- Twitter


## Disqus の アカウント作成
Disqus の Signup ページ [https://disqus.com/profile/signup](https://disqus.com/profile/signup) へ アクセスします.

今回は Twitter の 認証連携でサインアップするので Twitter アイコン を クリックします.
![](/assets/hexo/disqus/signup/02.png)

Twitter の サイト へ 遷移するので、Twitter へ サインインして連携を許可します. 各種権限の許可ができない場合は Twitter 連携を諦めて直接サインアップします.
![](/assets/hexo/disqus/signup/03.png)

許可すると Disqus の サイトへ戻ります. 名前、メールアドレス、パスワードの入力をします. 名前 は Twitter からひいてくれたようですが、メアド、パスワードは入力が必須でした... この辺を入力したくない(特にパスワード)から Twitter で 認証したのに、意味なかった. orz
![](/assets/hexo/disqus/signup/04.png)


## Disqus サイト の 開設
サインアップが完了すると、そのまま Disqus サイト の 開設が始まります.
ブログに設置するためにアカウントを作成したので、そのままサイトの開設に進みます.

ここでの「サイト」は Disqus に 開設するサイトのようで、ブログサイトとは関係ないようなので注意が必要です. (ブログサイトから連携する先の Disqus サイト と いったイメージでしょうか)

2つの選択肢が示されます. 今回はブログにコメント欄を設置するので [I want to install Disqus on my site] を クリックします.
![](/assets/hexo/disqus/signup/05.png)

基本設定の入力画面が表示されます.
**[Website Name] は ユーザ名 に あたるものを入力します.** ここで入力した文字列から生成された **Your unique disqus URL** の 先頭部分 が ブログから連携する Disqus の サイト の **Short Name** になります. 個々のブログやウェブサイトの名前でないことに注意が必要です.
ここで **表示された "Your unique disqus URL" = Short Name** を ひかえておきます.
Category や Language は お好みで選択します.
![](/assets/hexo/disqus/signup/06.png)

無事、Disqus サイトの開設ができました.
[Got it. Let's get started!] を クリックし、続いてブログ・サイトの連携を作成に進みます.
![](/assets/hexo/disqus/signup/07.png)


## ブログ・サイト の 連携設定
自分のサイトで使っているサービスやプロダクトを選択する画面が表示されます.
Hexo は ラインナップされていないため、汎用 の [I don't see my platform listed] を クリックします.
![](/assets/hexo/disqus/signup/08.png)

コードによるセットアップ方法について解説がありますが、Hexo の デフォルト・テーマ Landscape は 設定 1つで対応できるので、勉強がてら眺めつつ一番下の [Configure] を クリックします.
![](/assets/hexo/disqus/signup/09.png)

ブログ・サイトの設定を入力する画面が表示されます. **ここは自サイトの情報になります.**
[Website Name] は、ブログ の サイト名になります. コメント欄のタイトルに表示されます. [Website URL] は、ブログ の URL を 入力します. [Category]、[Description]、[Language] は お好みで.
![](/assets/hexo/disqus/signup/10.png)

無事、セットアップが完了しました！
![](/assets/hexo/disqus/signup/11.png)


## Hexo に Disqus を 設定
Hexo の デフォルト・テンプレート Landscape の `after-footer.ejs` と `article.ejs` で `config.disqus_shortname` の 有無を判定し、コメント欄を出力するかが制御されています.

この `config.` の Prefix が 曲者で、Hexo の 設定 に `disqus_shortname` が 必要でした.
テーマに関する設定項目と思っていたので、Landscape の `/themes/landscape/_config.yml` に 設定をしたところ、どうしても表示されずはまりました. 同じファイル名ではありますが、Hexo の `/_config.yml` に 以下のように設定します.
```yaml
disqus_shortname: [shortname]
```

ここで、Disqus の サイトで開設した Web サイト の **Short Name** の 値 を 設定します.
ローカルで確認する際に、もしかしたら正しく表示されないケースがあるので、その場合は `hexo clean` してから再生成すると表示されます.
![](/assets/hexo/disqus/signup/12.png)



- - - -
Disqus アカウントのサインアップから、Disqus サイトの開設、ブログ連携 と 一連の作業で流れて行ったので、作業している時に今何をしているかちょっとわからないところがあり、迷ってしまいました... 最近 Disqus の ウェブサイトが変わったのか、自分のやり方が違ったのか、参考情報もなくて悩みつつでしたが、改めて文章で起こしてみると、3つのパートだったことが分かりました. 勉強になりました.
