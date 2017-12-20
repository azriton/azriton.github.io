---
title: Eclipse 4.7 Oxygen の 設定 - JavaScript編
date: 2017-08-09
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/assets/eclipse/4.7-oxygen.png "Eclipse Oxygen")

今回は、Eclipse 4.7 Oxygen の JavaScript に 関する設定を行っていきます.
引き続き各種設定については、お好みがあると思います. ご参考になれば.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、いつも通り Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/assets/eclipse/4.7-oxygen-config/001.png)


### [JavaScript] - [エディター] - [入力]
- セミコロン: チェック
- 波括弧: チェック
- 文字列リテラルへの貼り付け時にテキストをエスケープ: チェック

なるべく自動でやってもらった方が楽なので...
![](/assets/eclipse/4.7-oxygen-config/301.png)


### [JavaScript] - [エディター] - [保管アクション]
- 保管時に選択したアクションを実行: チェック
- 追加アクション: チェック
- [構成] ボタンをクリックし、以下を追加
  - コード・スタイル - if/while/for/do ステートメントでブロックを使用 - 常時
  - コード・スタイル - 条件を括弧で囲む - 必要な場合のみ
  - 不要なコード - 未使用のローカル変数を除去
  - 不要なコード - 不要な '$NON-NLS$' タグを除去
  - コード編成 - 末尾の空白を除去 - 全ての行

こちらも Java編 同様、処理を自動化して楽になるように設定します.
![](/assets/eclipse/4.7-oxygen-config/302.png)


### [JavaScript] - [コードスタイル]
- 新機関数と型のコメントを自動的に追加: チェック

JSDoc の テンプレートが自動で入ってくれるので便利です.
![](/assets/eclipse/4.7-oxygen-config/303.png)


### [JavaScript] - [コードスタイル] - [フォーマッター]
[新規] ボタンをクリック
![](/assets/eclipse/4.7-oxygen-config/304.png)


### [JavaScript] - [コードスタイル] - [フォーマッター] - [新規プロファイル]
プロファイル名に任意の名称 (ここでは Formatter) を 入力し、[OK] ボタンをクリック
![](/assets/eclipse/4.7-oxygen-config/305.png)


### [JavaScript] - [コードスタイル] - [フォーマッター] - [Formatter プロファイル]
- タブ・ポリシー: スペースのみ
- インデント・サイズ: 2

Java エディター の タブ設定同様で、ここで設定しないとタブが入力されます. 要注意です. またインデント・サイズ は [Google の JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html) の "Each time a new block or block-like construct is opened, the indent increases by two spaces. - [4.2 Block indentation: +2 spaces](https://google.github.io/styleguide/jsguide.html#formatting-block-indentation)" に 習って `2` にしました.
![](/assets/eclipse/4.7-oxygen-config/306.png)


### [JavaScript] - [バリデーター] - [JSDoc]
- 誤った形式の Jsdoc コメント: 警告
  - タグ内のエラーをレポート: チェック
- 未指定の Jsdoc タグ: 警告
- 未指定の Jsdoc コメント: 警告

厳しく設定します.
![](/assets/eclipse/4.7-oxygen-config/307.png)


### [JavaScript] - [バリデーター] - [エラー/警告]
- JavaScript セマンティクス検証を使用可能にする: チェック
- 次の JavaScript バリデーター・オプションの問題重大度レベルを選択: 以下を除き すべて `警告`
  - 外部化されていないストリング: 無視

厳しく設定します. この手のものは習慣なので、厳しく設定したとしても習慣化されれば気にもならなくなるので、むしろ設定しておいた方が楽ですね.
![](/assets/eclipse/4.7-oxygen-config/308.png)


### [JSON] - [JSON ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
  - インデント・サイズ: 2

JSON は データの表現なので行の幅を縛るより、ありのままで表示してほしいので 行の幅 を `999` に しました. またインデント・サイズは JavaScript の コードに合わせて `2` に しています.
![](/assets/eclipse/4.7-oxygen-config/309.png)


### [JSON] - [JSON ファイル] - [検証]
- 構文検証を使用可能にする: チェック
- スキーマ検証を有効にする: チェック

チェックはしてもらった方が良いので、有効にします.
![](/assets/eclipse/4.7-oxygen-config/310.png)



- - - -
## Eclipse の 書籍
本記事の 4.7 より古いバージョンとなりますが、Eclipse の 使い方について一通り知るにはよいでしょう. Eclipse は だいぶ枯れているので、大きな違いはないので多少のバージョン違いでも特に問題なく対応できます. (※ Eclipse や IDE を すでに使っている場合には不要です)
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/61UJFDCJZyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Eclipse 4.4 完全攻略 (完全攻略シリーズ)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">石黒 尚久 SBクリエイティブ 2014-10-25    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12960645%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-79-738095-8%2520%257C%25204-797-38095-8%2520%257C%25204-7973-8095-8%2520%257C%25204-79738-095-8%2520%257C%25204-797380-95-8%2520%257C%25204-7973809-5-8" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

## Java の 書籍
IDE は あくまでも道具なので、プログラミングの知識はしっかりつけておきたいところです.
Java 8 までを、しっかり身に着けるには こちらの書籍がよいです. こんな感じで Java 9 も 出してほしいですね.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51741IwOl5L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Java本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">谷本 心,阪本 雄一郎,岡田 拓也,秋葉 誠,村田 賢一郎 技術評論社 2017-04-18    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14782914%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418909-3%2520%257C%25204-774-18909-3%2520%257C%25204-7741-8909-3%2520%257C%25204-77418-909-3%2520%257C%25204-774189-09-3%2520%257C%25204-7741890-9-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
Java 程ではありませんでしたが、設定項目は多めでした. だいぶ設定ができてきました.
