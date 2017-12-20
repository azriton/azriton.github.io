---
title: TypeScript の tslint.json を 調べる
date: 2017-09-16
comments: true
categories: 開発環境
tags:
- TypeScript
- Node.js
---

![](/assets/typescript/typescript.png "TypeScript")

[TypeScript の tsconfig.json が とりあえずできました.](/2017/09/10/TypeScriptのtsconfig.jsonを考える-コンパイル・オプション編/) 次は静的解析ツールの TSLint を 設定する tslint.json の 設定内容について検討します.

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4
- TSLint 5.6.0


## TSLint とは？
[TSLint](https://palantir.github.io/tslint/) は プログラムを動作させずに解析し問題になりそうなコードや規約違反などをチェックする TypeScript 向けの静的解析ツールです. JavaScript では [ESLint](https://eslint.org/)、Java では [SpotBugs](https://spotbugs.github.io/)(FindBugs) ＆ [Checkstyle](http://checkstyle.sourceforge.net/) などが同種のツールにあたります.


## 設定項目の確認
[TSLint core rules](https://palantir.github.io/tslint/rules/) を 確認し、ざっと理解のためのメモを作りました.
英語ができないのと、TypeScript/TSLint に 詳しくない状態で書いているので、間違えや勘違いがあるかもしれないでご注意ください...
英文のままになっているところは、よくわからなかった and/or 日本語で表現できなかった ので、とりあえず そのまま転記しました. いつの日かアップデートできるように頑張りたい.


### [TypeScript-specific](https://palantir.github.io/tslint/rules/#typescript-specific)

| ルール                        | 概要 |
|:------------------------------|:-----|
| adjacent-overload-signatures  | 関数のオーバーロードは連続して記述し、可読性を向上させる |
| ban-types                     | 特定の型を使用禁止にする |
| member-access                 | クラス・メンバーの可視性宣言を強制させる |
| member-ordering               | クラス・メンバーの順序付けを強制し、可読性を向上させる |
| no-any                        | `any` の 型宣言を使用禁止にする |
| no-empty-interface            | 空のインターフェースを禁止する |
| no-import-side-effect         | 副作用を伴う `import` を 禁止する |
| no-inferrable-types           | `number` `string` `boolean` の 初期化済みの型宣言を禁止し冗長なコードを排除させる |
| no-internal-module            | 内部 `module` を 禁止し、新しい `namespace` キーワードの利用を使用させる |
| no-magic-numbers              | マジックナンバーを禁止する (ただしデフォルトでは `-1` `0` `1` は 許可) |
| no-namespace                  | 内部 `module` と`namespace` を 禁止し、 ES6-style の `import/export` を使用させる |
| no-non-null-assertion         | non-null アサーションを禁止し、strict null checking mode を 活用させる |
| no-reference                  | `/// <reference path=>` を 禁止し、ES6-style の `import/export` を使用させる |
| no-unnecessary-type-assertion | 型アサーションが、式の型を変更しない場合に警告する |
| no-var-requires               | `import` 以外の `require` を 禁止し、ES6-style の `import/export` を使用させる |
| only-arrow-functions          | Arrow-style 以外の `function` 関数式を禁止し、予期しない `this` アクセスを防止する |
| prefer-for-of                 | 配列のインデックスにアクセスしないループの場合は、 `for-of` を 使用させる |
| promise-function-async        | `Promise` を 返す関数またはメソッドは `async` の マークを必須とする |
| typedef                       | 型定義を必須とする |
| typedef-whitespace            | 型指定子(コロン) の 前後のスペース有無をチェックします |
| unified-signatures            | `union` または `optional/rest` で１つに統合できるオーバーロードを警告する |


### [Functionality](https://palantir.github.io/tslint/rules/#functionality)

| ルール                           | 概要 |
|:---------------------------------|:-----|
| await-promise                    | `Promise` ではない `awaited` な 値を警告する |
| ban                              | 特定の関数やグローバル・メソッドを禁止する |
| curly                            | `if/for/do/while` で 中括弧を必須とする |
| forin                            | `for-in` の `if` フィルターを必須とし、継承プロパティへの偶発アクセスを防止する |
| import-blacklist                 | 直接 `import` `require` せず、サブモジュール・ロードにして不要なロードを避ける |
| label-position                   | ラベルの使用を `do/for/while/switch` に限定し、コードの構造化を強化する |
| no-arg                           | `arguments.callee` を 禁止し、パフォーマンスの最適化を行えるようにする |
| no-bitwise                       | ビット演算子を禁止し、保守性を向上させる |
| no-conditional-assignment        | `do-while/for/if/while` での条件文における代入を禁止する |
| no-console                       | 指定するコンソール・メソッド (e.g. `log` `error`) の 仕様を禁止する |
| no-construct                     | `String` `Number` `Boolean` の コンストラクタ利用を禁止し、関数を利用させる |
| no-debugger                      | `debugger` ステートメントを禁止する |
| no-duplicate-super               | コンストラクタでの `super()` 二重呼び出しを警告する |
| no-duplicate-variable            | 同一スコープでの `var` 重複宣言を禁止する |
| no-empty                         | 空ブロックを禁止する |
| no-eval                          | `eval` を 禁止し、危険なコードを防止させる |
| no-floating-promises             | 関数から返された `Promise` の ハンドルを厳格にさせる |
| no-for-in-array                  | 配列の `for-in` ループを禁止し `for-of` を 利用させる |
| no-inferred-empty-object-type    | 関数とコンストラクタの呼出し側で、 `{}` (空オブジェクト型)による型推論を禁止する |
| no-invalid-template-strings      | テンプレート文字列以外 の `${` を 警告する |
| no-invalid-this                  | クラス外での `this` キーワードの使用を禁止する |
| no-misused-new                   | *Warns on apparent attempts to define constructors for interfaces or `new` for classes.* |
| no-null-keyword                  | `null` の 使用を禁止し、 `undefined` に 統一させる |
| no-object-literal-type-assertion | インターフェースや |
| no-shadowed-variable             | 変数のシャドーイングを禁止する |
| no-sparse-arrays                 | 配列リテラルの欠落要素(重複カンマ)を禁止しする |
| no-string-literal                | 不要な文字列リテラルのプロパティアクセスを禁止し `obj.property` 書式を使用させる |
| no-string-throw                  | プレーン・テキストの `throw` を 禁止し、 `Error` の スローを使用させる |
| no-submodule-imports             | サブモジュールのインポートを禁止し、最上位パッケージのエクスポートを使用させる |
| no-switch-case-fall-through      | `switch` の ケース・フォールスルーをきん資する |
| no-this-assignment               | 不要な `this` 参照を禁止し、Arrow-style ラムダを使用させる |
| no-unbound-method                | メソッドがメソッド呼び出しの外で使用されることを警告する |
| no-unsafe-any                    | 動的な方法の `any` 型 利用を警告する |
| no-unsafe-finally                | `finally` ブロックでの `return` `continue` `break` `throws` の 使用を禁止する |
| no-unused-expression             | 未使用の式ステートメントを禁止する |
| no-unused-variable               | 未使用 の インポート、変数、関数、プライベート・クラスのメンバー を 禁止する |
| no-use-before-declare            | 変数の宣言前利用を禁止する |
| no-var-keyword                   | `var` を 禁止し、 `let` と `const` を 使用させる |
| no-void-expression               | *Requires expressions of type `void` to appear in statement position.* |
| prefer-conditional-expression    | *Recommends to use a conditional expression instead of assigning to the same thing in each branch of an if statement.* |
| prefer-object-spread             | *Enforces the use of the ES2015 object spread operator over `Object.assign()` where appropriate.* |
| radix                            | `parseInt` を 使う場合に、 `radix` パラメータの指定を必須とする |
| restrict-plus-operands           | 変数の加算は双方の型が同じ(`number` + `string` は禁止) であることを強制する |
| strict-boolean-expressions       | ブール式で許可する型を制限する (デフォルトは `boolean` のみ) |
| strict-type-predicates           | 常に `true` もしくは `false` となる型述語を警告する |
| switch-default                   | `switch` 文は、 `default` ケース の 実装を必須とする |
| triple-equals                    | `==` と `!=` を 禁止し、 `===` と `!==` を 使用させる |
| typeof-compare                   | *Makes sure result of `typeof` is compared to correct string values.* |
| use-default-type-parameter       | 明示的に指定された型引数が、その型パラメータのデフォルトである場合に警告する |
| use-isnan                        | `NaN` 定数の比較を禁止し、 `isNaN()` 関数を使用させる |


### [Maintainability](https://palantir.github.io/tslint/rules/#maintainability)

| ルール                   | 概要 |
|:-------------------------|:-----|
| cyclomatic-complexity    | サイクロマティック複雑度の分析と閾値を適用する |
| deprecation              | デプリケート API の 使用を警告する |
| eofline                  | ファイルが改行で終わるようにする |
| indent                   | 指定するインデントルールを適用する |
| linebreak-style          | 指定する改行コードを適用する |
| max-classes-per-file     | ファイル内のクラス数を制限する |
| max-file-line-count      | ファイルの行数を制限する |
| max-line-length          | １行の文字数を制限する |
| no-default-export        | ES6-style の デフォルト・エクスポートを禁止し、名前付きエクスポートを使用させる |
| no-duplicate-imports     | 同じモジュールからの複数インポートを禁止する |
| no-mergeable-namespace   | 同じファイル内のマージ可能な `namespace` を 禁止する |
| no-require-imports       | `require()` を 禁止し、新しい ES6-style `import/export` を 使用させる |
| object-literal-sort-keys | オブジェクト・リテラルのキーは、アルファベット順に記述させる |
| prefer-const             | 変数への割り当てが１回に限られる場合、 `const` を 使用させる |
| trailing-comma           | 配列とオブジェクトのリテラルで最後にカンマを付けるかのルールを強制する |


### [Style](https://palantir.github.io/tslint/rules/#style)

| ルール                          | 概要 |
|:--------------------------------|:-----|
| align                           | 指定する要素 (e.g. 引数) を 垂直方向を揃えて整列して記述させる |
| array-type                      | 配列宣言を `T[]` もしくは `Array` の いずれかに統一させる |
| arrow-parens                    | Arrow-style 関数 の パラメーターで括弧を必須とする |
| arrow-return-shorthand          | `() => { return x; }` を `() => x` と させる |
| binary-expression-operand-order | *In a binary expression, a literal should always be on the right-hand side if possible. For example, prefer ‘x + 1’ over ‘1 + x’.* |
| callable-types                  | コールシグネチャのみのインターフェース名やリテラル型は関数型を使用させる |
| class-name                      | クラスとインターフェース名はパスカルケース(アッパーキャメルケース)にさせる |
| comment-format                  | １行コメントの書式設定ルールを強制する |
| completed-docs                  | *Enforces documentation for important items be filled out.* |
| encoding                        | UTF-8 エンコーディングを強制する |
| file-header                     | すべてのファイルに対して指定する正規表現にマッチするヘッダーコメントを強制する |
| import-spacing                  | `import` の キーワード間にスペースを入れることを強制する |
| interface-name                  |  ×  | インターフェース名は `I` で 始まることを強制する |
| interface-over-type-literal     | 型リテラル( `type T = {...}` ) ではなく インターフェース名を使用させる |
| jsdoc-format                    | JSDoc の 基本形式ルールを強制する |
| match-default-export-name       | デフォルト・インポートには、インポートする宣言と同じ名前の使用を必須とする |
| newline-before-return           | ブロック内に `return` 以外の行がある場合、 `return` の 前に空行を必須とする |
| new-parens                      | `new` キーワードを使用してコンストラクタを呼び出す場合、括弧を必須とする |
| no-angle-bracket-type-assertion | 型アサーションに `<Type>` の 代わりに `as Type` を 使用させる|
| no-boolean-literal-compare      | `x === true` のような、ブール・リテラルとの比較を警告する |
| no-consecutive-blank-lines      | １もしくは複数の空行を禁止する (デフォルトで １つの空行を許可する) |
| no-irregular-whitespace         | 文字列やコメントの外側にある不規則な空白を禁止する |
| no-parameter-properties         | クラス・コンストラクタでのパラメーター・プロパティを禁止する |
| no-reference-import             | `<reference types="foo" />` の 使用を禁止する |
| no-trailing-whitespace          | 行末尾の空白を禁止する |
| no-unnecessary-callback-wrapper | *Replaces `x => f(x)` with just `f`. To catch more cases, enable `only-arrow-functions` and `arrow-return-shorthand` too.* |
| no-unnecessary-initializer      | `var` `let` `destructuring initializer` の `undefined` 初期化を禁止する  |
| no-unnecessary-qualifier        | *Warns when a namespace qualifier (`A.x`) is unnecessary.* |
| number-literal-format           | 小数点以下のリテラルは `0.` で 始まり、末尾が `0` で 終わらないこと強制する |
| object-literal-key-quotes       | `Enforces consistent object literal property quote style.` |
| object-literal-shorthand        | 可能であれば ES6-style オブジェクト・リテラル の ショートハンドを強制する |
| one-line                        | 特定の予約語 (e.g. `try` の `}` と `catch`) を同一行にさせるかのルールを強制する |
| one-variable-per-declaration    | 複数の変数を同時定義することを禁止する |
| ordered-imports                 | `import` を アルファベット順に記述することを強制する |
| prefer-function-over-method     | *Warns for class methods that do not use ‘this’.* |
| prefer-method-signature         | インターフェースと型の定義を `foo: () => void` でなく `foo(): void` を 使用させる |
| prefer-switch                   | *Prefer a `switch` statement to an `if` statement with simple `===` comparisons.* |
| prefer-template                 | 文字列の連結ではなく、テンプレート式を使用させる |
| quotemark                       | 文字列リテラルを、一重引用符 または 二重引用符 の 指定する方に強制する |
| return-undefined                | Void 関数 では `return;` を、値を返す関数では `return undefined;` を 使用させる |
| semicolon                       | すべてのステートメントの最後に一貫したセミコロンの使用を強制する |
| space-before-function-paren     | 関数の括弧前に入れるスペースのルールを強制する |
| space-within-parens             | 括弧内のスペースのルールを強制する |
| switch-final-break              | `switch` の 最終節 が `break` で 終わるかのルールを強制する |
| type-literal-delimiter          | *Checks that type literal members are separated by semicolons. Enforces a trailing semicolon for multiline type literals.* |
| variable-name                   | 変数名のルールを強制する |
| whitespace                      | 空白スタイルのルールを強制する |



- - - -
TypeScript の コーディング量が足りていないので、どのようなコードを指しているのか想像がつかないものもかなりありました.
あと `cyclomatic-complexity` が あるのがいいですね.
先に設定を悩むより、圧倒的なコーディング量を背景に、よろしくないコードを防ぐことをした方がよかったかもと思いつつ、理解が深まったのでよしとしましょう. 次は、設定を考えたいと思います.
