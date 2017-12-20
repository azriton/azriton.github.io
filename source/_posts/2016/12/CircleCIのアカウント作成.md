---
title: CircleCI の アカウント作成
date: 2016-12-05
comments: true
categories: 開発環境
tags:
- CircleCI
- GitHub
---

![](/assets/circleci/circleci.png "CircleCI")

継続的インテグレーション(CI: Continuous Integration) の プラットフォームである [CircleCI](https://circleci.com/) を 使って、GitHub に ある 様々リポジトリをビルドできるようにしたいと思います.
うまく流れるようになると GitHub の 特定のブランチにマージされると、ソースコードのビルと、テスト、パッケージング、デプロイ までの一連の作業が自動的に行われるようになります！
まずはビルドを行う前に、アカウントの作成をします.

**作業環境**
- CircleCI


## CircleCI とは？
こちらのスライド、[はじめての CircleCI](http://www.slideshare.net/mogproject/circleci-51253223) が しっかり まとめてくださっており、改めて書くことがないどころか、勉強になりました. 素晴らしいスライドありがとうございます！
ということで、やりたいことに特化してアカウント作成部分だけの記事にしたいと思います.


## アカウントの作成
CircleCI の Signup ページ [https://circleci.com/signup](https://circleci.com/signup) へ アクセスします.
GitHub と Bitbucket の どちらで認証するか聞かれます. 今回は GitHub を 選択しました.
![](/assets/circleci/signup/01.png)

GitHub の サイトへリダイレクトされますので、GitHub へ サインインします.
![](/assets/circleci/signup/02.png)

CircleCI へ 渡す権限の確認画面が表示されます.
ここで許可しないと次へ進めないのもありますが、CircleCI で Private Repository の ビルドなども行うので妥当な権限と思いますので [Authorize application] を クリックして許可します.
問題がある場合は [Authorize application] を クリックせず、CircleCI の 利用は断念します.
![](/assets/circleci/signup/03.png)

CircleCI へ 戻されます. 無事ログインできました.
![](/assets/circleci/signup/04.png)



- - - -
GitHub と 連携するだけなので、アカウント作成は簡単にできますね.
この後、まずは Circle CI で Hexo を 動かせるようにして、投稿の編集を GitHub の  `draft-xxx` ブランチで行い、`source` ブランチ へ マージされたら CircleCI が 回って、自動的にデプロイされるようにしたいと思います.
