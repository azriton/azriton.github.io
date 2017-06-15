# azriton.github.io
GitHub Pages [https://azriton.github.io](https://azriton.github.io) の ソースコードです.
- `master` ブランチ: GitHub Pages で ホストされるウェブサイトのソース
- `source` ブランチ: 静的ジェネレーター用のソースコード管理
※ Orphan で 作成しているため `master` と `source` は 関連がない

## 更新のポリシー
- Hexo および blog の 設定 と 投稿 は 別で Issues と commit する
- GitHub Issues の 登録
    - 設定系は 英語のタイトル (本文は日本語可)
    - 投稿は `投稿: ` を 先頭につけて、投稿のタイトルと合わせる(日本語可)
    - 投稿の更新は `改訂: ` を 先頭につけ、タイトル略称と更新サマリ(日本語可)

## ブログ記事 の カテゴリー と タグ
- カテゴリー
    - なるべく１階層にし、階層を深くしないようにする
    - 一般名称を付けるようにし、固有名称はタグにする
- タグ
    - なるべく正式名称を付け、短縮名などは避ける
    - 一連の記事以外でも使用するものから抽出し、一回限りなどを増やさない
    - 新しいタグを作った場合は、過去の記事にも付与するべきか見直しをする

## 環境構築 の 手順
- azriton.github.io で Private & Initialize で リポジトリを作成
- コマンドプロンプトで以下を実行
```shell-session
set USER=azriton
set NAME=Azriton
set MAIL=agent.azriton@gmail.com
set SITE=%USER%.github.io

npm install hexo-cli -g

cd %TEMP%
hexo init %SITE%

cd %SITE%
npm install
npm install hexo-deployer-git --save

git init
git checkout --orphan source
git remote add origin git@github.com:%USER%/%SITE%

git config --local user.name %NAME%
git config --local user.email %MAIL%

echo # %SITE%>README.md
git add README.md
git commit -m "Initial commit"
git push origin source

git add .
git commit -m "Initial commit for this PJ"
git push origin source

cd ..
rd /s /q %SITE%
```

- GitHub の [Settings] - [Branches] から Default branch を `source` にする
- Eclipse から リポジトリ を `clone` し プロジェクトのインポート
- コマンドプロンプトで以下を実行
```shell-session
cd c:\develop\repos\personal\azriton.github.io
npm install

git config --local user.name Azriton
git config --local user.email agent.azriton@gmail.com
```

- Eclipse Mylyn に GitHub Isses の タスクリポジトリを追加
- 以下のタスクを登録し、順次コミット
  - Add Eclipse project settings for writing environment
  - Set initial configuration of blog in Hexo
  - Add feed generator of Atom and PubSubHubbub
  - Add sitemap generator for search engine
  - Add related posts navigation
  - Open Disqus comment field
  - Customize font settings for default theme
  - Customize blockquote style for default theme
  - Make banner and footer settings the default theme
  - Set up Google Analytics integration for default theme
  - Add Twitter Cards setting to default theme
  - Remove unused language file from default theme
  - Revise the template of new posts
  - Change file path of posts for simplify file management
  - Remove sample article "Hello"
  - Add CicleCI integration for automatic deployment
  - Write initial construction procedure in README.md
  - 投稿: ...(最初の記事のタイトル)
