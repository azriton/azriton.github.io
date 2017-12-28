---
title: Visual Studio Code で Hello Python3！
date: 2017-08-29
updated: 2017-08-29
comments: true
categories: 開発環境
tags:
- Visual Studio Code
- Python
---

![](/assets/vscode/visual-studio-code.png "Visual Studio Code")

ようやく [Visual Studio Code の 準備](/2017/08/26/Visual-Studio-Code最初の設定変更/)ができました！ さっそく Python の コーディングに入りたいと思います. まずは基本の Hello World から！

**作業環境**
- Windows 10 64bit
- Python 3.6 64bit
- Visual Studio Code


## Python サポート の 拡張機能を追加
Visual Studio Code で Python の コード補完や、Lint、デバッグ を 行うには、Pyhton サポート の 拡張機能を追加します.
Python サポート の 拡張機能は [かなりの数](https://marketplace.visualstudio.com/search?term=tag%3Apython&target=VSCode&category=All%20categories&sortBy=Relevance) 登録されています. その中から、今回は [Don Jayamanne さん の 拡張機能](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) を 使わせていただくことにしました.
レーティングが高く、インストール数も多く、また [Visual Studio Code の 公式ドキュメントで紹介](https://code.visualstudio.com/docs/languages/python)されていること、「ようこそ」画面で推薦されていることなどからの選定となります. Thank you for the great extension!, Don Jayamanne-san!!

Visual Studio Code を 起動し「ようこそ」画面から、[カスタマイズする] - [ツールと言語] - [Python] を クリックします.
※ 「ようこそ」 が 表示されない場合は、メニュー の [ヘルプ] - [ようこそ] を 選択します
![](/assets/vscode/python/01.png)

「Python に追加サポートをインストールしたあと、ウィンドウが再読み込みされます.」 が 表示されるので [OK] を クリックします.
![](/assets/vscode/python/02.png)

Visual Studio Code が 再起動し、「ようこそ」の Python の 色が変わり Python サポートがインストールされました.
![](/assets/vscode/python/03.png)

また、`Ctrl + Shift + X` で 拡張機能を表示するとインストール済みに Python が 追加されていることからも確認できます.
![](/assets/vscode/python/04.png)


## プロジェクト・フォルダー の 作成 と 展開
さっそくプロジェクト用のフォルダーを作成し開きます. `Ctrl + K` `Ctrl + O` から フォルダー選択ダイアログを表示してもよいのですが、今回はフォルダーを作成する必要があるので "Being able to keep your hands on the keyboard when writing code is crucial for high productivity. VS Code has a rich set of default keyboard shortcuts as well as allowing you to customize them. - Basic Editing in Visual Studio Code - [Keyboard shortcuts](https://code.visualstudio.com/docs/editor/codebasics#_keyboard-shortcuts)" にならい、統合コンソールから開くことにします.

`Ctrl + @` で 統合コンソールを表示. 下記コマンドを実行しフォルダーの作成 と Visual Studio Code で フォルダーを開きます. 今回は `C:\Develop\workspace\hello-python3` に フォルダーを作成しました.
```console
PS C:\Users\username> mkdir C:\Develop\workspace\hello-python3
PS C:\Users\username> code C:\Develop\workspace\hello-python3\
```
![](/assets/vscode/python/05.png)


## Python の 仮想環境 を 作成
Hello World なので、仮想環境を作成するほどでは無いのですが、Visual Studio Code の 設定確認もかねて.
`Ctrl + @` で 統合コンソールを表示し、 `venv` を 実行します. フォルダーを開いているので、カレント・フォルダーが自動的にプロジェクト用のフォルダになっています.
```console
PS C:\Develop\workspace\hello-python3> python -m venv venv
```

Visual Studio Code で 仮想環境を使うように設定します.
`Ctrl + Shift + P` で コマンドパレット、`interp` と 入力して、[Python: Select Workspace Interpreter] を 選択します.
続いて利用可能な `python.exe` の 候補が出るので、 `venv - python.exe` を 選択します.
![](/assets/vscode/python/06.png)
![](/assets/vscode/python/07.png)

続いて新しいファイルを作成します. `Ctrl + N` でもよいのですが、Visual Studio Code っぽく `Ctrl + Shift + P` で コマンドパレット、`new file` を 入力して、Visual Studio Code の エクスプローラーに直接ファイルを追加します. ファイル名は `hello.py` としました.
![](/assets/vscode/python/08.png)

ファイル名を確定すると「Linter pylint is not installed」の エラーが表示されます. [Install pylint] を クリックしてインストールします.
![](/assets/vscode/python/09.png)

新しい統合ターミナルが起動し Pylint が インストールされます. 先ほど仮想環境を選択しているので実行する `python.exe` が `venv` の ものになっています. インストールできたら、統合ターミナルは不要なので `Ctrl + @` で 閉じておきます.
![](/assets/vscode/python/10.png)


## Python コーディング
単純な Hello World ならぬ Hello Python3！ なので `print('Hello Python3！')` 以上、なのですが Visual Studio Code を 使うからには入力補完を使うようなコードにしたいところです. ちょっと回りくどいですが `main()` から `hello()` を 呼ぶような構造にしました.

```python
def hello(name):
    print('Hello ' + name)

def main():
    hello('Python3！')

if __name__ == '__main__':
    main()
```

では早速. `d` と タイプすると入力補完が即時立ち上がりました！ (間違えて消えてしまったら `Ctrl + Space` )
いくつかの候補があり迷いますが、コード・スニペット の `def` を 選択します.
![](/assets/vscode/python/11.png)

ファンクション定義のスニペットが展開されました. `funcname` に フォーカスが当たっているので、ファンクション名 `hello` を 入力し、 `Tab` キーを押下します.
![](/assets/vscode/python/12.png)

`parameter_list` へ フォーカスが移るので、今回のパラメーター `name` を 入力し、 `Tab` キーを押下します.
![](/assets/vscode/python/13.png)

ファンクションのボディに展開されていた `pass` へ フォーカスが当たるので、 `print`文 を 入力します.
![](/assets/vscode/python/14.png)

`print` は `print` の 文字列の入力補完が効きますが括弧はつかないようです. また、 `name` は パラメーター定義があるのを見てくれて入力補完のリストが表示されます.
![](/assets/vscode/python/15.png)

`print` 文のパラメーター `'Hello ' + name` まで入力し、 `Ctrl + Enter` すると行の途中(今回は閉じ括弧のが残っている状態)でも、残りを改行せずにカーソルだけ次行へ改行してくれます. `Ctrl + S` で ファイルを保存します.
`def` に 警告が表示されています. 「[pylint] C0111:Missing module docstring」と「[pylint] C0111:Missing function docstring」ですね...
マウス・カーソルを警告が出ている波線にのせるほか、 `Ctrl + Shift + M` で 問題ビューを表示できます.
![](/assets/vscode/python/16.png)

とりあえず先に進んで、 `main()` を 実装します.
`main()` は、そのあとの `if __name__ == '__main__':` と ペアになります. そのため `if` 文の入力補完を期待して `if` から入力し、 `if(main)` の 入力補完を選択します.
![](/assets/vscode/python/17.png)

期待通りの `main()` と `if __name__ == '__main__':` を 補完してくれました！ `pass` に フォーカスが当たっているので `hello('Python3！')` を 入力します.
![](/assets/vscode/python/18.png)

実装できたので `Ctrl + S` で 保存します. 警告 を すべて無くしてから完成ですが、今回は Visual Studio Code で Python を 実行することが目的で、コードは残さないので、このまま進めます.
![](/assets/vscode/python/19.png)


## Python プログラム の 実行
まずは Python 拡張機能 の [Python: Run Python File in Terminal] から実行してみます. `Ctrl + Shift + P` で コマンドパレットを表示、 `Python run` と 入力して絞り込みます.
![](/assets/vscode/python/20.png)

[Python: Run Python File in Terminal] を 選択し、実行します.
![](/assets/vscode/python/21.png)


## その他 - venv 仮想環境 の python.exe の パス
`venv` の 仮想環境 `python.exe` の パスは `.vscode\settings.json` の `"python.pythonPath"` に フル・パスで記述されています. `.vscode\settings.json` を Git などへコミットし共有する場合は、絶対パスか`"${workspaceRoot}/venv/scripts/python.exe"` のように、プロジェクトのディレクトリをさす `${workspaceRoot}` に 置き換えておきます. 詳細は こちら [Relative Paths to Python Interpreter - Python Path and Version · DonJayamanne/pythonVSCode Wiki](https://github.com/DonJayamanne/pythonVSCode/wiki/Python-Path-and-Version#relative-paths-to-python-interpreter)
![](/assets/vscode/python/98.png).


## その他 - 統合ターミナルから venv 仮想環境 の 実行
`Ctrl + @` で 統合ターミナルを起動し、そこから Python および 関連コマンド(e.g. pip)を実行する場合、 venv を 有効化する必要があります. この場合は [Python: Select Workspace Interpreter] が 効いていないので注意が必要です.
```console
PS C:\Develop\workspace\hello-python3> Set-ExecutionPolicy RemoteSigned -Scope Process
PS C:\Develop\workspace\hello-python3> .\venv\Scripts\Activate.ps1
(venv) PS C:\Develop\workspace\hello-python3>
```
![](/assets/vscode/python/99.png)


<a href="//af.moshimo.com/af/c/click?a_id=871746&p_id=1296&pc_id=2120&pl_id=19703&guid=ON" target="_blank" rel="nofollow"><img src="//image.moshimo.com/af-img/0453/000000019703.jpg" width="300" height="250" style="border:none;"></a><img src="//i.moshimo.com/af/i/impression?a_id=871746&p_id=1296&pc_id=2120&pl_id=19703" width="1" height="1" style="border:none;">



- - - -
主にクロール系の処理になるので、こちらで勉強しています.
最初の言語として勉強するための本には向いていませんが、Python の 言語使用の話もあるので他の言語を知ってる場合には、これ１冊でも十分ではないでしょうか.
またクローラーを作るという観点では、Python の 基本機能だけではなく Shell や Python の 外部ライブラリを使ったりと、バリエーションを持たせて解説があるので とても分かりやすいほか、法律に関する話などもあるのが素晴らしいです！
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51MK3rXBRRL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P" target="_blank" >Pythonクローリング＆スクレイピング ―データ収集・解析のための実践開発ガイド―[Kindle版]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">加藤 耕太 技術評論社 2016-12-16    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01NGWKE0P%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774183679%2F" target="_blank" >Amazon[書籍版]で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div>                                                  </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
Visual Studio Code で Hello World が できました！
これで Python プログラミングが始められます. 何を作ろうかなぁ～
