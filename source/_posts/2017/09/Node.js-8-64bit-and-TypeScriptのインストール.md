---
title: Node.js 8 64bit & TypeScript の インストール
date: 2017-09-01
comments: true
categories: 開発環境
tags:
- Node.js
- TypeScript
- Hexo
---

![](/assets/nodejs/nodejs.png "Node.js")

急遽 TypeScript を 使うことになり... インストールとか環境構築とか. ちょっと手を広げすぎている感があるけど、勉強する機会があるのはよいこと、と考えることにして Let's Go！

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4.2


# バージョン と インストールする環境について検討
ブログ環境を構築する [Hexo を インストール](/2016/11/01/HexoとGitHub-Pagesでブログ環境の構築/)した際に、Node.js 6.9 LTS を インストールしました. その際には "開発用途ではないので LTS(Long Term Support) である v6.9.1 LTS" としてました. が、今回はバージョン指定があり Node.js 8.x に なります.
指定があるということは、考えることがないですね.

なお Hexo の 方も、この際バージョンアップしてしまうことにします. 2つのバージョンを管理したり行き来するのが手間ですし、そもそも v6 LTS の 選択は Hexo 以外に用途がなかったのでサポートが長い LTS にしていたという経緯になります. 今回開発に使うので、ちゃんと新しいバージョンへの追随やトラブルシュートもやる(たぶｎ)ので、あまり困らないかなぁと.


## Node.js の インストール と 動作確認
Node.js の トップ・ページ [https://nodejs.org/en/](https://nodejs.org/en/) へ アクセスします.
画面中ほどにダウンロードリンクがあります.
今回は、ちゃんと Windows の 64bit であることを認識して、[Download for Windows (x64)] で 表示してくれています. と、いうことで素直に [v8.4.0 Current] を クリックしてダウンロードします.
![](/assets/nodejs/install-8/01.png)

チェックサム は ダウンロード・ページ [https://nodejs.org/en/download/current/](https://nodejs.org/en/download/current/) に [Signed SHASUMS for release files] が あります. 今回ダウンロードした node-v8.4.0-x64.msi は `8efbd1b94ff8338bd36a1c30a86aba4fae3b80b61e265401fa97e7a4c5478ab2` でした.
確認方法は Windows PowerShell 4.0 以降の `Get-FileHash` コマンドを使います.
```console
c:\> powershell Get-FileHash -Algorithm SHA256 c:\node-v8.4.0-x64.msi

Algorithm  Hash
---------  ----
SHA256     8EFBD1B94FF8338BD36A1C30A86ABA4FAE3B80B61E265401FA97E7A4C5478AB2
```

ダウンロードした `node-v8.4.0-x64.msi` を ダブルクリックして、インストーラーを起動します.
![](/assets/nodejs/install-8/02.png)
![](/assets/nodejs/install-8/03.png)

End-User License Agreement が 表示されます. 内容を確認し、同意できたら [I accept the terms in the Lisence Agreement] に チェックして [Next] を クリックします.
![](/assets/nodejs/install-8/04.png)

インストール先を指定します. 今回は `C:\Develop\sdk\nodejs8\` と しました.
![](/assets/nodejs/install-8/05.png)

Custom Setup で オプションを聞かれます. 今回は特に変更なしで、すべて インストールしました.
![](/assets/nodejs/install-8/06.png)

インストール確認が表示されるので、[Install] ボタンをクリックしてインストールします.
![](/assets/nodejs/install-8/07.png)

インストールが完了しました.
![](/assets/nodejs/install-8/08.png)

動作確認としてコマンド プロンプト で バージョンを出力してみます. コマンドは `node --version` もしくは `node -v` です. インストール・オプションで [Add to PATH] に チェックした(変更しなかった)ので、パスが通っており、直接 `node` コマンドが実行できます.
```console
c:\> echo %PATH%
C:\Develop\sdk\nodejs8\;C:\Users\username\AppData\Roaming\npm; ...(省略)

c:\> node --version
v8.4.0

c:\> node -v
v8.4.0
```


## TypeScript の インストール と 動作確認
`npm` コマンドでインストールします. TypeScript の トランスパイラー に加え、Lint も 合わせて入れておきます.
```console
c:\> npm install -g typescript tslint
+ tslint@5.6.0
+ typescript@2.4.2
added 31 packages in 4.277s
```

動作確認は TS コンパイラー の `tsc` コマンドで行います.
```console
c:\> tsc --version
Version 2.4.2

c:\> tsc -v
Version 2.4.2
```



- - - -
とりあえず、Node.js 8 と TypeScript が インストールできました. ついでに Hexo 環境もバージョンアップとなりましたが、今のところ特に困ったことはなさそうです. (そりゃ、そうだ)
