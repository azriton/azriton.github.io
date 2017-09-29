---
title: CircleCI の 自動ビルド で Hexo の master ブランチをビルドさせない
date: 2016-12-09
comments: true
categories: ブログ
tags:
- CircleCI
- GitHub
---

![](/images/circleci/circleci.png "CircleCI")

前回 [CircleCI で Hexo の 自動ビルド と デプロイ設定](/2016/12/08/CircleCIでHexoの自動ビルドとデプロイ設定/) で GitHub の `source` ブランチ に プッシュすると CircleCI が 自動的にビルドしてデプロイするようにしました.
これで記事が自動的に公開できるようになったのですが、CircleCI が 記事に使っている `master` ブランチもビルドしようとしてエラーが発生します. 今回はこのエラーの原因と対策をします. (止めないと記事を公開するたびに、ビルドエラーの通知が来てしまう...)

**作業環境**
- CircleCI
- Hexo 3.2


## エラー の 原因
CircleCI の ビルド画面 や、エラー通知のメールを見るとわかりますが [NO TESTS] と なっています. 前回の記事の通り、CircleCI は テストがないとエラーとして扱うためです.
では このエラーはどこから来たのか、これは [master] と 書かれているように、`master` ブランチをビルドして、テストがないためにエラーとなったものになります.
![](/images/circleci/hexo-master-error.png)

はて、以下に抜粋した通り `circle.yml` で ビルドするのは `source` ブランチに限っているはずです. なぜ `master` ブランチでビルドが走ってしまったのでしょうか？
```yaml
general:
  branches:
    only:
      - source
```

原因は、[Hexo と GitHub Pages で ブログ環境の構築](/2016/11/01/HexoとGitHub-Pagesでブログ環境の構築/) で ウェブサイトのソースをコミットする `source` ブランチを `git checkout --orphan source` コマンドで作成しているとことにあります.

`git checkout --orphan` は 親を持たない空のブランチを作成するオプション指定です. これにより `master` と `source` は 関連を持たないブランチとなっています.
その関連のない `source` ブランチに `circle.yml` は あるので、`master` は `circle.yml` を 持たない状態になっています. そのため、いくらビルドするブランチから `master` を 外してもビルドは行われる状況になっていたということになります.


## master ブランチをビルドさせない対策検討
原因が分かったので対策に入りたいと思います.

まず浮かぶのは `master` ブランチ に `circle.yml` を 置く方法です. この方法だと公開されるウェブサイトにも `circle.yml` が 配置され公開されることになります. う～ん、いまひとつ...

ではどうするか、[Skip a build - CircleCI](https://circleci.com/docs/skip-a-build/) に 書かれている、コミット・メッセージ に `[ci skip]` もしくは `[skip ci]` を 含めるというやり方があります.

この方法だと毎回のコミット・メッセージに `[ci skip]` を 入れることになりますが、ここのコミット・メッセージは Hexo が 自動で行っているため手間はかかりません. 

また コミット・メッセージ に 毎回入れてよいかという観点では、`master` ブランチは GitHub Pages の ウェブサイト公開用で Hexo で 上書きされ更新履歴のトラックも使っていません.

以上の事から、コミット・メッセージに `[ci skip]` を いれても問題ないと考えられます.


## コミット・メッセージ に [ci skip] を 入れる
Hexo の 設定ファイルである `_config.yml` の `deploy` セクション に `message` を 追加します.
今回はデフォルトのコメントの最後に `[ci skip]` を 追加する形にしました.
設定の詳細は、こちら [Deployment | Hexo](https://hexo.io/docs/deployment.html#Git) に なります.
```yaml
deploy:
  type: git
  repo: git@github.com:[username]/[username].github.io.git
  branch: master
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }} [ci skip]"
```



- - - -
以上で `master` ブランチのビルドを止めることができました.
CircleCI の ビルド・ログ にはスキップしたことが残ってしまいますが、基本的にエラーが発生しない限りは CI の 画面は見ないですし、不要な設定ファイルをウェブサイトのツリーに配置するよりはよいと思うので、まずは形で行こうかなと.
とはいえ、もっと良い方法があるといいのだけど.
