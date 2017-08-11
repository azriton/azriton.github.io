---
title: Eclipse 4.7 Oxygen の 設定 - Web/XML編
date: 2017-08-12
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

Eclipse 4.7 Oxygen の 設定も大詰め、Web/XML の 設定になります. ここはエディターの設定を少し変えたいぐらいになります.
大きくは、インデントを [Google の HTML/CSS Style Guide](https://google.github.io/styleguide/htmlcssguide.html) の "Indent by 2 spaces at a time. - [2.2.1 Indentation](https://google.github.io/styleguide/htmlcssguide.html#Indentation)" に ならって設定したところと、XML の 検証が無効になっているので有効にします.

また行の幅を `999` に しています. 私は HTML や XML の タグは折り返したくないというがあり、最長の設定にしています. ここはチームによってかなり温度差がありそうです. 周りとよく相談のうえで設定が必要ですね.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、いつも通り Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/images/eclipse/4.7-oxygen-config/001.png)

### [Web] - [CSS ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/images/eclipse/4.7-oxygen-config/401.png)

### [Web] - [HTML ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/images/eclipse/4.7-oxygen-config/402.png)

### [XML] - [XML ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
- インデント・サイズ: 2
![](/images/eclipse/4.7-oxygen-config/403.png)

### [XML] - [XML ファイル] - [検証]
- 文法指定なし: 警告 (必要に応じて 無視)
- マークアップ検証を使用可能にする: チェック
![](/images/eclipse/4.7-oxygen-config/404.png)



- - - -
ここは、あまり設定項目がなかったので簡単に終わりました. 残すは 3rd Party の Plug-in 導入と設定になります. もう一息.
