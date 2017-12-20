---
title: Hexo に 関連する記事のリストを追加する
date: 2017-06-16
comments: true
categories: ブログ
tags:
  - Hexo
---

![](/assets/hexo/hexo-3.2.png "Hexo")

WordPress などでよく見る「関連記事」の リンク、設置してみたいです. Hexo は 静的サイト・ジェネレーターなので、あらかじめサイトが作られていて訪問いただいた際にデータを蓄積して、それを活用するような仕組みは作れません. JavaScript で 制御できる範囲までです. と、あきらめていたら素晴らしいプラグインを作ってくださっている方がいらっしゃりました！ ということで、さっそく設置してみます.

**作業環境**
- Windows 10
- Hexo 3.3
- Hexo Theme Landscape
- hexo-related-popular-posts


## hexo-related-popular-posts
その素晴らしいプラグインは [hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts)！ 関連する記事だけでなく、人気の切りリストも作れてしまう優れものです. 素敵なプラグインをありがとうございます！

設置方法については、作者さん の 記事 [ブログで関連記事や人気記事を生成するプラグインを作った(node.js製hexo) | TPB](https://tea3.github.io/p/hexo-related-popular-posts/) が すべてを語ってくださっているので、このまま！ という感じですが、機能がたくさんあるので、まずは関連する記事のリストだけに絞って設置しましたので、その記録.


## hexo-related-popular-posts の インストール
`npm` で インストールを実行するだけになります.
```shell-session
C:\Develop\repos\[username].github.io> npm install hexo-related-popular-posts --save
`-- hexo-related-popular-posts@2.0.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```


## Hexo Theme Landscape に 関連する記事 の リスト を 表示
Hexo の デフォルト・テーマ Landscape を カスタマイズします.
テーマ の レイアウト・ファイル `/themes/landscape/layout/_partial/article.ejs` の 表示したい場所に `popular_posts()` を 追加します.
今回は記事の後の [NEWER/OLDER ナビゲーション] と [コメント欄] の 間に配置するため、以下のようにしました.
```ejs
...
</article>

<nav id="related-posts" class="article-inner" style="font-size: smaller">
  <div class="article-entry">
    <h2>関連記事</h2>
    <%-
      popular_posts({ PPMixingRate: 0.0 })
    %>
  </div>
</nav>

<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
  <div id="disqus_thread">
    <noscript>Please enable JavaScript to view the <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
  </div>
</section>
<% } %>
```

4行目 ~ 11行目 の `<nav>` タグで 囲まれている部分が追加したものになります.
`popular_posts()` は、関連する記事を `<ul>` と `<li>` の HTML で 出力してくれます. 「関連記事」のようなタイトルは自前で記述する必要があります.

今回は記事やコメントの枠と合わせたかったので、`<nav class="article-inner">`、`<div class="article-entry">` で 囲むようにしました. この辺はお好みで自由にできるのがいいですね.

続いて 本体の `popular_posts({ PPMixingRate: 0.0 })` の `PPMixingRate: 0.0` ですが、関連記事のみを表示するオプションになります.
hexo-related-popular-posts は 関連記事 と 人気記事 の 両方が扱えます. すごい！ この混合具合を指定することができ、`0.0` が 関連記事のみ、`1.0` が 人気記事のみ と なります.
人気記事の機能を使うには、Google Analytics API の 設定が必要とのことで、今回は関連記事のみとしました. いずれちゃんと設定して、人気記事を表示するか、ブレンドするようにしてみたいと思います.

そのほかのオプションについては、[ヘルパータグの表示オプション](https://tea3.github.io/p/hexo-related-popular-posts/#ヘルパータグの表示オプション) に 詳細が書かれています.



## いざ確認！
`hexo generate` して ローカルサーバで動作確認すると、`<head>` に Google Analytics のためのコードが追加されています.
![](/assets/hexo/hexo-related-popular-posts.png)




- - - -
無事、関連記事が表示されるようになりました. Slack ボット の 記事などは、ボットを いろいろなパターンで作っていたりするので、ご紹介できたらなぁと思っていたので、とても助かります.

静的サイト・ジェネレーターを使っているので関連記事などは難しいかなぁと思っていましたが、素晴らしいプラグインのおかげでつけられました. 作者さま ありがとうございます！
