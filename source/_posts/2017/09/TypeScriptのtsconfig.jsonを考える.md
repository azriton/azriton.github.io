---
title: TypeScript の tsconfig.json を 考える
date: 2017-09-07
updated: 2017-09-07
comments: true
categories: 開発環境
tags:
- TypeScript
- Node.js
---

![](/assets/typescript/typescript.png "TypeScript")

[TypeScript で Hello World](/2017/09/04/Visual-Studio-CodeでHello-TypeScript！/) が できたところで、TypeScript について深堀したいと思います. なにせ、初めて使うものですから... まず `tsconfig.json` に 何を設定しておいた方がいいのか、考えたいと思います.
今回の検討の前提として、TypeScript の コードはサーバーサイドで使うことを想定しています. ブラウザ内で動作する JavaScript は いったん考慮外としています. また既存のしがらみは無く、新しく作るものを想定しています.

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4


## 前回 Hello World の 設定を振り返る
前回の Hello World では Visual Studio Code の [公式ドキュメント の tsconfig.json](https://code.visualstudio.com/docs/languages/typescript#_tsconfigjson) を基に作成しました. まずは、この内容について振り返りをしたいと思います.

```json
{
    "compilerOptions": {
        "target": "es5",
        "module": "commonjs",
        "sourceMap": true
    }
}
```

設定したのはコンパイラーのオプションでだけになります. (よくトランスパイル/トランスパイラーって見聞きして真似てたけど、もしかしてコンパイル/コンパイラーが正しい？ ^^;)

`target` は、TypeScript を トランスパイルする先の、ECMAScript の ターゲット・バージョンになります. `es3` が デフォルトで、前回は `es5`  ECMAScript 5 に 設定していました. Hello World 的にはあまり関係はないのでサンプルのままです. 今後の設定としてはサーバーサイト前提なので 2017年8月現在 最新 の async/await が 使える `es2017` になります.

`module` は、モジュールコードの生成方法になります. Hello World では、もちろん関係ありません... 後々使っていく場合の設定値としては、サーバー再度用途で Node.js で 動かすので `commonjs` に設定したいと思います.

`sourceMap` は、 `.map` ファイルを生成するオプションです. `.map` ファイルがないとデバッガーはトランスパイルされた `.js` ファイルを参照するため、 `.ts` を 見ているエンジニア側とミスマッチが発生します. デバッガーなどのツールに`.map` ファイルを参照してもらうことで、 `.ts` ファイルを参照してもらうことができるので、ほぼ必須のオプションでしょう.


## tsconfig.json の 仕様について調査
つづいて `tsconfig.json` の 仕様はどうなっているのか、公式ドキュメント [https://www.typescriptlang.org/docs/handbook/tsconfig-json.html](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) から大筋を参照したいと思います.


### [Overview](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#overview) / [Using tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#using-tsconfigjson)
まず、 `tsconfig.json` がある ディレクトリを TypeScript プロジェクトのルートとして認識するとのことです. `tsc` を 実行する際に、 `tsconfig.json` を 関連度ディレクトリから親ディレクトリ方向に探していき使うか、 `-p (--project)` オプションで `tsconfig.json` の パスを指定します.


### [Examples](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#examples) / [Details](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#details)
`tsconfig.json` の 記述形式は `compilerOptions` プロパティに続き、 `files` プロパティ を使用する形式と、 `include/exclude` プロパティを使用する形式に分かれます.

`files` プロパティの使用例 (公式ドキュメントから抜粋)
```json
{
    "compilerOptions": { ... },
    "files": [
        "core.ts",
        "sys.ts",
        "types.ts"
    ]
}
```

`include/exclude` プロパティの使用例 (公式ドキュメントから抜粋)
```json
{
    "compilerOptions": { ... },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```

`compilerOptions` プロパティについては、省略した場合はコンパイラーのデフォルトが使われ、明示的に指定する場合は [Compiler Options](https://www.typescriptlang.org/docs/handbook/compiler-options.html) を 参照とのこと. 設定項目が多いので後日.

`files` プロパティは、相対または絶対のファイルパスのリストとのことで、たぶん使うのは面倒くさいので置いとき、 `include/exclude` プロパティを使うのがよいでしょう.

`include/exclude` プロパティは、glob のようなパターンで指定できます. glob、Linux などで `*.txt` の `*` のような "ワイルドカードでファイル名のセットを指定するパターンのこと - [グロブ - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%B0%E3%83%AD%E3%83%96)" とのことで、こっちで指定する方が便利です. 以下のワイルドカードからパターンを作ります.
- `*` ０個以上の文字
- `?` 任意の１文字
- `**/` 任意のサブディレクトリに再帰的にマッチ

`files` プロパティ と `include/exclude` プロパティ の 有無や、組み合わせなどの複雑なルールについての記載もあるけど、あまり頑張らないで簡単な構造にしたいのでパスします.


### [@types, typeRoots and types](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)
`@types` パッケージは、すべてコンパイルに含まれるとのこと. その制御については `compilerOptions` の `typeRoots` と `types` で できる.
全部入って問題ないような気もします. 使うとしたら、 `types` で パッケージ (e.g. node, lodash, express) を 明示するのでよさそうです.


### [Configuration inheritance with extends](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#configuration-inheritance-with-extends)
`tsconfig.json` は `extends` プロパティを使って別ファイルから構成を継承できます. ベース(継承元) の 設定がロードされて、継承している設定が上書きロードします.
ある程度の規模で複数プロジェクトに分けて構成する場合に便利なので使う機会は結構ありそうです. すぐには使う機会はなさそうですが、このような設定ができることを覚えておくと、後で便利そうです.


### [compileOnSave](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#compileonsave)
`compileOnSave` プロパティを設定すると、 `tsconfig.json` 保存時に すべてのファイルを再生成するように、この機能をサポートしている IDE へ 通知します.
`tsconfig.json` の 試行錯誤段階ではよさそうだけど、ある程度固まってきたら使わないのかもしれないです. ところで、今回使う Visual Studio Code の 記載がないが対応しているのだろうか？


### [Schema](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#schema)
スキーマは、ここだよ [http://json.schemastore.org/tsconfig](http://json.schemastore.org/tsconfig) って、ことで後で眺める.


### サマリ
`compilerOptions` を どう設定するかがメインになりそう.
後は `include/exclude` . これは、Java の [Maven](https://maven.apache.org/) のように 定番があると嬉しいのですが、サンプル以外に特に具体的なパターンはなかったです. あまり独自のスタイルを作るよりも、定番に乗っかっていきたいところ.


## TypeScript を 使っているプロジェクトを調査

### [Microsoft/TypeScript](https://github.com/Microsoft/TypeScript/)
TypeScript 自身のリポジトリ. テストケース用なのか `tests` ディレクトリ以下にかなりの `tsconfig.json` が あります.
その中から `src` 以下をいくつか参照したところ、`/src/tsconfig-base.json` を 継承して作られているようで、こちらは `compilerOptions` だけなので、後日参照.
継承してる側は `files` プロパティで列挙でした.


### [Microsoft/vscode](https://github.com/Microsoft/vscode)
Visual Studio Code の ベースとなっているリポジトリ. (製品版とは異なるらしい → [Visual Studio Code - Wikipedia](https://ja.wikipedia.org/wiki/Visual_Studio_Code))
`src/tsconfig.json` が あり、また `extensions/` 以下に各拡張機能用の `tsconfig.json` があります. 継承関係は無いです..

`/build/tsconfig.json` では `"node_modules/**"` を`exclue` 、拡張機能系はファイルが分かれているが大体同じで、 `"src/**/*"` の `include` でした.


## 今のところの設定
`compilerOptions` は 改めて調査するとして、今までのところで作ると以下のような感じでしょうか. って、結局 公式ドキュメントのサンプル...

```json
{
    "compilerOptions": { ... },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4822298973" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51bLLkWaH4L._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4822298973" target="_blank" >TypeScript実践マスター</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">古賀慎一 日経BP社 2017-12-12    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4822298973" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F15269698%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-82-229897-5%2520%257C%25204-822-29897-5%2520%257C%25204-8222-9897-5%2520%257C%25204-82229-897-5%2520%257C%25204-822298-97-5%2520%257C%25204-8222989-7-5" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0733113NK" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51UE0ypcmKL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0733113NK" target="_blank" >速習TypeScript: altJSのデファクトスタンダートを素早く学ぶ！ 速習シリーズ[Kindle版]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">山田祥寛 WINGSプロジェクト 2017-06-21    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0733113NK%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div>                                                  </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
プロジェクトのディレクトリ構造にかかわる設定なので、いろいろと悩んでしまいます. とりあえず公式ドキュメントのサンプルに則って定義しておきたいと思います.
使い込んでみてわかることなのでしょう. きっと.
