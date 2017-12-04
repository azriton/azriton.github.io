---
title: reveal.js の 右下コントロール・ボタン を 公式デモっぽくする
date: 2017-10-17
comments: true
categories: プレゼン
tags:
- reveal.js
---

![](/images/misc/pptman.png "パワポマン")


[reveal.js を 使って、Markdown で プレゼンテーション・スライド が 作れる](/2017/10/14/reveal.jsでプレゼンテーション・スライドをMarkdownで書く/) ようになりました.
Markdown で 書くので デザインや多様な表現はできないことは承知しているのですが、右下にあるコントロール・ボタンでデザインが気になります. というこうとで、公式のオンライン・デモのコントロール・ボタンっぽくしてみたいと思います.
※ 以降「公式のオンライン・デモ」を「公式デモ」と表記します (ソースに `demo.html` があるので、ここまではオンラインをつけてましたが長いので

**作業環境**
- Windows 10 64bit
- reveal.js 3.5.0


## デザインの違い と 作りたいものの確認
現在の状況について確認します.

まずは、自分で作成したスライドです. 右下のコントロール・ボタンは [ ▶ ] の ような形です.
![](/images/remarkjs/revealjs/05.png)

続いて、公式デモのスライドです. 右下のコントロール・ボタンは [ ＞ ] の ような形です.
![](/images/remarkjs/revealjs/02.png)

この [ ▶ ]を、公式デモの [ ＞ ] に したいというものになります.
あとは、最初のスライドと、下のスライドがある際に、コントロール・ボタンが少し動きます. スライドに動きがあると気になってしまうというのもありますが、ナビゲーションの観点から動いてもいいのかなぁと思います.

今回は、以下の３つを作ります
- 右下のコントロール・ボタンを [ ＞ ] の デザインにする
- 最初のスライドはコントロール・ボタンがわずかに動く
- 下のスライドがある場合は、コントロール・ボタン の [ Ｖ ] と [ ＞ ] を わずかに動く


## 公式デモ の デザイン を 持ってくる
コントロール・ボタンのデザインについて、実は公式デモと配布版では CSS が 異なっています.
特に CONTROLS 以下は完全に違う状態で、これがデザインの [ ▶ ] と [ ＞ ] の 違いになっています.
![](/images/remarkjs/revealjs/06.png)

ということで、公式デモの `CONTROLS` 部分 の CSS を、自分のスライドの HTML `<link>` タグの下へ コピーします.
```html
<style>
.reveal .controls {
  display: none;
  position: absolute;
  top: auto;
  bottom: 12px;
  right: 12px;
  left: auto;
  z-index: 1;
  color: #000;
  pointer-events: none;
  font-size: 10px; }
  .reveal .controls button {
    position: absolute;
    padding: 0;
    background-color: transparent;
    border: 0;
    outline: 0;
    cursor: pointer;
    color: currentColor;
    -webkit-transform: scale(0.9999);
            transform: scale(0.9999);
    transition: color 0.2s ease, opacity 0.2s ease, -webkit-transform 0.2s ease;
    transition: color 0.2s ease, opacity 0.2s ease, transform 0.2s ease;
    z-index: 2;
    pointer-events: auto;
    font-size: inherit;
    visibility: hidden;
    opacity: 0;
    -webkit-appearance: none;
    -webkit-tap-highlight-color: transparent; }
  .reveal .controls .controls-arrow:before,
  .reveal .controls .controls-arrow:after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 2.6em;
    height: 0.5em;
    border-radius: 0.25em;
    background-color: currentColor;
    transition: all 0.15s ease, background-color 0.8s ease;
    -webkit-transform-origin: 0.2em 50%;
            transform-origin: 0.2em 50%;
    will-change: transform; }
  .reveal .controls .controls-arrow {
    position: relative;
    width: 3.6em;
    height: 3.6em; }
    .reveal .controls .controls-arrow:before {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(45deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(45deg); }
    .reveal .controls .controls-arrow:after {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(-45deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(-45deg); }
    .reveal .controls .controls-arrow:hover:before {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(40deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(40deg); }
    .reveal .controls .controls-arrow:hover:after {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(-40deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(-40deg); }
    .reveal .controls .controls-arrow:active:before {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(36deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(36deg); }
    .reveal .controls .controls-arrow:active:after {
      -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(-36deg);
              transform: translateX(0.5em) translateY(1.55em) rotate(-36deg); }
  .reveal .controls .navigate-left {
    right: 6.4em;
    bottom: 3.2em;
    -webkit-transform: translateX(-10px);
            transform: translateX(-10px); }
  .reveal .controls .navigate-right {
    right: 0;
    bottom: 3.2em;
    -webkit-transform: translateX(10px);
            transform: translateX(10px); }
    .reveal .controls .navigate-right .controls-arrow {
      -webkit-transform: rotate(180deg);
              transform: rotate(180deg); }
    .reveal .controls .navigate-right.highlight {
      -webkit-animation: bounce-right 2s 50 both ease-out;
              animation: bounce-right 2s 50 both ease-out; }
  .reveal .controls .navigate-up {
    right: 3.2em;
    bottom: 6.4em;
    -webkit-transform: translateY(-10px);
            transform: translateY(-10px); }
    .reveal .controls .navigate-up .controls-arrow {
      -webkit-transform: rotate(90deg);
              transform: rotate(90deg); }
  .reveal .controls .navigate-down {
    right: 3.2em;
    bottom: 0;
    -webkit-transform: translateY(10px);
            transform: translateY(10px); }
    .reveal .controls .navigate-down .controls-arrow {
      -webkit-transform: rotate(-90deg);
              transform: rotate(-90deg); }
    .reveal .controls .navigate-down.highlight {
      -webkit-animation: bounce-down 2s 50 both ease-out;
              animation: bounce-down 2s 50 both ease-out; }
  .reveal .controls[data-controls-back-arrows="faded"] .navigate-left.enabled,
  .reveal .controls[data-controls-back-arrows="faded"] .navigate-up.enabled {
    opacity: 0.3; }
    .reveal .controls[data-controls-back-arrows="faded"] .navigate-left.enabled:hover,
    .reveal .controls[data-controls-back-arrows="faded"] .navigate-up.enabled:hover {
      opacity: 1; }
  .reveal .controls[data-controls-back-arrows="hidden"] .navigate-left.enabled,
  .reveal .controls[data-controls-back-arrows="hidden"] .navigate-up.enabled {
    opacity: 0;
    visibility: hidden; }
  .reveal .controls .enabled {
    visibility: visible;
    opacity: 0.9;
    cursor: pointer;
    -webkit-transform: none;
            transform: none; }
  .reveal .controls .enabled.fragmented {
    opacity: 0.5; }
  .reveal .controls .enabled:hover,
  .reveal .controls .enabled.fragmented:hover {
    opacity: 1; }

.reveal:not(.has-vertical-slides) .controls .navigate-left {
  bottom: 1.4em;
  right: 6.4em; }

.reveal:not(.has-vertical-slides) .controls .navigate-right {
  bottom: 1.4em;
  right: 1.4em; }

.reveal:not(.has-horizontal-slides) .controls .navigate-up {
  right: 1.4em;
  bottom: 6.4em; }

.reveal:not(.has-horizontal-slides) .controls .navigate-down {
  right: 1.4em;
  bottom: 1.4em; }

.reveal.has-dark-background .controls {
  color: #fff; }

.reveal.has-light-background .controls {
  color: #000; }

.reveal.no-hover .controls .controls-arrow:hover:before,
.reveal.no-hover .controls .controls-arrow:active:before {
  -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(45deg);
          transform: translateX(0.5em) translateY(1.55em) rotate(45deg); }

.reveal.no-hover .controls .controls-arrow:hover:after,
.reveal.no-hover .controls .controls-arrow:active:after {
  -webkit-transform: translateX(0.5em) translateY(1.55em) rotate(-45deg);
          transform: translateX(0.5em) translateY(1.55em) rotate(-45deg); }

@media screen and (min-width: 500px) {
  .reveal .controls[data-controls-layout="edges"] {
    top: 0;
    right: 0;
    bottom: 0;
    left: 0; }
  .reveal .controls[data-controls-layout="edges"] .navigate-left,
  .reveal .controls[data-controls-layout="edges"] .navigate-right,
  .reveal .controls[data-controls-layout="edges"] .navigate-up,
  .reveal .controls[data-controls-layout="edges"] .navigate-down {
    bottom: auto;
    right: auto; }
  .reveal .controls[data-controls-layout="edges"] .navigate-left {
    top: 50%;
    left: 8px; }
  .reveal .controls[data-controls-layout="edges"] .navigate-right {
    top: 50%;
    right: 8px; }
  .reveal .controls[data-controls-layout="edges"] .navigate-up {
    top: 8px;
    left: 50%; }
  .reveal .controls[data-controls-layout="edges"] .navigate-down {
    bottom: 8px;
    left: 50%; } }
</style>
```


## スタイルを適用する要素を追加する
CSS を 持ってきただけでは、コントロール・ボタンは変化しません.
実は(実はが多い...)、コピーしたスタイル `controls-arrow` を 適用するためのタグが必要です. これも公式デモと JavaScript が 異なっている点になります.
![](/images/remarkjs/revealjs/07.png)

さすがに `reveal.js` 本体を変更したくないですし、CDN からとってこれないのも困ります.
なのでイベント・フックを使って HTML が 生成された後に、必要となる要素を追加するようにしました.
以下のコードを HTML の `<script>` タグ にある、 `Reveal.initialize({});` の 下に追加します.
```html
<script>
Reveal.addEventListener('ready', (event) => {
    document.querySelector('aside.controls').dataset.controlsBackArrows = 'faded';
    Array.from(document.querySelectorAll('aside.controls > button'), e => {
        let child = document.createElement('div');
        child.setAttribute('class', 'controls-arrow');
        e.appendChild(child);
        e.style.color = 'rgb(66, 175, 250)';
    });
});
</script>
```

reveal.js で コンテンツの準備ができた `ready` イベントで、以下の処理をしています.

`querySelector('aside.controls')` で、コントロール・ボタンを保持しているコンテナの要素取得します.
データセット `controlsBackArrows` に `faded` を 設定し、戻るボタン(左と上) に ボタンが薄くなるようなエフェクトをつけます.

`querySelectorAll('aside.controls > button')` で、すべてのコントロール・ボタンを取得します.
各ボタンの子要素に `<div class="controls-arrow"></div>` タグをを追加、このタグが先の CSS の 適用先になります.
そして、ボタンのスタイルに `rgb(66, 175, 250)` で カラーを設定します. ボタンの青のカラーが、上記 CSS の コピーでは上手く持ってこれないので、スタイル属性で適用しました.

これで、コントロール・ボタンのスタイルが公式デモのように [ ＞ ] に なりました！
が、よくよくスライドを進めていくと、下スライドがある時に配置がずれてる...
![](/images/remarkjs/revealjs/08.png)


## 配置を直し、ボタンを小さくする
どうも、元の [ ▶ ] の時に配置された CSS が 残っているようで、コピーしただけでは上書きできなかったようです.
再度 HTML に `<style>` を 追加して大きさと配置を修正します.
```html
<style>
  .reveal .controls .controls-arrow:before, .reveal .controls .controls-arrow:after { width: 1.4em; }
  .reveal .controls .navigate-left,  .reveal:not(.has-vertical-slides) .controls .navigate-left  { top: 60px; left: 40px; }
  .reveal .controls .navigate-right, .reveal:not(.has-vertical-slides) .controls .navigate-right { top: 60px; left: 60px; }
  .reveal .controls .navigate-up,    .reveal:not(.has-vertical-slides) .controls .navigate-up    { top: 50px; left: 50px; }
  .reveal .controls .navigate-down,  .reveal:not(.has-vertical-slides) .controls .navigate-down  { top: 70px; left: 50px; }
</style>
```

`.controls-arrow:after { width: 1.4em; }` が ボタンの大きさになります.
その下の４つが、配置です. 大きさに合わせて配置を揃えていきます.

コンパクトになり、配置もそろいました！
![](/images/remarkjs/revealjs/09.png)


## 最初のスライド と 下があるスライド の 場合は、少し動いてナビゲーションする
最初のスライドはよいとしても、メインのコンテンツ以外で動くものがあるのは、如何なものかとは思います. とはいえ縦方向にもスライドができるというは、あまりないのかなと思うと、ナビゲーションのために少し動いてもいいのかなと思い、公式デモから持ってきました.

まずは、少し動くアニメーションのスタイルです. 下矢印が動いて、戻るときに右矢印が動きます.
各％の数値を同じにすると、同時に動きます.
```html
<style>
  @keyframes bounce-right {
    0%, 35%, 50%, 65%, 75% { transform: translateX(0); }
   45% { transform: translateX(2px); }
   55% { transform: translateX(-1px); } }
  @keyframes bounce-down {
    0%, 10%, 25%, 40%, 50% { transform: translateY(2px); }
   20% { transform: translateY(2px); }
   30% { transform: translateY(2px); } }
</style>
```

続いて、最初のスライド と 下があるスライド の 際にアニメーションさせる JavaScript を 作ります.
`Reveal.addEventListener('ready', (event) => {})` は 上記ボタンの子要素を追加したときに記述した部分へ `if(){}` を 追記します.
```html
<script>
    Reveal.addEventListener('ready', (event) => {
        if (Reveal.availableRoutes().right) { document.querySelector('.navigate-right').classList.add('highlight'); }
        if (Reveal.availableRoutes().down) { document.querySelector('.navigate-down').classList.add('highlight'); }
    });

    Reveal.addEventListener('slidechanged', (event) => {
        Array.from(document.querySelectorAll('aside.controls > button.highlight'), e => { e.classList.remove('highlight') });

        if (event.indexh === 0 && event.indexv ===0) {
          document.querySelector('.navigate-right').classList.add('highlight');
        }

        if (Reveal.availableRoutes().down) {
            document.querySelector('.navigate-down').classList.add('highlight');
            if (Reveal.availableRoutes().right) {
                document.querySelector('.navigate-right').classList.add('highlight');
            }
        }
    });
</script>
```

`Reveal.addEventListener('ready', (event) => {});` で reveal.js の 描画が終わった後に、右矢印 と 下矢印 の それぞれが有効な場合に `highlight` の CSS クラスを適用します. `Reveal.availableRoutes()` で 指定する方向のスライドがあるかを判定できます.

`Reveal.addEventListener('slidechanged', (event) => {});` では、スライドが変更された際のイベントで処理ができます.
まずは `highlight` を すべて解除します.

続いて `indexh` と `indexv` が `0` 、つまり最初のスライドの場合に、右矢印に `highlight` を 設定.

そして、下方向がある場合に下矢印に `highlight` し、さらに右方向がある場合は右矢印にも `highlight` を 設定します.


これで、完成になります.
キャプチャは、動きが小さすぎて変化が分かるのが取れませんでした.



- - - -
### マイクロソフト伝説マネジャーの 世界№1プレゼン術
日本マイクロソフト株式会社 の 澤さん の プレゼンに関する書籍です. マイクロソフトテクノロジーセンター の センター長 を 勤め、マイクロソフト社のエバンジェリストを率いられています.
こちらの書籍は、プレゼンのためのテクニックというよりも、なぜプレゼンをするのか、なぜプレゼンが必要なのかという点から書かれています. ここでの「プレゼン」は 大人数を前にしたようなプレゼンではなく、営業だとか、自分の考えを伝えるためだとかにも当てはまり、ビジネス・スキルとしても役立ちます.
プレゼンを作る際にはいつも読み返す、基本の立ち位置として重宝しています.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4478101299" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/515pI-Y9icL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4478101299" target="_blank" >マイクロソフト伝説マネジャーの 世界№1プレゼン術</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">澤 円 ダイヤモンド社 2017-08-24    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4478101299" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB074FS2QLZ%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F15045317%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>         <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-47-810129-2%2520%257C%25204-478-10129-2%2520%257C%25204-4781-0129-2%2520%257C%25204-47810-129-2%2520%257C%25204-478101-29-2%2520%257C%25204-4781012-9-2" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

### プレゼンの極意はマンガに学べ
漫画家の三田紀房さん の 書籍です. <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB074CGP8L1" target="_blank" >ドラゴン桜</a> や <a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB074CF4N1Y" target="_blank" >エンゼルバンク</a> などが有名ですが、ビジネス関係の書籍も出されています.
こちらは、その中でもプレゼンに関する書籍で、プレゼンを一つの物語として考えてみようというものになります. 一般的なプレゼンの書籍とは一線を画すので、好みが分かれるところだと思います. ご自身の漫画を例に、どのようにスライドを作るのかについて書かれています.
プレゼンだけではなく、アイデアの練り方などにもふれているのがよく、たまに読み返しては様々なことに思いを巡らせたりしています.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4062181991" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51XfxsRHPTL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4062181991" target="_blank" >プレゼンの極意はマンガに学べ</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">三田 紀房 講談社 2013-02-22    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4062181991" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB00DW4ZYB6%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12152184%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-06-218199-0%2520%257C%25204-062-18199-0%2520%257C%25204-0621-8199-0%2520%257C%25204-06218-199-0%2520%257C%25204-062181-99-0%2520%257C%25204-0621819-9-0" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
デザイン・センスとか、スキルがないのに、どうしてもスライドのデザインとか気になってしまうんですよね... 絵が描けたりする人がうらやましいです.
今回は公式デモを真似するので、CSS を 上手く当てはめることでできましたが、JS や CSS 本体にデモ用の改変が入っていたので、HTML 側だけで合わせるのが難しかったです. 上手くできてよかった.
