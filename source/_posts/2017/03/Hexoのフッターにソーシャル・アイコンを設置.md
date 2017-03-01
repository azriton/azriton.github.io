---
title: Hexo の フッター に ソーシャル・アイコン を 設置
date: 2017-03-01
comments: true
categories: ブログ
tags:
- Hexo
- GitHub
- Twitter
---

![](/images/hexo/hexo-3.2.png "Hexo")

Hexo の デフォルト・テーマ Landscape に [Twitter の 設定を追加](/2017/02/25/HexoにTwitterのアカウントを設定/)した話では、ソーシャル・アイコンが追加されると早とちりをしてしまいました. 改めて思うと、ソーシャル・アイコンは、やはり どこかに配置しておきたいなぁと思うので、設置してみます.

**作業環境**
- Hexo 3.2
- Disqus
- Twitter


## 設置場所 の 検討
トップのタイトル・カバーやサイド・バーに配置されているケースが多いように感じます. そういった場所に配置しようかと思ったのですが、現在のレイアウトから手ごろな配置が浮かばなかったので、今回はフッターのコピーライトの横あたりにひっそりと置いておくことにします.


## アイコン集 の 検討
アイコン、アイコンはいろいろあるので悩みます. 素敵なデザインのサイトから頂戴しようかと思いましたが、まずは [Font Awesome](http://fontawesome.io/) を 使って設定したいと思います.

Font Awesome は 各種アイコンをフォントとして用意してくれています. フォントなので大きさを変えても崩れませんし、色を変更することもできます. 単色の色設定にはなってしまいますがシンプルなテイストでまとめるとも考えられますし、とても使いやすいツールです.

ライセンスは、[こちら](http://fontawesome.io/license/) で 2017年3月現在 の Version 4.7.0 では、フォント は [SIL OFL 1.1](http://scripts.sil.org/OFL)、CSS などのコードは [MIT](http://opensource.org/licenses/mit-license.html) と、今回のようなブログに設定するにはフリーで使わせていただけます. ただし企業や組織のブランド系のアイコンについては、注意書きがあるので確認およびそれに従った運用が必要です. [Font Awesome の ライセンス](http://fontawesome.io/license/) を ご確認ください.
![](/images/hexo/fontawesome/01.png)

Hexo Landscape は `/themes/landscape/source/css/fonts` に Font Awesome の フォントが入っています. そのため新たに追加しなくても使えるのが嬉しいところです.


## Hexo Landscape に 設置、も？
設置場所、アイコン集も決まったので Hexo Landscape に 配置していきます.
フッターに配置することにしたので、編集するファイルは `/themes/landscape/layout/_partial/footer.ejs` に なります. コピーライトの部分は以下のコードになっており `&copy;` 行 の `<br>` の 前に置くことでコピーライト表示に並べて行けそうです.
```
<div id="footer-info" class="inner">
  &copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %><br>
  <%= __('powered_by') %> <a href="http://hexo.io/" target="_blank">Hexo</a>
</div>
```

続いて、Font Awesome の アイコン ですが、こちらは [Icons](http://fontawesome.io/icons/) ページ から利用したいアイコンをクリックし、表示されたコードをコピペすることで簡単に利用することができます. たとえば Font Awesome の アイコン は `<i class="fa fa-font-awesome" aria-hidden="true"></i>` です.
![](/images/hexo/fontawesome/02.png)

とりあえず GitHub と Twitter を 配置するとして、以下のようなコードを置きました.
必要なソーシャルアイコンのタグを調べて配置して終了！
```html
<div id="footer-info" class="inner">
  &copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>
  <a href="https://github.com/[username]" target="_blank" title="GitHub"><i class="fa fa-github" aria-hidden="true"></i></a>
  <a href="https://twitter.com/[username]" target="_blank" title="Twitter"><i class="fa fa-twitter" aria-hidden="true"></i></a>
  <br>
  <%= __('powered_by') %> <a href="http://hexo.io/" target="_blank">Hexo</a>
</div>
```

いざ表示！と、行きたかったのですが、残念ながら表示されませんでした...
コードは追加されており、配置されている場所こそ気にはなるもののレンダリングもされているようです. しかし、`<i>` の スタイルシート が `user agent stylesheet` で `font-style: italic;` に なっています.
![](/images/hexo/fontawesome/03.png)

なんと、Hexo Landscape には Font Awesome の フォント は 入っているものの、CSS が 入っていない... 読み込んでいないではなく、物理的にファイルもない のでした. そのため、Font Awesome の タグ ではアイコンが入ってくれないという事象が発生したものになります.


## Unicode 指定 による CSS で 設置
タグ による指定できないので、フォントから直接 Unicode で 指定する方法を取りたいと思います.
Unicode による指定は、CSS で `font-family` に Font Awesome の フォントを指定し、`content` に Unicode で 使用する文字を指定します. Unicode は [Icons](http://fontawesome.io/icons/) ページ に コードが書かれているので、そちらを指定します. たとえば Font Awesome の アイコン は `f2b4` なのでエスケープを入れて `content: "\f2b4";` と なります.
![](/images/hexo/fontawesome/04.png)

CSS の クラス定義は、ちょうどソーシャルへシェアするためのアイコンのクラス定義があるので、それに習って作成したいと思います.
ファイルは `/themes/landscape/source/css/_partial/article.styl` で、`$article-share-link` と `.article-share-twitter` などがソーシャル・シェアのアイコンになります. こちらをまねて `$article-link` と `.article-link-twitter`、`.article-link-github` を 作成しました.
フォント・カラー を 直前のコピーライトと合わせ、文字をやや大きくしています. Unicode は Twitter が `f099` で、GitHub が `f09b` なので、それぞれ `content` に 指定しています.
```css
$article-link
  color: #999 !important
  &:before
    font-size: 24px
    font-family: font-icon
    text-align: center
  &:hover
    color: #fff !important
    text-decoration: none !important

.article-link-twitter
  @extend $article-link
  &:before
    content: "\f099"

.article-link-github
  @extend $article-link
  &:before
    content: "\f09b"
```

`/themes/landscape/layout/_partial/footer.ejs` は 先ほどのリンクを作る `<a>` タグ に クラス定義をあてるだけになります.
```html
<div id="footer-info" class="inner">
  &copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>
  <a href="https://twitter.com/[username]" class="article-link-twitter" target="_blank" title="Twitter"></a>
  <a href="https://github.com/[username]" class="article-link-github" target="_blank" title="GitHub"></a>
  <br>
  <%= __('powered_by') %> <a href="http://hexo.io/" target="_blank">Hexo</a>
</div>
```


## いざ、表示！
![](/images/hexo/fontawesome/05.png)


- - - -
無事表示することができました. Font Awesome は よく利用させていただいています. 絵心やデザイン・センスががない身としてはとても助かっています. ありがとうございます！
普段はタグで指定しているので Font Awesome の CSS が 無いケースでの設定は初めてでしたが、ちょうど ソーシャル・シェア の 定義があり流用できたので助かりました.
テーマへの手入れが増えてきたので、そろそろテーマを切り替えて本格的にやっていきたいのですが、なかなか手が出せず、今しばらくは Hexo Landscape に ちょくちょく手を入れつつお世話になりそうです.
