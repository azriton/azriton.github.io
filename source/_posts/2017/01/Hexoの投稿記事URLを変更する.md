---
title: Hexo の 投稿記事 URL を 変更する
date: 2017-01-09
updated: 2017-01-09
comments: true
categories: ブログ
tags:
  - Hexo
---

![](/assets/hexo/hexo-3.2.png "Hexo")

Hexo の 投稿記事 の URL は、タイトルが使われます. その際に半角スペースをハイフン(-) に 置き換えてくれるのですが、私は日本語とアルファベットが混ざる際に半角スペースを入れてレイアウトを調整します. そうなると URL 日本語なのにハイフンだらけとなってしまいます. 昔のブラウザは URL エンコードされた文字列だったので気にもならなかったのですが、最近のブラウザはでコードして日本語で表示してくれるので気になってしまうので、極力 ハイフンなし の URL に 戻したいと思います.

**作業環境**
- Windows 7
- Hexo 3.2
- hexo-generator-alias 0.1.3


## 記事のファイル名を変更する
URL を 変更するには、各投稿記事のファイル名を変更し再生成するだけです. 簡単！
アルファベットの区切りとしての半角スペース(＝ハイフン) は よいので、日本語とアルファベットの間でレイアウト調整した分だけ、ハイフンを消してファイル名変更します.

*メモ*
ファイル名を変更するということは、古いファイルを消して、新しいファイルを作ることなのですが、`hexo generate` は 生成済みのファイルの削除は行ってくれないようです. 基本的に投稿を削除するということはないので通常はそれでよいのですが、今回のようにファイル名変更した場合は `hexo clean` して生成済みのファイルを削除してから `hexo generate` する必要があります.


## 古い URL からのリダイレクトを設定
ファイル名を変更することで URL を 変更できましたが、古い URL へのケアが必要です. リンクしてくださっている サイト や SNS の 投稿などがありましたら `404 Not Found` に なってしまいますし、Google などの 検索エンジンからは重複記事と見えてしまいます. そのようなことにならないよう、古い URL に リダイレクトの設定をしておきます.

Hexo で リダイレクト設定するには、[hexo-generator-alias](https://github.com/hexojs/hexo-generator-alias) を 使います.
Hexo の ソースがあるフォルダで `npm install hexo-generator-alias --save` を実行します. ついでに各 Plugin の アップデートもしておきます. (下記例の [username] は 自分の GitHub ユーザ名)
```shell-session
C:\Develop\repos\[username].github.io> npm update
C:\Develop\repos\[username].github.io> npm install hexo-generator-alias --save
`-- hexo-generator-alias@0.1.3
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.17: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
```

リダイレクトの設定 は 各投稿のファイル もしくは、`_config.yml` で 行います.
`_config.yml` は 全体設定を置いておきたいので、今回は各投稿のファイルに書きます. たとえば本ブログの最初の記事の例で、以下のように Front-matter へ `alias: [古い URL]` を 追加します.
```yaml
---
title: Hexo と GitHub Pages で ブログ環境の構築
date: 2016-11-01
categories: ブログ
tags:
- Hexo
alias: /2016/11/01/Hexo-と-GitHub-Pages-で-ブログ環境の構築/
---
```

対象となる投稿記事の設定が終わったら、`hexo clean`, `hexo generate` で 再生成します.
これにより公開フォルダが以下のように生成され、古い URL にも `index.html` が 生成されています.
```shell-session
c:\Develop\repos\personal\[username].github.io>tree /F
C:.
├─.deploy_git
├─.settings
├─node_modules
├─public
│  ├─2016
│  │  ├─11
│  │  │  ├─01
│  │  │  │  ├─Hexo-と-GitHub-Pages-で-ブログ環境の構築
│  │  │  │  │      index.html
│  │  │  │  └─HexoとGitHub-Pagesでブログ環境の構築
│  │  │  │          index.html
│
├─scaffolds
├─source
└─themes
```

古い URL の `index.html` は、以下のようにリダイレクトのソースが生成されています. (実際は 1行、投稿用に整形済み)
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Redirecting...</title>
    <link rel="canonical" href="/2016/11/01/HexoとGitHub-Pagesでブログ環境の構築/">
    <meta http-equiv="refresh" content="0; url=/2016/11/01/HexoとGitHub-Pagesでブログ環境の構築/">
</head>
</html>
```



- - - -
URL の 変更を無事に行うことができました. 最初からちゃんと URL 構成を考えておけば、こんなことにならなかったわけですが、まぁ仕方ない. エイリアス設定の勉強ができたのでよしとしましょう.

ところで本件の原因となった、全角と半角が混ざる際に半角スペースを入れたくなる問題、本当は CSS なりのスタイル側で処理してくれるとよいもので、文章上の意味を持たないのにレイアウトとして半角スペースを入れるのはよくないのかなぁとは考えています...
とはいえ現状はスタイル側で対応できないので致し方なし、といういうのと対比となる文の場合で強調するほどでもないときに半角スペースを入れることで、自然な強調にできるのでよく使っていたりもします.

このあたり、みなさん いろいろと苦労されていらっしゃるようで、私もよい解があったら ぜひ乗りたいなぁ.
- [「全角文字と半角英字の間にはスペースを入れるが全角文字と半角数字の間には入れない」というルール - IT翻訳者の疑問](http://d.hatena.ne.jp/jacquelinet/20090422/p1)
- [半角スペース入れてますか？ - portal shit!](https://portalshit.net/2007/01/13/732)
- [日本語とアルファベットとの間に半角スペースを入れるか否か。 :: キミガタメ「ハ」](http://blog.livedoor.jp/tanahata/archives/50970576.html)
- [論文における日本語と英語の混在ルール - 臨床心理士的Blogger](http://a-clinical-psychologist.blogspot.jp/2014/10/blog-post.html)
