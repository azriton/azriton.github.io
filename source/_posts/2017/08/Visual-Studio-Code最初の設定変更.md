---
title: Visual Studio Code 最初の設定変更
date: 2017-08-26
updated: 2017-09-04
comments: true
categories: 開発環境
tags:
- Visual Studio Code
---

![](/assets/vscode/visual-studio-code.png "Visual Studio Code")

[Visual Studio Code が インストールでき](/2017/08/18/Visual-Studio-Codeのインストール/)、[Git も 使える](/2017/08/23/Visual-Studio-CodeでGitを使う/)ようになりました. まだ使いこんでいないので、よい設定や便利な拡張機能が見いだせていないですが、とりあえず必要最低限の最初の設定をしておきます.
Eclipse での設定と同様、各種設定については、お好みがあると思います. ご参考になれば、といったところでしょうか.

**作業環境**
- Windows 10 64bit
- Visual Studio Code


## ユーザー設定の表示
Visual Studio Code を 起動します.
![](/assets/vscode/config/01.png)

`Ctrl + ,` で ユーザー設定 の settings.json を 表示します.
Visual Studio Code は GUI の 設定を持っておらず、JSON ファイルを編集して設定します. とはいえ、各設定に説明がついていこと、エディターによる支援もあるので、難しいことなく設定できます.
![](/assets/vscode/config/02.png)


## 設定変更の方法概要
ユーザー設定が分割エディターで表示されています.
左側が既存の設定でデフォルトになります. そこから変更する項目を右側にコピーして値を変更します.
コピーと設定変更は、左側で変更したい項目にマウスをのせると、鉛筆アイコンが出てくるので、クリックします.
クリックすると、設定できる値に応じて選択肢が表示されたり、テキスト入力の場合はコピーするといったアクションが表示されます. アクションに応じて設定を変更していきます.
Visual Studio Code の 再起動が必要な場合は、ダイアログが表示されるので指示に従います.


## フォントの変更
まずはフォントを例に設定変更します.
開発環境にとってフォントは大事、今回も Eclipse でも使っている M+とIPAの合成フォント さん の [Migu 1M](http://mix-mplus-ipa.osdn.jp/migu/) を 使わせていただきます.. 素晴らしいフォントをありがとうございます！！

分割されているユーザー設定の左側、既存の設定 から [エディター] を 開きます.
![](/assets/vscode/config/03.png)

`"editor.fontFamily": "Consolas, 'Courier New', monospace",` の 行に マウスをのせると [鉛筆アイコン] が 表示されます.
![](/assets/vscode/config/04.png)

[鉛筆アイコン] を クリックします.
今回はテキスト入力の設定項目のため [設定にコピー] が 表示されるので、クリックします.
![](/assets/vscode/config/05.png)

右側にコピーされ、設定値 ここでは `"Consolas, 'Courier New', monospace"` に フォーカスがあたります.
![](/assets/vscode/config/06.png)

右側にcopyされた設定を `"'Migu 1M'"` にし、 `Ctrl + S` で 保存します. エディターに表示されているフォントが即時に変更されます.
![](/assets/vscode/config/07.png)


## 空白文字の表示方法を変更
続いて選択肢のある設定変更の例として、空白文字の表示方法を変更します.
左側から `"editor.renderWhitespace": "none",` を 探す or 設定の検索 をし、 [鉛筆アイコン] を クリックします.
![](/assets/vscode/config/08.png)

設定値の選択肢が表示されるので選択します. 今回は `"boundary"` を 選択しました.
右側にコピーされ、値が `"boundary"` に なっています.
![](/assets/vscode/config/09.png)


## 今回設定した項目
上記 フォント変更 や 空白文字の表示方法 の 設定変更のように、左側の [鉛筆アイコン] から アクションを選択し、右側で設定を変更していくことで、設定が変更できます.
これを Visual Studio Code 1.15.0 時点、354個 の 項目から変更する値を探して設定します. 凄い設定項目数ですね...

今回は以下の設定を行いました.

|            設定項目            |   設定値    |                    設定内容                     |
| :----------------------------- | :---------- | :---------------------------------------------- |
| editor.fontFamily              | "'Migu 1M'" | フォント を Migu 1M                             |
| editor.rulers                  | [80, 120]   | 垂直ルーラー は 標準の `80` と よく使う `120`   |
| editor.minimap.showSlider      | "always"    | ミニマップのスライダー を 常に表示              |
| editor.cursorBlinking          | "smooth"    | カーソル・アニメーション を スムーズに          |
| editor.renderWhitespace        | "boundary"  | 空白文字を単語間の単一スペース以外表示          |
| files.autoGuessEncoding        | true        | ファイルを開くときに文字セット エンコードを推測 |
| files.eol                      | "\n"        | 既定の改行文字を LF に設定                      |
| files.trimTrailingWhitespace   | true        | ファイルの保存時に末尾の空白をトリミング        |
| files.insertFinalNewline       | true        | ファイルの保存時に最新の行を末尾に挿入          |
| terminal.integrated.scrollback | 200000      | ターミナルの最大行数                            |

**更新**
2017年9月4日 `editor.minimap.showSlider` を 追加



- - - -
とりあえず基本設定はできました. `files.insertFinalNewline` などは よい設定ですね. 私は習慣化しているのでエディターの支援が効く機会が少ないかもしれませんが、チーム開発ではチョイチョイ引っかかるケースがあるので、設定に入れてしまって自動でやってしまうのがよさそうです.
