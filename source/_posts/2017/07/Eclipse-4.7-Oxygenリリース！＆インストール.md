---
title: Eclipse 4.7 Oxygen リリース！ ＆ インストール
date: 2017-07-28
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

[Eclipse Oxygen Now Available - 2017/06/28](https://www.eclipse.org/oxygen) ということで、統合開発環境 Eclipse の 新バージョンがリリースされました！ 毎年6月末ごろのお楽しみですね♪ 色々あって出遅れてしまいましたが、新バージョンを試してみたいと思います.

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## Eclipse とは
改めて書くほどのことではないかもしれませんが、本ブログで取り扱うのは初なので、一応ざっくりと.
当初 IBM が 開発し、現在は Eclipse Foundation が 開発・提供をしていフリー＆オープンソースの統合開発環境(IDE)です. Java の 流行に合わせて発展・普及してきた感じがあります. Java だけではなく、HTML, CSS, JavaScrip, C/C++, PHP や XML などの言語に対応しているほか、モデリング や リッチクライアント といった多様な開発が行えます. また Plug-in で 機能拡張もでき Python なども Plug-in で 対応できます. (基本的には全部 Plug-in で対応しているので、公式にパッケージングされているかの違いですね)
Android アプリ の 開発は、残念ながら [Android Studio](https://developer.android.com/studio) へと移行してしまいました...
無料で使える IDE としては、かなり高機能で Eclipse だけでかなりのことができます. とはいえ、最近は [Atom](https://atom.io) や [Visual Studio Code](https://code.visualstudio.com/) といった高機能エディタの流れもあり、軽量プログラミング言語と呼ばれる領域の開発はエディタに変わり、重量級の開発が Eclipse に 残る感じでしょうか.

今回のリリース 4.7 Oxygen では、たくさんの修正が入っているものの、さすがに 15年を超える歴史を持つソフトウェアとなると大きな新機能は無いようです. Java 9 対応がありますが、Java 9 自体が 2017年9月リリース予定なので early access preview となっています.

詳細なリリースは [Eclipse IDE, Oxygen Edition New and Noteworthy](https://www.eclipse.org/oxygen/noteworthy/) になります. また こちらの [Eclipse 4.7 Oxygen 新機能 30+ / Java 9 を試そう！ - Eclipse Oxygen 新機能・変更点](http://qiita.com/cypher256/items/e308d920dfaf15892baa#eclipse-oxygen-%E6%96%B0%E6%A9%9F%E8%83%BD%E5%A4%89%E6%9B%B4%E7%82%B9) が 詳細にまとめてくださっています.


## インストール手順
※ [Java](http://www.oracle.com/technetwork/java/javase/downloads/) は インストールされている前提とします.

Eclipse の ダウンロード・サイト [http://www.eclipse.org/downloads/](http://www.eclipse.org/downloads/) へ アクセスします.
環境を自動判別して [DOWNLOAD 64 BIT] ボタンが用意されていますが、今回は ZIP ファイルから入れるので、ボタンの下 [Download Packages] を クリックします. (インストーラーを使うと簡単なのですが、インストール・パスが思うようにいかなかったので ZIP ファイル に しました)
![](/images/eclipse/4.7-oxygen-install/01.png)

今回は Java の 開発で使うので、Eclipse IDE for Java Developers の パッケージを選択しました. Eclipse IDE for Java Developers の 行の、[64 bit] リンク を クリックします.
![](/images/eclipse/4.7-oxygen-install/02.png)

ダウンロード画面が表示されます. ダウンロードする前に [SHA-512] アイコンをクリックしてファイルのチェックサムをコピーしておきます. 準備ができたら [DOWNLOAD] ボタンをクリックしてダウンロードを開始します.
![](/images/eclipse/4.7-oxygen-install/03.png)

ファイルがダウンロードできたら Windows PowerShell 4.0 以降の `Get-FileHash` コマンドを使い、ダウンロード前にコピーしたチェックサムを確認します. eclipse-java-oxygen-R-win32-x86_64.zip の チェックサムは `aa41bced7699b933fac9b254de1609edfec7f2baac89a5a262e92d9119561a6b19fb367a65946ced7ab718f640b74b51f753ed685aeeb45b50f533e6131ac85a` でした.
![](/images/eclipse/4.7-oxygen-install/04.png)

ただし SHA-512 の チェックサムは長い文字列のため、コマンド プロンプト からの実行では出力が切れてしまいます.
```console
c:\>powershell Get-FileHash -Algorithm SHA512 %USERPROFILE%\Downloads\eclipse-java-oxygen-R-win32-x86_64.zip

Algorithm  Hash
---------  ----
SHA512     AA41BCED7699B933FAC9B254DE1609EDFEC7F2BAAC89A5A262E92D9119561A6B19F...
```

そのため PowerShell を 起動して、`Get-FileHash` から `Format-List` へ パイプします.
```console
c:\> powershell

PS C:\> Get-FileHash -Algorithm SHA512 $ENV:UserProfile\Downloads\eclipse-java-oxygen-R-win32-x86_64.zip | Format-List

Algorithm : SHA512
Hash      : AA41BCED7699B933FAC9B254DE1609EDFEC7F2BAAC89A5A262E92D9119561A6B19FB367A65946CED7AB718F640B74B51F753ED685AEEB45B50F533E6131AC85A
```

ZIP ファイル を 任意の場所に解凍します. 今回は `C:\Develop\tool\eclipse-4.7-oxygen-install` に 解凍しました.
※ 解凍ツールは Windows 10 の エクスプローラー で 右クリック から [すべて展開] を 使いました. 解凍する場所によっては [Windows の パスの長さ(ドライブ・レター、フォルダ、ファイル名) が 260文字に制限されている](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx#maxpath) 件にかかりエラーとなる場合があります. その場合は、パスが短くなる場所で解凍します. こちら [Pleiades - 日本語化プラグイン Eclipse、Android Studio、PhpStorm](http://mergedoc.osdn.jp/) さん の ページ下部「Windows 上で zip を解凍するときの注意」に 詳細があります.
![](/images/eclipse/4.7-oxygen-install/05.png)


## 日本語化
続いて Eclipse を 日本語化します. [Pleiades - 日本語化プラグイン Eclipse、Android Studio、PhpStorm](http://mergedoc.osdn.jp/) さん の サイトから Pleiades プラグイン を ダウンロードします. 今回は [最新版] を 利用させていただきました.
英語が苦手なので日本語化プラグインにはいつも助けていただいています. 素晴らしいプラグインをありがとうございます！
![](/images/eclipse/4.7-oxygen-install/06.png)

ダウンロードしたら、チェックサム... は わからなかったので確認できないです. (Subversion に コミットされているファイルのチェックサムってどうやってとるんだろう)
![](/images/eclipse/4.7-oxygen-install/07.png)

Eclipse を インストールした場所に解凍します.
![](/images/eclipse/4.7-oxygen-install/08.png)

続いて、 `eclipse.ini` に 以下の 2行を追記します. その際に改行コードが `LF` を 扱えるエディタで編集する必要があります. 改行コードを `CRLF` で 保存すると Eclipse が 起動しないことがあるので注意が必要です. 今回は Visual Studio Code を 使いました. (Eclipse を 一度起動すると `CRLF` に なるので、一度起動して終了し、編集するのも手かもしれません)
併せて `-Duser.name=Your Name` の 行も追加しておくと便利です. これを設定しておくと Eclipse の Javadoc コード保管で `Your Name` に 指定した文字列を入れてくれます.
```ini
-Xverify:none
-javaagent:plugins/jp.sourceforge.mergedoc.pleiades/pleiades.jar
```

`eclipse.exe -clean.cmd` を ダブルクリックして Eclipse を 起動します.
普段は `eclipse.exe` を 使いますが、初回起動 と Plug-in の 追加を行った場合は、 `eclipse.exe -clean.cmd` を 使ってクリーンアップした起動を行います.
![](/images/eclipse/4.7-oxygen-install/09.png)
![](/images/eclipse/4.7-oxygen-install/10.png)

ワークスペースのディレクトリーを選択するダイアログが表示されます. 任意の場所を指定し、[この選択をデフォルト...] に チェックします. (今回は `C:\Develop\workspace` に しました)
![](/images/eclipse/4.7-oxygen-install/11.png)

日本語化した Eclipse が 無事に起動しました！
「ようこそ」画面が表示されています. 必要なくなりましたら、右下の [始動時に常に「ようこそ」を表示] の チェックを外して、「ようこそ」タブ の 右にある [×] を クリックしてエディタを閉じます.
![](/images/eclipse/4.7-oxygen-install/12.png)



- - - -
Eclipse の インストーラー が できたとはいえ 日本語化の手順など、年１回の新バージョン・リリースでインストールしているので手慣れてはいますが、やはり整理しておかないとですね. 今もあるのかわかりませんが `eclipse.ini` の `CRLF` トラブルはかなりはまった記憶があります...
インストール後の各種設定もいろいろあし、Python や Visual Studio Code などにも手を出し始めたので、しばらくは初期設定話が続きそう orz
