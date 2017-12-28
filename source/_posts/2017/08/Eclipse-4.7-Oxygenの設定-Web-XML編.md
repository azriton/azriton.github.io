---
title: Eclipse 4.7 Oxygen の 設定 - Web/XML編
date: 2017-08-12
updated: 2017-08-12
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/assets/eclipse/4.7-oxygen.png "Eclipse Oxygen")

Eclipse 4.7 Oxygen の 設定も大詰め、Web/XML の 設定になります. ここはエディターの設定を少し変えたいぐらいになります.
大きくは、インデントを [Google の HTML/CSS Style Guide](https://google.github.io/styleguide/htmlcssguide.html) の "Indent by 2 spaces at a time. - [2.2.1 Indentation](https://google.github.io/styleguide/htmlcssguide.html#Indentation)" に ならって設定したところと、XML の 検証が無効になっているので有効にします.

また行の幅を `999` に しています. 私は HTML や XML の タグは折り返したくないというがあり、最長の設定にしています. ここはチームによってかなり温度差がありそうです. 周りとよく相談のうえで設定が必要ですね.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、いつも通り Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/assets/eclipse/4.7-oxygen-config/001.png)

### [Web] - [CSS ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/assets/eclipse/4.7-oxygen-config/401.png)

### [Web] - [HTML ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/assets/eclipse/4.7-oxygen-config/402.png)

### [XML] - [XML ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/assets/eclipse/4.7-oxygen-config/403.png)

### [XML] - [XML ファイル] - [検証]
- 文法指定なし: 警告 (必要に応じて 無視)
- マークアップ検証を使用可能にする: チェック
![](/assets/eclipse/4.7-oxygen-config/404.png)



- - - -
## Eclipse の 書籍
本記事の 4.7 より古いバージョンとなりますが、Eclipse の 使い方について一通り知るにはよいでしょう. Eclipse は だいぶ枯れているので、大きな違いはないので多少のバージョン違いでも特に問題なく対応できます. (※ Eclipse や IDE を すでに使っている場合には不要です)
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/61UJFDCJZyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Eclipse 4.4 完全攻略 (完全攻略シリーズ)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">石黒 尚久 SBクリエイティブ 2014-10-25    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4797380950" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F12960645%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-79-738095-8%2520%257C%25204-797-38095-8%2520%257C%25204-7973-8095-8%2520%257C%25204-79738-095-8%2520%257C%25204-797380-95-8%2520%257C%25204-7973809-5-8" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

## Java の 書籍
IDE は あくまでも道具なので、プログラミングの知識はしっかりつけておきたいところです.
Java 8 までを、しっかり身に着けるには こちらの書籍がよいです. こんな感じで Java 9 も 出してほしいですね.
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51741IwOl5L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Java本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">谷本 心,阪本 雄一郎,岡田 拓也,秋葉 誠,村田 賢一郎 技術評論社 2017-04-18    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F477418909X" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14782914%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418909-3%2520%257C%25204-774-18909-3%2520%257C%25204-7741-8909-3%2520%257C%25204-77418-909-3%2520%257C%25204-774189-09-3%2520%257C%25204-7741890-9-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
ここは、あまり設定項目がなかったので簡単に終わりました. 残すは 3rd Party の Plug-in 導入と設定になります. もう一息.
