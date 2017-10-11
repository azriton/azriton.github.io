---
title: Hexo に 404 Not Found ページ を 追加する
date: 2017-10-11
comments: true
categories: ブログ
tags:
  - Hexo
  - GitHub
---

![](/images/hexo/hexo-3.2.png "Hexo")

しばらく前にブログのソース・ファイルの名前を変更したことがありました. その際に関連するリンクの修正を忘れていたものが多かったらしく、久しぶりに見た Google Search Console で 23件もの 404 Not Found、ページが見つからないというエラーが報告されていました. リンク切れを直すとともに、404 エラー が 発生した場合に表示されるページを GitHub Pages に なっていたものを、ブログ用のページに直しました. 404 ページを設置にあたっては、Hexo で Markdown を 書き、GitHub Pages に 設定します.

**作業環境**
- Windows 10
- Hexo 3.3
- GitHub Pages


## 設定していない場合の状況
404 ページ を 設定していない場合は、存在しない URL へ アクセスすると以下のようなページが表示されます.
ウェブサイトを GitHub さん の GitHub Pages で ホストさせていただいているので、何もしなければ GitHub さん の 404 ページになります. 何を表示してよいのかわからないから、当然そうなりますね.
![](/images/hexo/404/01.png)

このページが表示されると、来ていただいた方にトップ・ページへさえも、戻ってもらうための手段が提供できないという問題があります.
内部のリンクをたどってきてくださった方は戻ることができますが、直接来た方は URL を 修正して、、、までは見ないですよね. (たぶん)

内部リンク切れは修正するとしても、404 が 表示される可能性は残ります.
その場合に備えて、404 ページ自体も自サイトのものにします.


## GitHub Pages で 404 ページ を 設定
GitHub Pages で 404 ページを設定するには、サイトのソース・ルートに `404.html` を 置きます.
下記例は Hexo を 使っていない場合に、単純な `404.html` を 置いたものになります. ホームへのリンクなどを設置していない簡単なものですが、独自のページになっていることが分かります.
![](/images/hexo/404/02.png)


## Hexo で 404 ページ を 設定
Hexo を 使って ウェブサイトを作っている場合は、Hexo の Markdown で 404 ページを作ります.
ソースルート直下の `source` ディレクトリに `404.md` を 作ります.
```markdown
---
layout: page
title: Page not found
comments: false
---

ページが見つかりませんでした.
```

アクセスすると以下のようなページが表示されます.
タイトールと、コンテンツがちゃんと表示されているのが分かります. そして カバーの画像 や カテゴリー など、サイトの構成もそのままなので、トップへ戻ったり、カテゴリーやタグを選びなおすなどの選択ができるようになります.
![](/images/hexo/404/03.png)

なお、 `404.html` を 直接置いた場合ですが、これは Hexo に 取り込まれコンテンツ部分に埋め込まれるので、完全に別ページを作るということはできなそうです.
![](/images/hexo/404/99.png)


## 日付とか、Share とか、関連記事とか、とか、
サイトの構成にうまく入り込んでくれたおかげで、すべてを作りこむ必要がなくなったのが助かります. 一方で 日付 や Share の リンク、関連記事 などが表示され、記事の１つのような感じが出ています. (※ 関連記事は、こちら [Hexo に 関連する記事のリストを追加する](/2017/06/16/Hexoに関連する記事のリストを追加する/) で 追加した Plugin に なります)
これを非表示にするにはテンプレートを修正する必要があります.
今回はデフォルト・テンプレート Landscape をつかっているので、Landscape を カスタマイズしました.

修正するファイルは `/themes/landscape/layout/_partial/article.ejs` です.
以下の３ヵ所を `<% if (post.path != "404.html"){ %>` - `<% } %>` で 囲みます.
- `<div class="article-meta">` ブロック
- `<footer class="article-footer">` ブロック
- `<nav id="related-posts"...>` ブロック

以下、 `article.ejs` 修正箇所 の 抜粋
```ejs
<article id="<%= post.layout %>-<%= post.slug %>" class="article article-type-<%= post.layout %>" itemscope itemprop="blogPost">
  <% if (post.path != "404.html"){ %>
    <div class="article-meta">
      <%- partial('post/date', {class_name: 'article-date', date_format: null}) %>
      <%- partial('post/category') %>
    </div>
  <% } %>
  <div class="article-inner">
    ...(省略)
    <% if (post.path != "404.html"){ %>
      <footer class="article-footer">
        <a data-url="<%- post.permalink %>" data-id="<%= post._id %>" class="article-share-link"><%= __('share') %></a>
        <% if (post.comments && config.disqus_shortname){ %>
          <a href="<%- post.permalink %>#disqus_thread" class="article-comment-link"><%= __('comment') %></a>
        <% } %>
        <%- partial('post/tag') %>
      </footer>
    <% } %>
    ...(省略)
</article>

<% if (post.path != "404.html"){ %>
  <nav id="related-posts" class="article-inner" style="font-size: smaller">
    <div class="article-entry">
      <h2>関連記事</h2>
      <%-
        popular_posts({ PPMixingRate: 0.0 })
      %>
    </div>
  </nav>
<% } %>
...(省略)
```

テンプレートを適用し、若干文章を直して、こんな感じになりました.
![](/images/hexo/404/04.png)



- - - -
無事、404 エラー に対して コンテンツを返せるようになりました.
内部リンク切れで 404 は 出してはいけないものなので反省しつつ、何かの拍子で出てしまってもサイト内へとどまっていただけるようにできたかと思います.
いつかは、こちら [デザインの参考にしたい！404（not found）ページ33選](https://liginc.co.jp/president/archives/5567) で 紹介されるような、ステキな 404 ページを作ってみたいものです.
