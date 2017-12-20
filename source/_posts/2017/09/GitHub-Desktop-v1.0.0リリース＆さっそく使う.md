---
title: GitHub Desktop v1.0.0 リリース ＆ さっそく使う
date: 2017-09-23
comments: true
categories: 開発環境
tags:
- Git
- GitHub
---

![](/assets/github/github.png "GitHub")

GitHub 社から、公式のデスクトップ・クライアントのアプリ [GitHub Desktop](https://desktop.github.com/) がリリースされました！ [Atom](https://atom.io/) エディターなどで使われている [Electron](https://electron.atom.io/) で 作られています. Electron アプリは開発側のメリットが大きく言われていますが、快適な操作性と、素敵なデザインのアプリが多く、Electron の 開発元でもある GitHub 社のアプリとなると楽しみます. さっそく使ってみます.


**作業環境**
- Windows 10 64bit
- GitHub Desktop 1.0.0
- Git Hub


## GitHub Desktop とは？
GitHub 社 が リリースした、公式の GitHub クライアント・アプリです. これまでβリリースされていましたが、 2017年9月19日に正式にバージョン 1.0.0 が リリースとなりました.
ベータ版では、残念なことに私の環境では動作しなかった(正確にはクローンとかができなかった) ので、正式リリースとのことで楽しみです.


## GitHub クライアント・アプリ への 期待
GitHub Desktop が サポートしているかは試してみるとして、GitHub の クライアント・アプリといったときに、こんな機能が欲しいという期待があります. まぁ、特殊な使い方をしている部分もあるので、あったらラッキーぐらいでしょうし、なければ自分で作るべきなのでしょうが腕が追い付かず...

ともあれ、これから使わせていただく GitHub Desktop、こんなことできるかな？

- Pull Request レビュー ＆ マージ を 専用アプリで
最近はレビューする機会が増え、Pull Request を 見ることが多くなりました.
そうなると、Pull Request の 通知から始まり、やり取りの管理などが簡単に行るようになると助かります. ブラウザでも十分なエクスペリエンスを提供していただいていると思いますが、ブラウザで特定のページを固定的に扱うのが得意でなく、できれば専用アプリで使いたいというのがあります.

- 複数アカウントをワンストップで
そもそも複アカするな、という話もあると思います orz なぜこんなことになっているのか、自分でも困り果てているのですが 使わないといけない GitHub アカウントが多いのです...
ブラウザだと Sign in して 2FA して、Sign out して次. かなり厳しいです. この辺が便利になってくれると嬉しいです.

そんな、超個人的な期待はよそに、インストールを進めます.


## ダウンロード ＆ インストール
GitHub Desktop の ウェブサイト [https://desktop.github.com/](https://desktop.github.com/) へ アクセスします.
環境に合わせてボタンが用意されているのでダウンロードします. 今回は [Download for Windows (64bit)] を 選択しました.
![](/assets/github/desktop/01.png)

ダウンロードされた [GitHubDesktopSetup.exe] を ダブルクリックします. チェックサムは見つかりませんでした. 確認したいなぁ...
![](/assets/github/desktop/02.png)

インストール先やオプションを聞かれることなく、インストールが実行されます. しばし待ちます.
![](/assets/github/desktop/03.png)

Welcome が 表示されます. 今回は [GitHub.com] へ サインアップしました. GitHub Enterprise にも対応しているようです.
![](/assets/github/desktop/04.png)

GitHub の Username(or Email) と Password を 入力して [Sign in] を クリックします. 2FA を 有効にしている場合でもパスワードで大丈夫です. 次でコードを聞かれます.
![](/assets/github/desktop/05.png)

2FA を 有効にしているので、コードを聞かれました. コードを入力して [Verify] します. (2FA を 有効にしていない場合は表示されません)
![](/assets/github/desktop/06.png)

続いて Git の Name と Email を 聞かれます. GitHub で 設定した Username と Email を 入力します. 合っていないと下記画像のように自分の相子ではなく Hubot アイコンが表示されるようです. (Web の GitHub 上でもアイコンが異なり困るので、ちゃんと合わせるようにします)
![](/assets/github/desktop/07.png)

最後に改善のための匿名レポートを送るかを選択し、[Finish] ボタンをクリックします.
![](/assets/github/desktop/08.png)

インストールは、ここまでとなります.
引き続きメイン画面が標示されるので、作業を進めます.


## リポジトリのクローンから、プッシュまで
無事、GitHub Desktop の 画面が標示されました.
まだリポジトリが追加されていないので、追加します. 今回は初回ということで一番右のクローンから始めました.
![](/assets/github/desktop/09.png)

すでにログインしているので、自分のアカウントに関連するリポジトリが表示されます. クローンするリポジトリを選択し [Clone] ボタンをクリックします. 今回は、このブログのソースを選択しました.
![](/assets/github/desktop/10.png)

しばらく待つとクローンされ、画面左上に現在選択しているリポジトリが表示されます. なお、新規クローンなのでローカルに変更はないのでリポジトリ名以外、操作を必要とするような変化はありません.
![](/assets/github/desktop/11.png)

リポジトリの編集をします. これは GitHub Desktop の スコープではなく、メニュー の [Repository] から [Open in XXXX] を 選択します. 今回の環境は Visual Studio Code が 該当するため Visual Studio Code に なっています. Atom が 入っている場合は Atom が 表示されるでしょう. (設定変更は後述)
![](/assets/github/desktop/12.png)

ソース編集した Visual Studio Code は、こちらの記事のスコープ外なので編集できたとして、GitHub Desktop に 戻ると Git の 更新が表示されます. (表示されない場合は右上の [Fetch origin] を クリックします)
ここでは、本記事の前の [Raspberry Pi 基盤 の LED を 消灯する](https://azriton.github.io/2017/09/20/Raspberry-Pi-%E5%9F%BA%E7%9B%A4%E3%81%AELED%E3%82%92%E6%B6%88%E7%81%AF%E3%81%99%E3%82%8B/) の 変更がリストされています.
左下のフォームで、コミットのサマリと説明を入力し、[Commit to source] ボタンをクリックするとコミットできます.
※ このリポジトリのデフォルト・ブランチが `source` なので [Commit to source] ですが、変更していない場合は [Commit to master] です.
![](/assets/github/desktop/13.png)

コミットされると何もないような画面に戻りますが、画面右上の [Fetch origin] が [Push origin] に なっています. ここをクリックすることで GitHub へ Push できます.
![](/assets/github/desktop/14.png)


## エディタを変更する
今回は Visual Studio Code しか入っていない環境だったため、エディタの選択が Visual Studio Code でしたが Atom も 入っている場合や、Shell 設定を変更したい場合があります.
メニューの [File] から [Options...] を クリックします.
Options ダイアログ の [Advanced] タブ から、変更できます.
![](/assets/github/desktop/99.png)

GitHub への Sign in アカウントの変更や、Git の Name/Email の 変更もこちからできます. 左下に Hubot アイコンが出ている場合は、こちらから Git の 設定を直します.



- - - -
以上。

なんか、クローンしてプッシュして終わりな感じですが、v1.0.0 では ここまでのようです. ブランチを作ったり切り替えたりできますが、Issues や Pull Request などは これからのようです.

まずは基本機能から. これからを楽しみにしたいと思います！
