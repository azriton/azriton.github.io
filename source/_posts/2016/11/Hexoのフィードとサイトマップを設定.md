---
title: Hexo の フィード と サイトマップ を 設定
date: 2016-11-07
comments: true
categories: ブログ
tags:
  - Hexo
---

![](/assets/hexo/hexo-3.2.png "Hexo")

Hexo の トップ画面の右上にはフィードのアイコンとリングが配置されています. インストール直後では、機能しておらずクリックすると ステータスコード "404 Not Found" で "Cannot GET /atom.xml" の 文字列が画面に表示されてしまいます.

カッコ悪いのでフィードが出力されるようにし、合わせてサイトマップも出せるようにしたいと思います.

**作業環境**
- Windows 7
- Hexo 3.2


## フィードの出力
Hexo の [Plugin リスト](https://hexo.io/plugins/) から フィード関連 の Plugin を 探すと オフィシャル の [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) が あります. こちらを設定します.

Hexo の ソースがあるフォルダで `npm install hexo-generator-feed --save` を実行します. (下記例の [username] は 自分の GitHub ユーザ名)
また、実行前にアップデートも行っておくとよいでしょう.
```shell-session
C:\Develop\repos\[username].github.io> npm update
C:\Develop\repos\[username].github.io> npm install hexo-generator-feed --save
`-- hexo-generator-feed@1.2.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.15: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```

続いて HEXO の 設定ファイルである `_config.yml` に フィード出力の設定を追加します.
Atom と RSS2 の どちらかが出力できるようで、今回は Atom を 選択しました. また出力数は `limit` で 設定できます.
```yaml
## hexo-generator-feed (https://github.com/hexojs/hexo-generator-feed)
feed:
  type: atom
  path: atom.xml
  limit: 20
```

設定後 `hexo generate` すると、無事にフィードが生成されていることが確認できます. 確認できたらデプロイします.
![](/assets/hexo/hexo-feed.png)


## サイトマップの出力
同じく Hexo の [Plugin リスト](https://hexo.io/plugins/) から サイトマップ関連 の Plugin を 探すと オフィシャル の [hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap) が あります. もうひとつ hexo-generator-seo-friendly-sitemap が ありますが、とりあえずオフィシャルのものを設定します.
機会があったら違いなどを調べたいですが、まずは今の環境構築を優先で.

こちらもフィードと同様に Hexo の ソースがあるフォルダで `npm install hexo-generator-sitemap --save` を実行します.
```shell-session
C:\Develop\repos\[username].github.io> npm update
C:\Develop\repos\[username].github.io> npm install hexo-generator-sitemap --save
`-- hexo-generator-sitemap@1.1.2
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.15: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```

`_config.yml` に サイトマップ出力の設定を追加します.
```yaml
## hexo-generator-sitemap (https://github.com/hexojs/hexo-generator-sitemap)
sitemap:
  path: sitemap.xml
```

設定後 `hexo generate` すると、無事にフィードが生成されていることが確認できます. 確認できたらデプロイします.
![](/assets/hexo/hexo-sitemap.png)



- - - -
オフィシャル で Plugin が 用意されており、また Node.js の npm で 簡単に導入することができました.
`hexo-generator-feed` で PubSubHubbub による通知もできるようなので、こっちも設定しておきたいでが「それはまた、別の話」にて.
→ [Hexo で PubSubHubbub 通知をする](/2017/01/06/HexoでPubSubHubbub通知をする/) の 記事を追加しました. (2017年1月6日追記）