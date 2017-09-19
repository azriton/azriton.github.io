---
title: TypeScript の tslint.json を 考える
date: 2017-09-19
comments: true
categories: 開発環境
tags:
- TypeScript
- Node.js
---

![](/images/typescript/typescript.png "TypeScript")

[TSLint について調べた](/2017/09/16/TypeScriptのtslint.jsonを調べる/) ので、実際の設定を考えます.

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4
- TSLint 5.6.0


## 設定の方針検討
TypeScript は 学習中のため、設定の良し悪しが分からないところがありますが、まずは厳しくシメテおくのが良いと考えます.
この手のチェックは、後から緩めることは簡単ですが、後から厳しくすると指摘の嵐に見舞われ結局使わないといったことにもなりかねません. まずは厳しく、どうしても指摘事項に対する代替策が無かった場合に緩めるといった運用にします.


## 設定しなかったもの
以下の ２つは設定しませんでした. 今のところ禁止するものが 無い and/or 想像がつかない ので、設定するべき値がないためのとなります. (デプリケートではないもので、禁止した方が良いものってあるのかなぁ)
- [`ban-types`](): 特定の型を使用禁止にする
- [`ban`](): 特定の関数やグローバル・メソッドを禁止する


## 今回の設定値
以下にような設定 `tslint.json` を 作りました. (行数削減のため一部整形済み)
```json
{
    "rules": {
        "adjacent-overload-signatures": true,
        "member-access": true,
        "member-ordering": [ true, { "order": [ "static-field", "instance-field", "constructor" ]}],
        "no-any": true,
        "no-empty-interface": true,
        "no-import-side-effect": true,
        "no-inferrable-types": true,
        "no-internal-module": true,
        "no-magic-numbers": true,
        "no-namespace": true,
        "no-non-null-assertion": true,
        "no-reference": true,
        "no-unnecessary-type-assertion": true,
        "no-var-requires": true,
        "only-arrow-functions": [ true ],
        "prefer-for-of": true,
        "promise-function-async": true,
        "typedef": [
            true,
            "call-signature",
            "arrow-call-signature",
            "parameter",
            "arrow-parameter",
            "property-declaration",
            "variable-declaration",
            "member-variable-declaration",
            "object-destructuring",
            "array-destructuring"
        ],
        "typedef-whitespace": [
            true,
            { "call-signature": "nospace", "index-signature": "nospace", "parameter": "nospace", "property-declaration": "nospace", "variable-declaration": "nospace" },
            { "call-signature": "onespace", "index-signature": "onespace", "parameter": "onespace", "property-declaration": "onespace", "variable-declaration": "onespace" }
        ],
        "unified-signatures": true,
        "await-promise": true,
        "curly": [ true, "ignore-same-line" ],
        "forin": true,
        "import-blacklist": true,
        "label-position": true,
        "no-arg": true,
        "no-bitwise": true,
        "no-conditional-assignment": true,
        "no-console": [ "debug", "error", "info", "trace", "warn" ],
        "no-construct": true,
        "no-debugger": true,
        "no-duplicate-super": true,
        "no-duplicate-variable": [ true, "check-parameters" ],
        "no-empty": true,
        "no-eval": true,
        "no-floating-promises": true,
        "no-for-in-array": true,
        "no-inferred-empty-object-type": true,
        "no-invalid-template-strings": true,
        "no-invalid-this": [ true, "check-function-in-method" ],
        "no-misused-new": true,
        "no-null-keyword": true,
        "no-object-literal-type-assertion": true,
        "no-shadowed-variable": true,
        "no-sparse-arrays": true,
        "no-string-literal": true,
        "no-string-throw": true,
        "no-submodule-imports": true,
        "no-switch-case-fall-through": true,
        "no-this-assignment": true,
        "no-unbound-method": true,
        "no-unsafe-any": true,
        "no-unsafe-finally": true,
        "no-unused-expression": true,
        "no-unused-variable": true,
        "no-use-before-declare": true,
        "no-var-keyword": true,
        "no-void-expression": [ true, "ignore-arrow-function-shorthand" ],
        "prefer-conditional-expression": [ true, "check-else-if" ],
        "prefer-object-spread": true,
        "radix": true,
        "restrict-plus-operands": true,
        "strict-boolean-expressions": true,
        "strict-type-predicates": true,
        "switch-default": true,
        "triple-equals": true,
        "typeof-compare": true,
        "use-default-type-parameter": true,
        "use-isnan": true,
        "cyclomatic-complexity": [ true, 6 ],
        "deprecation": true,
        "eofline": true,
        "indent": [ true, "spaces", 4 ],
        "linebreak-style": [ true, "LF" ],
        "max-classes-per-file": [ true, 1 ],
        "max-file-line-count": [ true, 300 ],
        "max-line-length": [ true, 120 ],
        "no-default-export": true,
        "no-duplicate-imports": true,
        "no-mergeable-namespace": true,
        "no-require-imports": true,
        "object-literal-sort-keys": true,
        "prefer-const": true,
        "trailing-comma": [ true, { "multiline": "never", "singleline": "never" }],
        "align": [ true, "parameters", "arguments", "statements", "members", "elements" ],
        "array-type": [ true, "array" ],
        "arrow-parens": [ true, "ban-single-arg-parens" ],
        "arrow-return-shorthand": [ true, "multiline" ],
        "binary-expression-operand-order": true,
        "callable-types": true,
        "class-name": true,
        "comment-format": [ true, "check-space", "check-uppercase" ],
        "completed-docs": [ true ],
        "encoding": true,
        "file-header": [ true, "Copyright \\d{4}" ],
        "import-spacing": true,
        "interface-name": [ true, "never-prefix" ],
        "interface-over-type-literal": true,
        "jsdoc-format": true,
        "match-default-export-name": true,
        "newline-before-return": true,
        "new-parens": true,
        "no-angle-bracket-type-assertion": true,
        "no-boolean-literal-compare": true,
        "no-consecutive-blank-lines": [ true, 2 ],
        "no-irregular-whitespace": true,
        "no-parameter-properties": true,
        "no-reference-import": true,
        "no-trailing-whitespace": true,
        "no-unnecessary-callback-wrapper": true,
        "no-unnecessary-initializer": true,
        "no-unnecessary-qualifier": true,
        "number-literal-format": true,
        "object-literal-key-quotes": [ true, "always" ],
        "object-literal-shorthand": true,
        "one-line": [ true, "check-catch", "check-finally", "check-else", "check-open-brace", "check-whitespace" ],
        "one-variable-per-declaration": [ true, "ignore-for-loop" ],
        "ordered-imports": true,
        "prefer-function-over-method": true,
        "prefer-method-signature": true,
        "prefer-switch": true,
        "prefer-template": [ true, "allow-single-concat" ],
        "quotemark": [ true, "single", "avoid-template", "avoid-escape" ],
        "return-undefined": true,
        "semicolon": [ true, "always" ],
        "space-before-function-paren": [ true, "never" ],
        "space-within-parens": 0,
        "switch-final-break": [ true, "always" ],
        "type-literal-delimiter": true,
        "variable-name": [ true, "check-format", "ban-keywords" ],
        "whitespace": [ true, "check-branch", "check-decl", "check-operator", "check-module", "check-separator", "check-type", "check-typecast", "check-preblock" ]
    },
    "defaultSeverity": "error"
}
```


