---
title: CircleCI で Hexo の 自動ビルド と デプロイ設定
date: 2016-12-08
comments: true
categories: ブログ
tags:
- Hexo
- Git
- GitHub
- CircleCI
---

![](/images/circleci/circleci.png "CircleCI")

[CircleCI の アカウント作成](/2016/12/05/CircleCIのアカウント作成/) が できたので、ブログ生成に使っている Hexo の 自動ビルドとデプロイができるようします.

**作業環境**
- CircleCI
- Node.js 6.9.1 LTS
- Hexo 3.2


## CircleCI の 設定を追加
ソース・ルート直下に `circle.yml` を 配置することで CircleCI の 設定を行います.
```yaml
general:
  branches:
    only:
      - source

machine:
  timezone: Asia/Tokyo
  node:
    version: 6.9.1

test:
  override:
    - echo "skip test"

deployment:
  deploy:
    branch: source
    commands:
      - git config --global user.name "CircleCI"
      - git config --global user.email [your e-mail]
      - hexo clean
      - hexo generate
      - hexo deploy
```

`general - branches - only` で `source` ブランチだけビルドするようにしています.
指定方法の詳細は、こちらの [Specifying branches to build](https://circleci.com/docs/configuration/#branches) に なります. ビルドするブランチを指定する以外に、特定のブランチを無視したり、正規表現で指定できます.

`test - override - echo "skip test"` は、CircleCI が テストしないとビルドに失敗するようになっているので、テストがないことをコンソール出力するために設定しています.

`git config --global` で Git の ユーザ設定を追加します. `user.name` は CircleCI の ビルドであることが分かるようにしました. `user.email` は GitHub に 登録している自分のメールアドレスを指定しました.

`circle.yml` ファイルができたら、GitHub へ コミットしておきます.


## CircleCI に リポジトリを追加
CircleCI の [プロジェクト追加](https://circleci.com/add-projects) ページ へ 行きます.
自分の GitHub アカウントが表示されているのでクリックし、右側のプロジェクト一覧から GitHub Pages の `[username].github.io` プロジェクト 右 の [Build project] ボタンをクリックします.
![](/images/circleci/add/01.png)

自動的にビルドが始まり、ビルド画面へ遷移します. しばらく待っているとビルドが失敗し、レッド の [FAILED] 表示がされます.
これは Hexo の デプロイ で GitHub へのアクセス権がなかったために起こるものなので、SSH キー を 追加します. このあたりの設定は [GitHub security and SSH keys - CircleCI](https://circleci.com/docs/github-security-ssh-keys) に 説明があります.
画面の上部中央にある [Project Settings] を クリックします.
![](/images/circleci/add/02.png)

プロジェクト設定画面が表示されるので、左側のメニューを下へスクロールし [Checkout SSH Keys] を クリックします. 右側に設定内容が出てくるので、[Authorize with GitHub] ボタンをクリックします.
![](/images/circleci/add/03.png)

GitHub の 画面が表示され、CircleCI へ Public SSH keys の 権限を許可するかを確認されます. 許可しないと始まらないのですが、問題ある場合は ここで止めて CircleCI の 自動デプロイをあきらめます.
![](/images/circleci/add/04.png)

上記画面で [Authorize application] ボタンをクリックすると CircleCI の 画面に戻ります.
右側の設定から [Create and add [username] user key] ボタンをクリックし、SSH キー を GitHub へ 追加します.
![](/images/circleci/add/05.png)

ボタンをクリックすると設定画面に [[username] user key] が 追加されているのを確認し、画面上部 の [View [username].github.io] リンクをクリックします.
![](/images/circleci/add/06.png)

前回のビルドが失敗している履歴が表示されるので、[rebuild] を クリックします.
![](/images/circleci/add/07.png)

自動的にビルドが始まり、ビルド画面へ遷移します. しばらく待っていると今度はビルドが成功し、グリーン の [FIXED] が 表示されます.
![](/images/circleci/add/08.png)



- - - -
これで、無事に自動ビルドとデプロイができるようになりました. 以降は `source` ブランチ へ プッシュするたびに自動でビルドが行われ、デプロイもされるようになります.
ローカルでの確認は必要ですが `hexo deploy` 無しで、自動的に記事が公開できるので便利ですね.
