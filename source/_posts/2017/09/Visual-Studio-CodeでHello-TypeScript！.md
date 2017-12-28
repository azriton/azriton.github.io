---
title: Visual Studio Code で Hello TypeScript！
date: 2017-09-04
updated: 2017-09-04
comments: true
categories: 開発環境
tags:
- Visual Studio Code
- TypeScript
- Node.js
---

![](/assets/vscode/visual-studio-code.png "Visual Studio Code")

[TypeScript の インストールと設定](/2017/08/26/Visual-Studio-Code最初の設定変更/)ができました！ さっそく Visual Studio Code から TypeScript で Hello World を！
Visual Studio Code の [公式ドキュメント](https://code.visualstudio.com/docs/languages/typescript) が 詳しいので、ドキュメントに則って進めたいと思います.

**作業環境**
- Windows 10 64bit
- Node.js 8.4.0 64bit
- TypeScript 2.4
- Visual Studio Code


## Visual Studio Code に TypeScript サポート の 拡張機能を追加
Visual Studio Code は 標準で TypeScript に 手厚いサポート "VS Code provides many features for TypeScript out of the box. - [TypeScript Programming with Visual Studio Code](https://code.visualstudio.com/docs/languages/typescript)" がついています. ただし Lint については 拡張機能が必要なようで、今回は [egamma さん の 拡張機能](https://marketplace.visualstudio.com/items?itemName=eg2.tslint) を 使わせていただくことにしました.
レーティングが高く、インストール数も多く、また「ようこそ」画面で推薦されていることなどからの選定となります. Thank you for the great extension!, egamma-san!!

Visual Studio Code を 起動し「ようこそ」画面から、[カスタマイズする] - [ツールと言語] - [TypeScript] を クリックします.
※ 「ようこそ」 が 表示されない場合は、メニュー の [ヘルプ] - [ようこそ] を 選択します
![](/assets/vscode/typescript/01.png)

「TypeScript に追加サポートをインストールしたあと、ウィンドウが再読み込みされます.」 が 表示されるので [OK] を クリックします.
![](/assets/vscode/typescript/02.png)

Visual Studio Code が 再起動し、「ようこそ」の TypeScript の 色が変わり TypeScript サポートがインストールされました.
![](/assets/vscode/typescript/03.png)

また、`Ctrl + Shift + X` で 拡張機能を表示するとインストール済みに TypeScript が 追加されていることからも確認できます.
![](/assets/vscode/typescript/04.png)


## プロジェクト・フォルダー の 作成 と 展開
例によって `Ctrl + @` で 統合コンソールを表示. 下記コマンドを実行しフォルダーの作成 と Visual Studio Code で フォルダーを開きます. 今回は `C:\Develop\workspace\hello-typescript` に フォルダーを作成しました.
```console
PS C:\Users\username> mkdir C:\Develop\workspace\hello-typescript
PS C:\Users\username> code C:\Develop\workspace\hello-typescript\
```
![](/assets/vscode/typescript/05.png)


## tsconfig.json と tslint.json の 作成
まず最初に `tsconfig.json` を 作成し、TypeScript プロジェクトの設定を行います.
`Ctrl + Shift + P` で コマンドパレットを表示、 `new file` で `tsconfig.json` を 作成し、以下の内容で保存します.

```json
{
    "compilerOptions": {
        "target": "es5",
        "module": "commonjs",
        "sourceMap": true
    }
}
```
![](/assets/vscode/typescript/06.png)


`Ctrl + Shift + P` で `tslint` 絞り込み、[TSLint: Create a "tslint.json" file] を 選択します.
![](/assets/vscode/typescript/07.png)

`tslint.json` ファイルが追加されます. このファイルがないと TSLint の 拡張機能が動作しないので要注意です. 今回は TSLint の 設定をデフォルトのままとしました.
![](/assets/vscode/typescript/08.png)


## TypeScript コーディング
`Ctrl + Shift + P` - `new file` - `hello.ts` を 以下の内容で作成します.

```typescript
class Startup {
    public static main(): number {
        console.log('Hello TypeScript！');
        return 0;
    }
}

Startup.main();
```
![](/assets/vscode/typescript/09.png)


## ビルド ＆ 実行
`Ctrl + Shift + B` で ビルドを実行します. ビルド・タスクを聞かれるので `tsc: ビルド - tsconfig.json` を 選択します.
![](/assets/vscode/typescript/10.png)

`tsc` によって トランスパイルされ、 `hello.js` と `hello.js.map` が 生成されます.
![](/assets/vscode/typescript/11.png)

`Ctrl + Shift + @` で 新しい統合ターミナルを開き、 `node` を 実行します.
```console
PS C:\Develop\workspace\hello-typescript> node .\hello.js
Hello TypeScript！
```
![](/assets/vscode/typescript/12.png)



- - - -
Visual Studio Code で TypeScript の Hello World が できました！
