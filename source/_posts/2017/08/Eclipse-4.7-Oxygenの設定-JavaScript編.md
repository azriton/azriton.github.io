---
title: Eclipse 4.7 Oxygen の 設定 - JavaScript編
date: 2017-08-09
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

今回は、Eclipse 4.7 Oxygen の JavaScript に 関する設定を行っていきます.
引き続き各種設定については、お好みがあると思います. ご参考になれば.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## 設定
まずは、いつも通り Eclipse の メニュー から [ウィンドウ] - [設定] を クリックし、設定ウィンドウを表示します.
![](/images/eclipse/4.7-oxygen-config/001.png)


### [JavaScript] - [エディター] - [入力]
- セミコロン: チェック
- 波括弧: チェック
- 文字列リテラルへの貼り付け時にテキストをエスケープ: チェック

なるべく自動でやってもらった方が楽なので...
![](/images/eclipse/4.7-oxygen-config/301.png)


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
![](/images/eclipse/4.7-oxygen-config/302.png)


### [JavaScript] - [コードスタイル]
- 新機関数と型のコメントを自動的に追加: チェック

JSDoc の テンプレートが自動で入ってくれるので便利です.
![](/images/eclipse/4.7-oxygen-config/303.png)


### [JavaScript] - [コードスタイル] - [フォーマッター]
[新規] ボタンをクリック
![](/images/eclipse/4.7-oxygen-config/304.png)


### [JavaScript] - [コードスタイル] - [フォーマッター] - [新規プロファイル]
プロファイル名に任意の名称 (ここでは Formatter) を 入力し、[OK] ボタンをクリック
![](/images/eclipse/4.7-oxygen-config/305.png)


### [JavaScript] - [コードスタイル] - [フォーマッター] - [Formatter プロファイル]
- タブ・ポリシー: スペースのみ
- インデント・サイズ: 2

Java エディター の タブ設定同様で、ここで設定しないとタブが入力されます. 要注意です. またインデント・サイズ は [Google の JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html) の "Each time a new block or block-like construct is opened, the indent increases by two spaces. - [4.2 Block indentation: +2 spaces](https://google.github.io/styleguide/jsguide.html#formatting-block-indentation)" に 習って `2` にしました.
![](/images/eclipse/4.7-oxygen-config/306.png)


### [JavaScript] - [バリデーター] - [JSDoc]
- 誤った形式の Jsdoc コメント: 警告
  - タグ内のエラーをレポート: チェック
- 未指定の Jsdoc タグ: 警告
- 未指定の Jsdoc コメント: 警告

厳しく設定します.
![](/images/eclipse/4.7-oxygen-config/307.png)


### [JavaScript] - [バリデーター] - [エラー/警告]
- JavaScript セマンティクス検証を使用可能にする: チェック
- 次の JavaScript バリデーター・オプションの問題重大度レベルを選択: 以下を除き すべて `警告`
  - 外部化されていないストリング: 無視

厳しく設定します. この手のものは習慣なので、厳しく設定したとしても習慣化されれば気にもならなくなるので、むしろ設定しておいた方が楽ですね.
![](/images/eclipse/4.7-oxygen-config/308.png)


### [JSON] - [JSON ファイル] - [エディター]
- 行の幅: 999
- スペースを使用したインデント: チェック
  - インデント・サイズ: 2

JSON は データの表現なので行の幅を縛るより、ありのままで表示してほしいので 行の幅 を `999` に しました. またインデント・サイズは JavaScript の コードに合わせて `2` に しています.
![](/images/eclipse/4.7-oxygen-config/309.png)


### [JSON] - [JSON ファイル] - [検証]
- 構文検証を使用可能にする: チェック
- スキーマ検証を有効にする: チェック

チェックはしてもらった方が良いので、有効にします.
![](/images/eclipse/4.7-oxygen-config/310.png)



- - - -
Java 程ではありませんでしたが、設定項目は多めでした. だいぶ設定ができてきました.
