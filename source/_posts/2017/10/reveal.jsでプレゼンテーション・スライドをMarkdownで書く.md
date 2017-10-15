---
title: reveal.js で プレゼンテーション・スライド を Markdown で 書く
date: 2017-10-14
comments: true
categories: プレゼン
tags:
- reveal.js
---

![](/images/misc/pptman.png "パワポマン")

プレゼンテーションや発表などで、お話をする際にはプレゼンテーション・スライドを用意するかと思います. 普段 PowerPoint を使って作るケースが多いのですが、最近はコーディングな仕事をしているので、せっかくだから Markdown で スライドづくりをしてみたいと思います.

**作業環境**
- Windows 10 64bit
- reveal.js 3.5.0
- Node.js 8.4.0 64bit
- Browsersync 2.18.13


## reveal.js って？
論より何とかではないですが、まずは [公式デモ](http://lab.hakim.se/reveal-js/) を ざっくり動かしてみるとよいと思います.  プレゼンテーション・スライド風なページが表示されます. キーボードの [→] ボタンを押下ないし、右下のコントロールから [＞] を クリックするとスライドして次のページが表示されます.
![](/images/remarkjs/revealjs/01.png)

少し進んでいくと右下のコントロールに「ｖ」が 表示され、下へもスライドできます.
![](/images/remarkjs/revealjs/02.png)

なんと、これらを Markdown で 書くことができ、Web で 公開までもできるのが、reveal.js に なります.
基本的な機能どころか、発表でも十分使える機能があります. 以下に主な機能を抜粋します.
- [ESC] で スライドマップ(slide overview) を 表示、縦横があるのでいいかも
- [Alt + クリック] で ズーム、画像やコードを拡大したいときに便利です
- FRAGMENTS スライド、コンテンツを順々に追加するようなスライドも作れます
- スライド遷移のスタイル も [None - Fade - Slide - Convex - Concave - Zoom] から 選択可能
- テーマも多数 [Black (default) - White - League - Sky - Beige - Simple - Serif - Blood - Night - Moon - Solarized]
- 背景も画像やビデオにでき、別途遷移スタイルも適用できます
- スライド作成に必要な、リスト表示やテーブル、引用、シンタックス・ハイライトされたコード表示、スライド間リンクなどもできます
- 画像もちゃんと扱えます (**が、レイアウトはできないので上から順に並べるだけ**)
- PDF 出力もできるので印刷して配布などもできます (Google Chrome の 印刷経由)
- 発表者ツールがあるので、発表時も安心です

発表時は、[S] キー を 発表者ツールを表示し、スライド側をプロジェクタ等に移動して [F] で フルスクリーン表示します.
![](/images/remarkjs/revealjs/03.png)


## HTML ファイル の 作成
まずは、reveal.js を 使う HTML を 作成します. 大枠としては以下のような感じになります.
```html
<!doctype html>
<meta charset="utf-8">
<title>Presentation</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/css/reveal.min.css" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/css/theme/white.min.css" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/lib/css/zenburn.min.css" />

<script>
    let link = document.createElement('link');
    link.rel = 'stylesheet';
    link.type = 'text/css';
    link.href = window.location.search.match(/print-pdf/gi) ?
             'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/css/print/pdf.min.css' :
             'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/css/print/paper.min.css';
    document.getElementsByTagName('head')[0].appendChild(link);
</script>

<div class="reveal">
    <div class="slides">
            <section data-markdown="contents.md"
                     data-separator="^\n\n---$"
                     data-separator-vertical="^\n>>>$"
                     data-notes="^Note:"
                     data-charset="utf-8">
            </section>
    </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/lib/js/head.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/js/reveal.min.js"></script>
<script>
    Reveal.initialize({
        dependencies: [
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/lib/js/classList.js', condition: function() { return !document.body.classList; }},
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/plugin/markdown/marked.js', condition: function() { return !!document.querySelector( '[data-markdown]' ); }},
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/plugin/markdown/markdown.min.js', condition: function() { return !!document.querySelector( '[data-markdown]' ); } },
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/plugin/notes/notes.min.js', async: true },
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/plugin/zoom-js/zoom.min.js', async: true },
            { src: 'https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.5.0/plugin/highlight/highlight.min.js', async: true, callback: function () { hljs.initHighlightingOnLoad(); }}
        ]
    });
</script>
```

JavaScript や CSS は CDN の [cdnjs.com](https://cdnjs.com/) さん から配信いただくようにしました.

テーマは 白ベースの `white.min.css` に しました. テーマは公式デモの [THEMES](http://lab.hakim.se/reveal-js/#/themes) で試すことができます.

`<div class="slides">` 配下の `<section>` が Markdown ファイルの読み込みと、書式の定義になります.
- `data-markdown` は、読み込む Markdown ファイル名です.
- `data-separator` は、スライドの区切りを示す正規表現です. 今回は改行２つの後に `---` で次のスライドになります.
- `data-separator-vertical` は、垂直方向スライドの区切り正規表現です. 今回は改行１つの後に `>>>` にしました.
- `data-notes` は、発表者ノートのパートを表す正規表現です. 行頭から `Note:` になります.
- `data-charset` は、Markdown ファイルの文字セットです.


## Markdown ファイル の 作成
`<section data-markdown="contents.md"... >` で 指定したファイルを作成します.
```markdown
# 最初のページ


---
## ２枚目のページ
- 起: 事実や出来事を述べる
- 承: 『起』で述べたことに関することを述べる
- 転: 『起承』とは関係のない別のことがらを持ち出す
- 結: 全体を関連づけてしめくくる


---
## ３枚目のページ

ここに本文を書いていく
Markdown を 使うことができる

>>>
### ３枚目のページ の 下

下のページ
左右の流れと、上下の流れ が作れる


---
## ４枚目のページ

Note: これは発表者ノート
```


## 表示 と 快適なエディティング の ために Browsersync
ここまででスライドづくりは完了ですが、外部ファイル の Markdown で スライド作成をすると、表示するには HTTP サーバー が 必要となります. HTML 内に書いてしまうという手もあるのですが、Markdown は 別にしておきたいので HTTP サーバー を 立てる方向で考えました.

[Browsersync](https://www.browsersync.io/) は ローカル の HTTP サーバーとして動作し、ファイルの変更を検出したらブラウザをリロードしてくれるツールになります.
```console
c:\Develop\repos\slack-bot> npm install -g browser-sync
c:\Develop\repos\slack-bot> browser-sync start --server --files **/*
[Browsersync] Access URLs:
 -----------------------------------
       Local: http://localhost:3000
    External: http://192.168.0.100:3000
 -----------------------------------
          UI: http://localhost:3001
 UI External: http://192.168.0.100:3001
 -----------------------------------
[Browsersync] Serving files from: ./
[Browsersync] Watching files...
```

Browsersync が 起動すると、ブラウザで自動的にページが表示されます.
スライド・マップを見ると単純な例ですが、ちゃんと上下のスライドも使えています.
![](/images/remarkjs/revealjs/04.png)


## パワポマン！
スライド作りとなると、どうしても欲しくなるのが **パワポマン** こと、パワーポイントの人型のアイコン、本投稿のアイキャッチにも使っているクリップアートさんたち. 以前はクリップアートの検索で、**アバター** とか **Style 1541** などで検索できていましたが、いつの間にかなくなってしまったというのがありました.

それについては、こちら [いつか消えた PPT の アバター とか、 Style 1541 とか](/2017/03/25/いつか消えたPPTのアバターとか、Style-1541とか/) に まとめましたので、よろしいかったら ご覧ください.

なお、クリップアート自体は [マイクロソフト クリップアート 復刻](http://msclipart.blogspot.jp/) さん から入手できます.
パワポマンはいつもお世話になっております. 復刻してくださり ありがとうございます！



- - - -
スライドづくりの環境準備ができました！
Markdown で 書くので、パワポに比べて表現力は落ちるでしょうが、しばらくはキレイで派手なスライドから離れるので、サクサク書ける Markdown のが嬉しいです. まずは、reveal.js で ドンドン書いてみよう♪