## とりあえず、 Hello World に TSLint かけてみる
[Visual Studio Code で Hello TypeScript！](/2017/09/04/Visual-Studio-CodeでHello-TypeScript！/) で作ったコードに TSLint を かけてみます.
作成したコードは以下になります. すでに引っかかることが目に見えていますが、ただの実験コードなので気にせずかけます.
```typescript
class Startup {
    public static main(): number {
        console.log('Hello TypeScript！');
        return 0;
    }
}

Startup.main();
```

コマンドラインで実行するには、 `tslint -p` で `tsconfig.json` を 指定します.
今回は `compilerOptions` に `strictNullChecks: true` が 無いので警告されています. また案の定 JSDoc などのドキュメンテーションが引っかかりました.
`Missing blank line before return` も 出ているので、コードのチェックもしてくれていることが分かります.

```console
PS C:\Develop\workspace\hello-typescript> tslint -p .\tslint.json
strict-type-predicates does not work without --strictNullChecks

ERROR: C:/Develop/workspace/hello-typescript/src/hello.ts[1, 1]: Documentation must exist for classes.
ERROR: C:/Develop/workspace/hello-typescript/src/hello.ts[2, 5]: Documentation must exist for public,static methods.
ERROR: C:/Develop/workspace/hello-typescript/src/hello.ts[1, 1]: missing file header
ERROR: C:/Develop/workspace/hello-typescript/src/hello.ts[4, 9]: Missing blank line before return
```

Visual Studio Code では、以下のようにチェック結果がエディタに範囲されています.
`Documentation` の チェックが反映されていないようですね...
![](/images/vscode/typescript/13.png)



- - - -
一通りチェックするような設定ができました！
これで、良いプラクティスを反映した文法でコーディングできるので、作るべきことに集中できますし、レビューの際もロジックなどに見るべき場所に注力できますね.

Visual Studio Code で チェックできてないものがありそうなのは、参照しているスキーマが、公式サイトのサンプルと若干ずれているようで、 `tslint.json` にも警告が出ていました.
後ほど詳しく調べるとして、とりあえず CI と 組み合わせて、コマンドライン実行の Lint も かけることで、ダブルチェックで逃げることにします.

あとは感覚的にしっくりくる設定なのかガッツりコーディングしてブラッシュアップ、いざっコーディング！
