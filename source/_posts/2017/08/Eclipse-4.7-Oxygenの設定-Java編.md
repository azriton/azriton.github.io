---
title: Eclipse 4.7 Oxygen の 設定 - Java編
date: 2017-08-06
updated: 2017-08-06
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/assets/eclipse/4.7-oxygen.png "Eclipse Oxygen")

今回は、Eclipse 4.7 Oxygen の Java に 関する設定を行っていきます. 今回はかなり長いです. もうちょっとデフォルト設定と気が合えばよいのですが、ついつい設定したくなってしまう...
引き続き各種設定については、お好みがあると思います. ご参考になれば.


**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、いつも通り Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/assets/eclipse/4.7-oxygen-config/001.png)


### [Java] - [インストール済みの JRE]
Java SE Development Kit(JDK) の パスになっているかを確認します.
JRE の場合、Java 標準ライブラリーのソースが参照できないため開発に支障があるので、追加してチェックをつけます.
![](/assets/eclipse/4.7-oxygen-config/201.png)


### [Java] - [エディター] - [入力]
- 訂正位置で自動的に挿入 - セミコロン: チェック

なるべく自動でやってもらった方が楽なので...
![](/assets/eclipse/4.7-oxygen-config/202.png)


### [Java] - [エディター] - [保管アクション]
- 保管時に選択したアクションを実行: チェック
- 追加アクション: チェック
- [構成] ボタンをクリックし、以下を追加
  - コード・スタイル - if/while/for/do ステートメントでブロックを使用 - 常時
  - コード・スタイル - 'for' ループを拡張へ変換
  - コード・スタイル - 式で括弧を使用 - 必要な場合のみ
  - コード・スタイル - 関数型インターフェース・インスタンスへ変換: 可能な場合はラムダを使用
  - コード編成 - 末尾の空白を除去 - 全ての行
  - メンバー・アクセス - 宣言されているクラスを修飾子として使用: チェック
  - 不要なコード - 未使用のインポートの除去
  - 不要なコード - 不要な '$NON-NLS$' タグを除去
  - 不要なコード - 冗長な型引数を除去

保管アクションは、エディタを保存するたびに実行してくれる処理になります. うまく設定することで作業を自動化させたり、チームでの開発で お作法を整えたりできるのでかなり便利です. 厳しくするなら全部チェックしてもよいぐらいですが、このぐらいがバランスが良いように感じます.
ソース・コードのフォーマットにチェックがついているチームに入ったことがありますが、ちょっと使いにくかった... (たぶん Ctrl + S を かなりの高頻度で押す私が悪い)
![](/assets/eclipse/4.7-oxygen-config/203.png)


### [Java] - [コード・スタイル] - [コード・テンプレート]
- 新規メソッドと型のコメントを自動的に追加: チェック

Javadoc の テンプレートが自動で入ってくれるので便利です. ここのテンプレートもしっかり設定したいのですが、今回は設定項目が多いので別途.
![](/assets/eclipse/4.7-oxygen-config/204.png)


### [Java] - [コード・スタイル] - [フォーマッター]
[新規] ボタンをクリック
![](/assets/eclipse/4.7-oxygen-config/205.png)


### [Java] - [コード・スタイル] - [フォーマッター] - [新規プロファイル]
プロファイル名に任意の名称 (ここでは Formatter) を 入力し、[OK] ボタンをクリック
![](/assets/eclipse/4.7-oxygen-config/206.png)


### [Java] - [コード・スタイル] - [フォーマッター] - [Formatter プロファイル]
- タブ・ポリシー: スペースのみ

