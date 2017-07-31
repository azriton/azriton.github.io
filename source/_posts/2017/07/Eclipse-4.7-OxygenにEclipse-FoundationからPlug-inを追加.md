---
title: Eclipse 4.7 Oxygen に Eclipse Foundation から Plug-in を 追加
date: 2017-07-31
comments: true
categories: 開発環境
tags:
- Eclipse
---

![](/images/eclipse/4.7-oxygen.png "Eclipse Oxygen")

前回 [Eclipse 4.7 Oxygen を インストール](/2017/07/28/Eclipse-4.7-Oxygenリリース！＆インストール/) しました. インストールしたのは Eclipse IDE for Java Developers の パッケージになり、HTML や CSS などの Web 開発を行うには Plug-in を 追加する必要があります. 今回は Web 開発系 の Plug-in 他、Eclipse Foundation が 提供している Plug-in を 追加します. (3rd Party の Plug-in は 別途)

**作業環境**
- Windows 10 64bit
- Java SE Development Kit 1.8.0_141
- Eclipse 4.7 Oxygen


## Plug-in の 追加手順
Eclipse の メニューから [ヘルプ] - [新規ソフトウェアのインストール] を クリックします.
![](/images/eclipse/4.7-oxygen-eclipse-plugins/01.png)

インストールする Plug-in の 選択画面が表示されるので、[作業対象] に [--すべての使用可能なサイト--] を 選択します.
![](/images/eclipse/4.7-oxygen-eclipse-plugins/02.png)

少しするとインストールできる Plug-in の 一覧が表示されるので、インストールする Plug-in に チェックします.
今回は以下の Plug-in を 選択しました.
- [コラボレーション] - [動的言語ツールキット - Mylyn 統合]
- [コラボレーション] - [Eclipse GitHub 統合 (タスク・フォーカス・インターフェース)]
- [Web, XML, Java EE および OSGi エンタープライズ開発] - [Eclipse Web 開発者ツール]
- [Web, XML, Java EE および OSGi エンタープライズ開発] - [JavaScript 開発ツール]
![](/images/eclipse/4.7-oxygen-eclipse-plugins/03.png)

インストールする Plug-in の 確認画面が表示されるので、[次へ] ボタンをクリックして進めます.
![](/images/eclipse/4.7-oxygen-eclipse-plugins/04.png)

ライセンスの確認画面が表示されます. 内容を確認し同意できたら [使用条件の条項に同意します] を 選択し、[完了] ボタンをクリックします. 同意できなかったら、残念ながら終了です...
![](/images/eclipse/4.7-oxygen-eclipse-plugins/05.png)

Plug-in の ダウンロード と インストールが行われます. インストールが完了すると再起動の確認ダイアログが表示されます. 特に問題なければ [今すぐ再起動] ボタンをクリックします.
![](/images/eclipse/4.7-oxygen-eclipse-plugins/06.png)

[いいえ] を 選んで Eclipse を自分で終了した場合は、次回 `eclipse.exe -clean.cmd` で 起動します. 日本語化 Plug-in の Pleiades を 正しく機能させるためには  Plug-in を 追加した後はクリーンアップ起動する必要があります.
![](/images/eclipse/4.7-oxygen-install/09.png)

いずれにしても Plug-in 追加後の起動で、下図のような「キャッシュのクリーンアップ中」の スプラッシュが表示されないで起動した場合は Plug-in の 機能 および 日本語化が正しく行えていない場合があるので、 `eclipse.exe -clean.cmd` で 起動しなおします.
![](/images/eclipse/4.7-oxygen-install/10.png)



- - - -
HTML や CSS、JavaScript などは Atom や Visual Studio Code といった高機能エディタで開発する流れもあり、また サーバサイドは Web API に することも多く、Eclipse で HTML/CSS/JavaScript を コーディングするシーンが減ってくるのかなとも思いますが、Plug-in を 入れておくとハイライトしてくれたりと、何かと便利なので入れています. 設定については次回にて.
