---
title: Hexo と GitHub Pages で ブログ環境の構築
date: 2016-11-01
comments: true
categories: ブログ
tags:
- Hexo
---

![](/images/hexo/hexo-3.2.png "Hexo")

調べものメモや環境構築の記録などを残しておく場所がほしいと考えて調べていたところ、GitHub Pages と Hexo の 組み合わせ が よさそうなので、早速調べつつ記録を残してみたいと思います.

**作業環境**
- Windows 7
- Node.js 6.9.1 LTS
- Git 2.11.0
- Hexo 3.2


## GitHub Pages とは？
GitHub に、独自のウェブサイトをホスティングするための公式の機能です.

ソースコード管理 の Git ホスティング で 有名な GitHub で、静的なウェブサイトもホスティングできる機能があり、それが GitHub Pages です. [公式ページ](https://pages.github.com) に "Websites for you and your projects." と あるように、GitHub の アカウント、組織、プロジェクト(=リポジトリ) のためのウェブサイトを簡単に持つことができるようになります.

GitHub リポジトリ で 直接管理することができるため、ウェブサイトのバージョン管理を行うこともできますし、Issues や Projects も 使えるので 記事作成 の ToDo 管理 や チームでのウェブサイト管理も行えます. もちろん Pull Request も 使えます. 一方で GitHub Pages は、静的なウェブサイトのホスティングの機能であるため、データベースを使ったり、動的な機能を盛り込むことができません.

今回は調査メモや環境構築の記録なので動的な機能を持たせることは想定しないので、GitHub Pages を 使ってみたいと思います.


## GitHub Pages での ウェブサイト公開方法
大きく 2つのタイプでウェブサイトを公開できます. 
1. https:// **name**.github.io 形式 の **ユーザ ウェブサイト・組織(Organization)ウェブサイト**
1. https:// **name**.github.io/**repository** 形式 の **プロジェクト ウェブサイト**

今回は個人ウェブサイトになるためユーザ ウェブサイトを選択しました.
GitHub に `[username].github.io` の ネーミング・ルールでリポジトリを作成します.
※ username 部分は、自分 の GitHub アカウント名


## Hexo とは？
Hexo は Markdown などで書いた記事から、ブログを生成する 静的 ウェブサイト ジェネレータ です. ([公式ページ](https://hexo.io) では "A fast, simple & powerful blog framework" と フレームワーク謳っているけど、ジェネレータの方がなんとなくしっくりする)

類似のツールとしては Jekyll や Octopress、Pelican などがあります. GitHub Pages では、Jekyll が 公式ツールとなっています.
いろいろな 静的 ウェブサイト ジェネレータ が ある中で、以下のサイトを参考にさせて頂き Hexo に することにしました. たくさんあるツールを確認し、まとめてくださってて助かりました. 感謝感謝です.
- [Static Site Generators](https://staticsitegenerators.net)
- [ブログをHexoにしてみた | task blog](http://task-blog.net/2015/01/25/remove-blog-rails-to-hexo)
- [Github Pagesでブログ構築ができる静的サイトジェネレーター総まとめ - Qiita](http://qiita.com/okmttdhr/items/82ecb0332835472e905f)


## Hexo の インストール
Hexo は Node.js と Git が 必要です. それぞれ以下からダウンロードしてインストールします.
- [Node.js - https://nodejs.org](https://nodejs.org)
- [Git - https://git-scm.com](https://git-scm.com)

Node.js は 開発用途ではないので LTS(Long Term Support) である v6.9.1 LTS を 入れました.

Git は インストール後にユーザ設定を行います. `user.email` と `user.name` は、それぞれ GitHub に 登録している メールアドレス、ユーザ名 で 良いかと思います.
```shell-session
c:\> git config --global user.email "you@example.com"
c:\> git config --global user.name "Your Name"
```


Node.js と Git が 入ったら、コマンド プロンプト から Hexo の モジュールをインストールします.
```shell-session
c:\> npm install hexo-cli -g
...(省略)
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\hexo-cli\node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.15: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```


## Hexo で ウェブサイト の ソース生成
Hexo の モジュールがインストールできたら、ウェブサイトのソースを生成します.
今回はソースの場所を `C:\Develop\repos` とします. また、以降の `[username]` は 自分の GitHub ユーザ名 に 置き換えてください.
```shell-session
c:\> cd C:\Develop\repos
C:\Develop\repos> hexo init [username].github.io
...(省略)
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.15: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
INFO  Start blogging with Hexo!
```

続いて、ウェブサイトのソースに Hexo と Git の デプロイモジュールを追加します.
```shell-session
C:\Develop\repos> cd [username].github.io
C:\Develop\repos\[username].github.io> npm install
C:\Develop\repos\[username].github.io> npm install hexo-deployer-git --save
`-- hexo-deployer-git@0.2.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.15: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```


## Hexo に 最小限 の 初期設定
Hexo の 初期設定を行います. Hexo は `_config.yml` で 設定をします.
テキストエディタなどで `C:\Develop\repos\[username].github.io\_config.yml` を 開きます. 日本語の入力を行う場合は UTF-8 で 保存する必要があります.
以下に主な設定ヶ所を抜粋します.
```yaml
title: [ブログのタイトル]
subtitle: [ブログのサブタイトル、もし必要あれば]
description: [ブログの説明、もし必要あれば]
author: [著者名]
language: en
timezone: Asia/Tokyo

url: https://[username].github.io

deploy:
  type: git
  repo: git@github.com:[username]/[username].github.io.git
  branch: master
```

`language` は 日本語に対応しているテーマを使う場合は `ja` に しますが、デフォルトテーマは対応していないようなので `en` に しました.


## デフォルトのテーマから利用しない他言語を削除
`language: en` に しているのですが、たまに異なる言語でブログが生成されるケースがあるので、利用しない他言語を削除します. 特に問題がない場合は削除する必要はありませんが、`hexo generate` した結果を確認しないでデプロイしたら、ブログ右のタグやカテゴリーが知らない言語になっていたことがあったので、念のため削除することにしました.
`/<ブログ・ソース・ルート>/themes/landscape/languages/default.yml` を 残して、他の言語のファイルを削除します.


## ウェブサイト の 生成 と GitHub Pages へ デプロイ
設定を行ったら、ウェブサイトの生成を行います.
通常は `hexo generate` だけでよいようですが、記事の削除などを行った場合は `hexo clean` を しないと消えてくれないことがあるので、ウェブサイト生成をする場合は `hexo clean` と `hexo generate` を セットで行うようにしています.
```shell-session
C:\Develop\repos\[username].github.io> hexo clean
INFO  Deleted database.

C:\Develop\repos\[username].github.io> hexo generate
INFO  Start processing
INFO  Files loaded in 535 ms
...(省略)
INFO  28 files generated in 2.36 s
```

ウェブサイト生成できたら GitHub へ デプロイします.
```shell-session
C:\Develop\repos\[username].github.io> hexo deploy
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
...(省略)
 * [new branch]      HEAD -> master
INFO  Deploy done: git
```

デプロイ完了後、`https://[username].github.io` で アクセスするとデフォルトの Hello World 記事が見れます.
![](/images/hexo/hexo-3.2-hello-world.png "Hexo")


## ウェブサイト の ソース を GitHub で 管理
ここまで作ったウェブサイトのソースを GitHub で 管理できるようにします. 今回は `source` ブランチで管理することにします.

以下の手順で、ウェブサイトのソースがあるフォルダに Git の リポジトリを作成し、ウェブサイトのある `master` とは関連の無い `source` の ブランチを作成してコミット＆プッシュします.
```shell-session
C:\Develop\repos\[username].github.io> git init
C:\Develop\repos\[username].github.io> git remote add origin git@github.com:[username]/[username].github.io.git

C:\Develop\repos\[username].github.io> git checkout --orphan source

C:\Develop\repos\[username].github.io> git add .
C:\Develop\repos\[username].github.io> git commit -am "Initial commit for Hexo source files"

C:\Develop\repos\[username].github.io> git push origin source
```



- - - -
以上で Hexo と GitHub Pages で ブログ環境が構築できました. 以降は `source` ブランチで記事のソースを管理し、投稿タイミングで `hexo clean`、`hexo generate -d` で `master` ブランチ = ウェブサイト を 更新していきます.

基本的に `source` ブランチしか使わないため GitHub の [Settings] - [Branches] から `source` ブランチをデフォルトにしておくと便利かもしれません.

手軽に記事のソース管理ができそうなので使っていくのが楽しみ.
