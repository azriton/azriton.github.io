---
title: TypeScript の tsconfig.json を 考える - コンパイル・オプション編
date: 2017-09-10
comments: true
categories: 開発環境
tags:
- TypeScript
- Node.js
---

![](/assets/typescript/typescript.png "TypeScript")

[TypeScript の tsconfig.json について検討](/2017/09/07/TypeScriptのtsconfig.jsonを考える/) を 続けています. 今回はコンパイル・オプションについて考えます.

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4


## これまでの振り返り
[TypeScript で Hello World](/2017/09/04/Visual-Studio-CodeでHello-TypeScript！/) では、Visual Studio Code の [公式ドキュメント の tsconfig.json](https://code.visualstudio.com/docs/languages/typescript#_tsconfigjson) を基に作成し、最小限のコンパイル・オプションを考え、ECMAScript の 最新に合わせた形になりました.

```json
{
    "compilerOptions": {
        "target": "es2017",
        "module": "commonjs",
        "sourceMap": true
    }
}
```

続いて、[TypeScript の tsconfig.json を 考える](/2017/09/07/TypeScriptのtsconfig.jsonを考える/) では、コンパイル対象のソースファイルを指定する方法として `files` と `include/exclude` を 考え、とりあえず [TypeScript 公式ドキュメント の `include/exclude` サンプル](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#examples) を 使うことにしました.

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


## コンパイル・オプションの検討
コンパイル・オプションは最小限の設定しか見ていなかったので、しっかりと確認したいと思います.
TypeScript 公式ドキュメント の Compiler Options は [https://www.typescriptlang.org/docs/handbook/compiler-options.html](https://www.typescriptlang.org/docs/handbook/compiler-options.html) に なります.
ざっと 70個ぐらいのオプションがあるようで... とりあえず 使いそう or 要検討 なところをピックアップしたいと思います.

`tsc --init` で 作られる `tsconfig.json` が カテゴリー別に整理されているので、それに合わせて検討します.


### Basic Options
- `"target": "es2017"` は、前回検討の通り 最新の `es2017` にします.
- `"module": "commonjs"` は、実行環境の Node.js に 合わせて `commonjs` にします
- `"lib": []` は、コンパイルに含めるライブラリー、必要になったら都度追加します
- `"declaration": true` は、型定義ファイル `*.d.ts` を 生成するので使用します
- `"sourceMap": true` は、デバッガー等のツール連携で使用します
- `"outDir": "./dist"` は、コンパイル結果などの出力先ですが、命名に悩みつつ `distribution` で `dist` かな...
- `"removeComments": true` は、コンパイル後もコメントを保持する必要はないので、削除します.

※ `outDir` は 悩ましいところです. 一般的なルールが欲しい... GitHub を 見ていると `OutDir` や `out` が 多いよう感じました.


### Strict Type-Checking Options
- `"strict": true`
すべての strict タイプ・オプション(`noImplicitAny`, `strictNullChecks`, `noImplicitThis`, `alwaysStrict`) を 有効化するので使います.
このカテゴリは `strict` だけで賄えます.


### Additional Checks
基本厳しくチェックなので、すべて `true` に します.
- `"noUnusedLocals": true` は、未使用のローカル変数 を エラー報告します
- `"noUnusedParameters": true` は、未使用のパラメーター を エラー報告します
- `"noImplicitReturns": true` は、不正確な `return` を エラー報告します
- `"noFallthroughCasesInSwitch": true` は、 `case` 文 の フォールスルー を エラー報告します


### Module Resolution Options
おそらく `types` で パッケージを指定するぐらいです.


### Source Map Options
ソース・マッピングに関する設定を変更する場合に設定するようですが、使わなそうです.


### Experimental Options
実験用. とはいえ、 `experimentalDecorators` は 使いたいです.
Decorators を 使えるようにするもので、 2017年8月現在で [Stage 2](https://github.com/tc39/proposal-decorators) であり、今後仕様が変わるかもしれませんが、フレームワークなどで使ったりするので、 `true` に しておきます.


### その他
`tsc --init` で 出力されていないけど、設定した方がよさそうなもの.
- `"newLine": "LF"` は、デフォルトがプラットフォーム依存なので明示しておきます
- `"forceConsistentCasingInFileNames": true` は、ファイルの大文字小文字の違いをエラー報告します


## とりえずの設定
前回と合わせて、こんな感じでしょうか. まずは これで始めたいと思います.

```json
{
    "compilerOptions": {
        "target": "es2017",
        "module": "commonjs",
        "declaration": true,
        "sourceMap": true,
        "outDir": "./dist",
        "removeComments": true,
        "strict": true,
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noImplicitReturns": true,
        "noFallthroughCasesInSwitch": true,
        "experimentalDecorators": true,
        "newLine": "LF",
        "forceConsistentCasingInFileNames": true
    },
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
List で チェックするような項目もコンパイラーでやってくれるのですね. 差異が分からないので、とりあえず厳しく設定してみました.
ディレクトリ系は相変わらずの悩みどころです. どこかにべスプラ的は話がないかなぁ...
