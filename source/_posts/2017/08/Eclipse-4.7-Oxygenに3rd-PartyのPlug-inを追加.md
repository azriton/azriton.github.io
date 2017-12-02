---
title: Eclipse 4.7 Oxygen に 3rd Party の Plug-in を 追加
date: 2017-08-15
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

ようやく Eclipse の 設定ができました. 最後に 3rd Party の Plug-in を 追加して完了となります.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## Plug-in の 追加
Eclipse の メニューから [ヘルプ] - [新規ソフトウェアのインストール] を クリックします. 以降、この [使用可能なソフトウェア] ウィンドウを使って追加していきます.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/01.png)


## SpotBugs (FindBugs)
[SpotBugs](https://spotbugs.github.io/) は 問題になるコードを検出してくれるツールです. Eclipse Plug-in を 入れることでリアルタイムに検出してくれるので便利です. 以前は FindBugs というプロダクトだったのですが、最近は SpotBugs に なったようです.

[作業対象] に `https://spotbugs.github.io/eclipse/` を 入力し Enter キーを押下します.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/11.png)

しばらく待つとウィンドウ中央のツリーに SpotBugs が 追加されるのでチェックをつけ、[次へ] ボタンをクリックします.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/12.png)

インストールの詳細が表示されます. [次へ] ボタンをクリックして進めます.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/13.png)

ライセンスの確認画面が表示されます. ライセンスを確認し同意できたら [使用条件の条項に同意します] を 選択し、[完了] ボタンをクリックします.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/14.png)

セキュリティ警告が出ますが、[インストール] ボタンをクリックして進めます.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/15.png)

インストール後、再起動を確認されます. 再起動しないと Plug-in が 有効にならないので [今すぐ再起動] ボタンをクリックします.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/16.png)

再起動後 [ウィンドウ] - [設定] から Eclipse の 設定を表示し設定を行います.
[Java] - [SpotBugs] - [報告構成]
- 分析力: 最大
- 報告する最小ランク: 1
- レポートする最低の信頼度: Low
- 報告(可視) バグ・カテゴリー: 実験用 以外 すべてチェック

Eclipse の Java コンパイラー設定同様に、とにかく厳しくします.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/17.png)

[Java] - [SpotBugs] - [ディテクター構成]
- 隠しディテクターを表示: チェック
- ディテクター: すべてチェック

実際のチェックを行う設定は、ディテクターになるので、ここも厳しく設定します.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/18.png)


## Eclipse Class Decompiler
[Eclipse Class Decompiler](http://www.cpupk.com/decompiler/) は JD, Jad, FernFlower, CFR, Procyon といった Java の デコンパイラーを使えるようにする Plug-in です.
普段使うことは無いのですが、ソースがロスとされている古いライブラリがあり仕方なしにデコンパイルすることがあるので入ってます. そんなもの捨ててしまえばいいのに...

Eclipse の メニューから [ヘルプ] - [新規ソフトウェアのインストール] で [使用可能なソフトウェア] ウィンドウ を 表示し、[作業対象] に `https://spotbugs.github.io/eclipse/` を 入力し Enter キーを押下します.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/21.png)

すべてにチェックをつけ [次へ] ボタンをクリックして進めます. 以降は SpotBugs の 手順同様に インストールの詳細を確認し、ライセンスの確認と同意、インストールして再起動します.
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/22.png)

再起動後 [ウィンドウ] - [設定] から Eclipse の 設定を表示し設定を行います.
[Java] - [逆コンパイラー]
- デフォルトのクラス逆コンパイラー: FernFlower
- オリジナル行番号をコメントとして出力

いろいろな逆コンパイラーが使えるので好みの逆コンパイラーを設定します. 違いについては こちら [Jadclipse (Jad + JD-Core) から Eclipse Class Decompiler プラグインへ変更 - Eclipse 4.6 Neon 新機能 TOP10！と Spring Boot STS - Qiita](http://qiita.com/cypher256/items/2384d6797ac49740f217#jadclipse-jad--jd-core-%E3%81%8B%E3%82%89-eclipse-class-decompiler-%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3%E3%81%B8%E5%A4%89%E6%9B%B4) の [cypher256](http://qiita.com/cypher256) さんが整理してくださっています. 逆コンパイル結果まで載せてくださっており、とても助かります. 素晴らしい まとめ ありがとうございます！
![](/images/eclipse/4.7-oxygen-3rd-party-plugins/23.png)



- - - -
## Eclipse の 書籍
本記事の 4.7 より古いバージョンとなりますが、Eclipse の 使い方について一通り知るにはよいでしょう. Eclipse は だいぶ枯れているので、大きな違いはないので多少のバージョン違いでも特に問題なく対応できます. (※ Eclipse や IDE を すでに使っている場合には不要です)
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/61UJFDCJZyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Eclipse 4.4 完全攻略 (完全攻略シリーズ)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">石黒 尚久 SBクリエイティブ 2014-10-25    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12960645%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-79-738095-8%2520%257C%25204-797-38095-8%2520%257C%25204-7973-8095-8%2520%257C%25204-79738-095-8%2520%257C%25204-797380-95-8%2520%257C%25204-7973809-5-8" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

## Java の 書籍
IDE は あくまでも道具なので、プログラミングの知識はしっかりつけておきたいところです.
Java 8 までを、しっかり身に着けるには こちらの書籍がよいです. こんな感じで Java 9 も 出してほしいですね.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51741IwOl5L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Java本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">谷本 心,阪本 雄一郎,岡田 拓也,秋葉 誠,村田 賢一郎 技術評論社 2017-04-18    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14782914%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418909-3%2520%257C%25204-774-18909-3%2520%257C%25204-7741-8909-3%2520%257C%25204-77418-909-3%2520%257C%25204-774189-09-3%2520%257C%25204-7741890-9-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
コードカバレッジが取れる [EclEmma](http://www.eclemma.org/) が デフォルトで入ってくれるようになったので、ここでの設定が減ってよかったです.
ようやく Eclipse の 設定が完了しました. 長かった～. 年１回とはいえなかなかヘビーですね.
