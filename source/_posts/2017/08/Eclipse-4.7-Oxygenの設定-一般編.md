---
title: Eclipse 4.7 Oxygen の 設定 - 一般編
date: 2017-08-03
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

今回から、Eclipse 4.7 Oxygen の 設定を行っていきます. そのまま使い始めてもよいのですが、ちゃんと設定してあげると より手になじむのでしっかり設定していきたいところです.
各種設定については、お好みがあると思います. ご参考になれば、といったところでしょうか.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/images/eclipse/4.7-oxygen-config/001.png)


### [一般] - [エディター]
- インプレース・システム・エディターの許可: チェックを外す

好みがわかれるところかと思いますが、Eclipse 内で Office ソフトが表示されたりするのは見ずらいと思うので外しています. (もちろん IDE なのだから統合すべしという観点もあるかと思います)
![](/images/eclipse/4.7-oxygen-config/101.png)


### [一般] - [エディター] - [テキスト・エディター]
- タブでスペースを挿入: チェック
- 印刷マージンの表示: チェック
  - 印刷マージン: 120
- 空白文字を表示

タブも好みがわかれるところかと思います. 私はスペース派なので設定しています.
また印刷マージンをいくつにするかも意見が割れるところですね. 最近は広く表示できるので `80` に こだわらなくてもよいかと思いますが、一方でいくつが適切なのかは、正直わからないです... なお [Google の Java Style Guide](https://google.github.io/styleguide/javaguide.html) は "Java code has a column limit of 100 characters. - [4.4 Column limit: 100](https://google.github.io/styleguide/javaguide.html#s4.4-column-limit)" と `100`文字ですね... `100` だと、ちょっと足りない気がしているので `120` に しています. 水平解像度 1920 で Eclipse 内でエディターを 2枚並べられる感じです.
好みというより コーディング規約で しょっ引かれるところだと思うので、ルールに合わせましょう.
![](/images/eclipse/4.7-oxygen-config/102.png)


### [一般] - [エディター] - [テキスト・エディター] - [空白文字を表示] - [可視性の構成]
- 空白 - 先頭: チェックを外す
- 空白 - 囲い: チェックを外す
- 復帰: チェックを外す
- 改行: チェックを外す

エディター内に表示する空白文字のパターンを選択します. OK なものは非表示、NG なものを表示にしました. 表示されていたら対処が必要との観点になるのでわかりやすいかと.
![](/images/eclipse/4.7-oxygen-config/103.png)


### [一般] - [エディター] - [テキスト・エディター] - [スペル]
- スペル・チェックを使用可能にする: チェックを外す

英語、苦手なんでチェックしたいところ. ですが、あまり有効に働いた思い出が無いので外しています.
![](/images/eclipse/4.7-oxygen-config/104.png)


### [一般] - [エディター] - [構造化テキスト・エディター] - [タスク・タグ]
- タスク・タグの検索を使用可能にする: チェック

有効にしておくと `TODO` や `FIXME` `XXX` などを タスク・ビュー表示してくれます. 暫定実装した際などはタグをつけておくと、後で改修がしやすいので便利です. 一方で多用されすぎでリストがあふれるなどの悲しいことも...
![](/images/eclipse/4.7-oxygen-config/105.png)


### [一般] - [ワークスペース]
- テキスト・ファイル・エンコード: UTF-8
- 新規テキスト・ファイルの行区切り文字: Unix

Windows を 使ってはいますが、ソースコードの観点からは `UTF-8` の `LF` が よいと思います. Git の 変換もいらないし.
![](/images/eclipse/4.7-oxygen-config/106.png)


### [一般] - [外観] - [色とフォント]
- テキスト・フォント: Migu 1M 10

開発環境にとってフォントは大事です. 専用のフォントも作られているくらいなので自分に合ったフォントを見つけて設定したいですね.
私は M+とIPAの合成フォント さん の [Migu 1M](http://mix-mplus-ipa.osdn.jp/migu/) を 使わせていただいてます. 素晴らしいフォントをありがとうございます！！
![](/images/eclipse/4.7-oxygen-config/107.png)



- - - -
## Eclipse の 書籍
本記事の 4.7 より古いバージョンとなりますが、Eclipse の 使い方について一通り知るにはよいでしょう. Eclipse は だいぶ枯れているので、大きな違いはないので多少のバージョン違いでも特に問題なく対応できます. (※ Eclipse や IDE を すでに使っている場合には不要です)
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/61UJFDCJZyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Eclipse 4.4 完全攻略 (完全攻略シリーズ)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">石黒 尚久 SBクリエイティブ 2014-10-25    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12960645%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-79-738095-8%2520%257C%25204-797-38095-8%2520%257C%25204-7973-8095-8%2520%257C%25204-79738-095-8%2520%257C%25204-797380-95-8%2520%257C%25204-7973809-5-8" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

## Java の 書籍
IDE は あくまでも道具なので、プログラミングの知識はしっかりつけておきたいところです.
Java 8 までを、しっかり身に着けるには こちらの書籍がよいです. こんな感じで Java 9 も 出してほしいですね.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51741IwOl5L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Java本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">谷本 心,阪本 雄一郎,岡田 拓也,秋葉 誠,村田 賢一郎 技術評論社 2017-04-18    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14782914%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418909-3%2520%257C%25204-774-18909-3%2520%257C%25204-7741-8909-3%2520%257C%25204-77418-909-3%2520%257C%25204-774189-09-3%2520%257C%25204-7741890-9-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
以上、Eclipse の 一般設定でした. 設定は好みがわかれるので難しいですよね.
より良い設定があったら教えていただければと.
