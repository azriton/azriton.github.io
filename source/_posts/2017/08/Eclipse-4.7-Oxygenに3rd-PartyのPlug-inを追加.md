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
コードカバレッジが取れる [EclEmma](http://www.eclemma.org/) が デフォルトで入ってくれるようになったので、ここでの設定が減ってよかったです.
ようやく Eclipse の 設定が完了しました. 長かった～. 年１回とはいえなかなかヘビーですね.
