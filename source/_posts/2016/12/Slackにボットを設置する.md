---
title: Slack に ボット を 設置する
date: 2016-12-17
comments: true
categories: ボット
tags:
- Slack
- Botkit
---

![](/images/slack/slack.png "Slack")

Slack を [CircleCI に 連携した](/2016/12/14/CircleCIの通知をSlackへ送る/) ように、Slack は さまざまなサービスと連携できます. またボットを設置して会話への応答やアクションを実行することも可能です. 最近は Slack の ような チャット・ツールからインフラのオペレーションを実行する ChatOps というキーワードも登場しています.
今回は Slack に 簡単なボットを設置したいと思います.

**作業環境**
- Windows 7
- Node.js 6.9.1 LTS
- Botkit 0.4.2


## Slack の Bots を 作成
まずは Slack に Bots の カスタム連携 を 追加します.
Slack へ ログインし、ボットの追加画面 [https://my.slack.com/services/new/bot](https://my.slack.com/services/new/bot) へ アクセスします.
ボットの名前を入力し、[Add bot integration] ボタンをクリックします. 今回は `bot` と しました. (ちゃんと名前を付けてあげよう... orz)
![](/images/slack/bot/01.png)

ボットが作成されました. 各種設定が行える画面が表示されるので、通知名やアイコンなどを必要に応じて変更します. せっかくのボットなので、ちゃんと名前とアイコンを付けてかわいがりましょう. 変更した際には画面下の [Save Integration] を クリックします.
最後に、API Token を コピーしておきます.
![](/images/slack/bot/02.png)

Slack の チャット画面に戻り、ボットが参加するチャンネルを表示し、`/invite @[BOT_NAME]` と コマンドを実行します. 今回は `sandbox` チャンネル で　`/invite @bot` と しました.
![](/images/slack/bot/03.png)

無事、ボットがチャンネルに参加しました.
![](/images/slack/bot/04.png)


## Botkit で ボットのプログラムを作成
ボットのプログラムを作成します. Slack の ボット というと、GitHub の [Hubot](https://hubot.github.com/) が 有名ですが、開発が止まってしまったようです. 残念...
今回は Slack から [発表](https://slackhq.com/the-slack-platform-launch-7a3feb5a423a#.y91c7qd6o) された Howdy の [Botkit](https://github.com/howdyai/botkit) を 使いたいと思います.

作業ディレクトリを `c:\Develop\repos`、プロジェクト名 を `slack-bot` とします. まずは `npm init` で プロジェクト作成を行います. 基本的にはエンターを押していくだけで問題ありません. `license` は 公開するならライセンス設定をしますし、今回は特に公開しないので `UNLICENSED` に しました.
```shell-session
c:\Develop\repos> mkdir slack-bot
c:\Develop\repos> cd slack-bot

c:\Develop\repos\slack-bot> npm init
name: (slack-bot)
version: (1.0.0)
description:
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC) UNLICENSED
About to write to c:\Develop\repos\slack-bot\package.json:

{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "UNLICENSED"
}


Is this ok? (yes)
```

プロジェクト作成後、Botkit を インストールします.
```shell-session
c:\Develop\repos\slack-bot> npm install botkit --save
```

ボットのプログラムを作成します. プロジェクト直下に `index.js` ファイルを作成します.
まずは基本動作の確認からなので、Botkit の readme.md [#Basic Usage](https://github.com/howdyai/botkit/blob/master/readme.md#basic-usage) になります.
```javascript
const Botkit = require('botkit');
const controller = Botkit.slackbot();

controller.spawn({
    token : process.env.token
}).startRTM();

controller.hears('hello', [ 'direct_message', 'direct_mention', 'mention' ], (bot, message) => {
    bot.reply(message, 'Hello yourself.');
});
```

ボットのプログラムができたら起動します.
`[API_TOKEN]` は Slack からコピーした、ボットの API Token に なります.
```shell-session
c:\Develop\repos\slack-bot> set token=[API_TOKEN]
c:\Develop\repos\slack-bot> node index.js
info: ** No persistent storage method specified! Data may be lost when process shuts down.
info: ** Setting up custom handlers for processing Slack messages
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...attempting to connect to RTM!
notice: RTM websocket opened
```


## Hello ボット！
ボットのプログラムが起動すると、DIRECT MESSAGES の ボットのユーザ名(ここでは bot) の 左のアイコンに色がつき ● になります. 色がない ○ の 場合は接続できていないのでプログラムのログなどを確認し接続できるようにします.
接続できたら `@bot hello` と、メンション で `hello` を ボットに送ります.
![](/images/slack/bot/05.png)

ボット が `Helllo yourself.` と 返してくれました！
![](/images/slack/bot/06.png)

先ほどのプログラムの以下の部分が会話の処理になります.
```javascript
controller.hears('hello', [ 'direct_message', 'direct_mention', 'mention' ], (bot, message) => {
    bot.reply(message, 'Hello yourself.');
});
```
`controller.hears()` の 第１引数で反応するメッセージ、第２引数で聞く(反応する)イベントを指定しています.
今回は `hello` という 文字列を以下のイベントで待っているという処理になります.
- **direct_message**: ダイレクト・メッセージ
- **direct_mention**: 名前で始まるメンションのメッセージ (e.g. `@bot hello`)
- **mention**: メンションのメッセージ (e.g. `hello @bot`)

第３引数 に イベントに対する反応の処理を渡します. `bot.reply()` なので、今回はメッセージに対して `Hello yourself.` と 返事をする処理になります.



- - - -
Botkit を 使うことで、簡単にボットを実装することができました. メッセージ対して返事をするだけでなく、さまざまな処理ができますので、このボットを育てていきたいともいます.