実は Java エディター の タブ設定は、ここに隠れているのです... 前回の [タブでスペースを挿入: チェック](/2017/08/03/Eclipse-4.7-Oxygenの設定-一般編/#一般-エディター-テキスト・エディター) は 一般エディターで、Java エディター の 設定ではないので注意が必要です.
![](/assets/eclipse/4.7-oxygen-config/207.png)


### [Java] - [コンパイラー] - [Javadoc]
- 誤った形式の Javadoc コメント: 警告
  - メンバーの可視性を次のように設定: Private
  - タグ引数の検証: チェック
  - 不可視参照をレポート: チェック
  - 使用すべきでない参照をレポート: チェック
  - タグ記述の欠落: すべての標準タグを検証
- 未指定の Javadoc タグ: 警告
  - メンバーの可視性を次のように設定: Private
  - オーバーライドしたメソッドの実装を無視: チェックを外す
  - メソッド型パラメーターを無視: チェックを外す
- 未指定の Javadoc コメント: 警告
  - メンバーの可視性を次のように設定: Protected
  - オーバーライドしたメソッドの実装を無視: チェックを外す

ひたすら厳しく設定しています. Javadoc は Protected 以上は書くようにし、書くからには厳しくチェックです.
よく「コードを見ればわかるでしょ」って言われるのですが、それはコードを見る側が言う言葉で、コードを見てもらう側がいう言葉ではないと思っています. 私の周辺だと、この違いがスキルにも大きく表れているように感じます.
![](/assets/eclipse/4.7-oxygen-config/208.png)


### [Java] - [コンパイラー] - [エラー/警告]
- フィルター入力 へ `~無視` を 入力し、以下を除いて すべて 警告
  - インスタンス・フィールドへの限定されていないアクセス: 無視
  - 外部化されていないストリング: 無視
  - static にできるメソッド: 無視
  - 潜在的に static にできるメソッド: 無視
  - ボクシングおよびアンボクシング変換: 無視

ここも厳しく設定しています. 最初から厳しくし設定しておけば習慣化しますし、コードレビューの際に不要なチェックや雑音というか不要なノイズが減るので、ちゃんと本質を見てもらいやすくなります.
![](/assets/eclipse/4.7-oxygen-config/209.png)


### [Java] - [ビルド・パス]
- ソース・フォルダー名: src/main/java
- 出力フォルダー名: target/classes

Maven の パスに合わせました.
![](/assets/eclipse/4.7-oxygen-config/210.png)


### [Maven]
- アーティファクト・ソースのダウンロード: チェック

Java 開発をする場合は、Maven/Gradle は 使うケースが多いかと思います. Eclipse の Gradle 設定はあまりないのですが、Maven は 少し設定しておきたいです.
利用するライブラリーのソースは参照できるようにしておくと便利なのでチェックしておきます. Javadoc は ソースから見れるので Javadoc の ダウンロードは設定してません.
![](/assets/eclipse/4.7-oxygen-config/211.png)


### [Maven] - [ユーザー・インターフェース]
- デフォルトの POM エディターで XML ページを開く

GUI エディター より、XML で 見た方が早いので設定しました. GUI エディターは設定するときよりも、Jar の 依存関係を見たりするときに使ってます.
![](/assets/eclipse/4.7-oxygen-config/212.png)


### [実行/デバッグ] - [コンソール]
- コンソールのバッファー・サイズ: 200000

少し多めに.
![](/assets/eclipse/4.7-oxygen-config/213.png)



- - - -
## Eclipse の 書籍
本記事の 4.7 より古いバージョンとなりますが、Eclipse の 使い方について一通り知るにはよいでしょう. Eclipse は だいぶ枯れているので、大きな違いはないので多少のバージョン違いでも特に問題なく対応できます. (※ Eclipse や IDE を すでに使っている場合には不要です)
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/61UJFDCJZyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Eclipse 4.4 完全攻略 (完全攻略シリーズ)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">石黒 尚久 SBクリエイティブ 2014-10-25    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12960645%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-79-738095-8%2520%257C%25204-797-38095-8%2520%257C%25204-7973-8095-8%2520%257C%25204-79738-095-8%2520%257C%25204-797380-95-8%2520%257C%25204-7973809-5-8" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

## Java の 書籍
IDE は あくまでも道具なので、プログラミングの知識はしっかりつけておきたいところです.
Java 8 までを、しっかり身に着けるには こちらの書籍がよいです. こんな感じで Java 9 も 出してほしいですね.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51741IwOl5L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Java本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">谷本 心,阪本 雄一郎,岡田 拓也,秋葉 誠,村田 賢一郎 技術評論社 2017-04-18    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14782914%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418909-3%2520%257C%25204-774-18909-3%2520%257C%25204-7741-8909-3%2520%257C%25204-77418-909-3%2520%257C%25204-774189-09-3%2520%257C%25204-7741890-9-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
結構、設定項目がありました. 手になじませて使うためにも仕方ないですね.
