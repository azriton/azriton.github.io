---
title: Slack の ボット で ランダムなカスタム絵文字リアクション を する
date: 2017-06-13
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

[Slack の ボット に OAuth で 権限を与えられた](/2017/06/01/SlackのボットにOAuthで権限を追加する/)ので、その権限を使って Slack API を 実行するボットを作成してみます.
今回は `emoji:read` の 権限を使い、チームに追加されているカスタム絵文字をランダムで選択してリアクションするボットにしたいと思います.


**作業環境**
- Slack
- Node.js 6.10.2 LTS
- Botkit 0.5.2


## ボット で 絵文字リアクション を 使う
例によって [Basic Usage](https://github.com/howdyai/botkit/blob/master/docs/readme.md#basic-usage) をもとにカスタマイズしたいと思います. Basic Usage そのままに、hello と DM や メンションされたら、返事をした後に 元のメッセージに `raised_hand` の リアクションをつけるようにします.
```javascript
const Botkit = require('botkit');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.bot_access_token
}).startRTM();

controller.hears('hello', [ 'direct_message', 'direct_mention', 'mention' ], (bot, message) => {
    bot.reply(message, 'Hello yourself.');

    bot.api.reactions.add({
        name: 'raised_hand',
        channel: message.channel,
        timestamp: message.ts
    });
});
```

起動する際に、環境変数 `bot_access_token` と `access_token` に、それぞれ ボットのトークン と OAuth で 権限取得したトークンを設定しておくことを忘れないように注意します.

Slack の リアクション は API 呼び出しになります. `bot.api` 以下に Slack API に 対応したメソッドiが用意されています. 今回は `reactions.add` の API を 呼び出したいので、`bot.api.reactions.add()` に なります.

[reactions.add の Slack API リファレンス](https://api.slack.com/methods/reactions.add) を 参考に、引数 を オブジェクトで渡します.
`token` は Botkit の 起動時に渡しているので不要で、`name` が 必須です. ここでは `raised_hand` を 設定しました. 文章中に絵文字として使う場合は `:raised_hand:` と `:` で 囲みますが、リアクション API で 指定する場合は `:` なしで指定します.
`channel` と `timestamp` は オプションなのですが、これはファイルへのリアクションとメッセージへのリアクションで引数が異なるだけで、メッセージへのリアクションは `channel` と `timestamp` が 必要となります.
`message` が 元のメッセージのオブジェクトなので、`message.channel` と `message.ts` で リアクションをつける元のメッセージ指定ができます.
また、第２引数で コールバック関数 を 渡して API の 戻りを受けられますが、今回は特に処理がないので省略しました. たいしたリアクションではないのでエラー処理も省略していますが、重要な場合はエラー・ハンドルしておいたほうがよいいでしょう.

あとはボットを起動して "@BOTNAME hello" と メンションすれば、自分のメッセージにリアクションを付けてくれ、"Hello yourself." と 返事をしてくれます.

[カスタム絵文字](https://my.slack.com/customize/emoji)でも、設定したカスタム絵文字の名前を `:` 無しで指定することで利用できます.

![](/images/slack/emoji-api/01.png)


## 利用できる カスタム絵文字 の 一覧 を 取得する
ここまでは API 呼び出しはしたものの、OAuth で 取得した権限とトークンは必要ありませんでした. ここからが本題 "カスタム絵文字をランダムで選択してリアクションするボット" を 作ります.

ランダムで選択するということは、ランダムで選ぶための候補が必要となります. そのために、まずは 利用できるカスタム絵文字 の 一覧 を 取得したいと思います.

カスタム絵文字の一覧取得は [emoji.list の Slack API](https://api.slack.com/methods/emoji.list) に なります. リファレンス の Description に "Authentication token. Requires scope: emoji:read" と あるように、`emoji:read` の 権限が必要となります.

とりあえず実験で、先のリアクション・プログラムに以下を追加します. 特別な引数はないので空オブジェクトを渡し、第２引数 で API の 戻りを受けてコンソールへ出力しています.
```javascript
    bot.api.emoji.list({}, (err, response) => {
        console.log(err);
        console.log(response);
    });
```

hello メンションをすると、以下のようなログが返ってくるかと思います. 権限がないと言われています.
```console
info: ** API CALL: https://slack.com/api/emoji.list
missing_scope
{ ok: false,
  error: 'missing_scope',
  needed: 'emoji:read',
  provided: 'identify,bot:basic' }
```

プログラムを修正し、`bot.api.emoji.list()` の 第１引数 の オブジェクト に `token: process.env.access_token` を 与えます. これまでは `token` を 与えていなかったので、Botkit が 内部的に、起動時の `controller.spawn()` で 渡されていた `process.env.bot_access_token` を 使ってくれていました. これはボット用のトークンなので、`identify,bot:basic` の 権限しかありません. 今回は、それに代わって `token: process.env.bot_access_token` と 明示的に利用するトークンを指定することで、OAuth で 与えられた権限を利用できるようにします.
```javascript
    bot.api.emoji.list({
        token: process.env.access_token
    }, (err, response) => {
        console.log(err);
        console.log(response);
    });
```

起動しなおして、hello メンションすると、以下のように `err` は `null` で `response` に 値が入ってきます.
```javascript
info: ** API CALL: https://slack.com/api/emoji.list
null
{ ok: true,
  emoji:
   { bowtie: 'https://emoji.slack-edge.com/T13A2XXXX/bowtie/f3ec6f2bb0.png',
     squirrel: 'https://emoji.slack-edge.com/T13A2XXXX/squirrel/465f40c0e0.png',
     glitch_crab: 'https://emoji.slack-edge.com/T13A2XXXX/glitch_crab/db049f1f9c.png',
     piggy: 'https://emoji.slack-edge.com/T13A2XXXX/piggy/b7762ee8cd.png',
     cubimal_chick: 'https://emoji.slack-edge.com/T13A2XXXX/cubimal_chick/85961c43d7.png',
     dusty_stick: 'https://emoji.slack-edge.com/T13A2XXXX/dusty_stick/6177a62312.png',
     slack: 'https://emoji.slack-edge.com/T13A2XXXX/slack/5ee0c9bea3.png',
     pride: 'https://emoji.slack-edge.com/T13A2XXXX/pride/56b1bd3388.png',
     thumbsup_all: 'https://emoji.slack-edge.com/T13A2XXXX/thumbsup_all/50096a1020.gif',
     slack_call: 'https://emoji.slack-edge.com/T13A2XXXX/slack_call/b81fffd6dd.png',
     shipit: 'alias:squirrel',
     white_square: 'alias:white_large_square',
     black_square: 'alias:black_large_square',
     simple_smile: 'https://a.slack-edge.com/0e8da/img/emoji_2016_06_08/apple/simple_smile.png',
     bot_aloha_01: 'https://emoji.slack-edge.com/T13A2XXXX/bot_aloha_01/8ca07cf9170a82c1.png',
     bot_aloha_02: 'https://emoji.slack-edge.com/T13A2XXXX/bot_aloha_02/cbed400e1d85041f.png',
     bot_aloha_03: 'https://emoji.slack-edge.com/T13A2XXXX/bot_aloha_03/4f9440ce2931a3df.png',
     bot_aloha_04: 'https://emoji.slack-edge.com/T13A2XXXX/bot_aloha_04/4c4ccb4999b129e0.png' },
  cache_ts: '1497403802.153746' }
```

前半は Slack の サービス側が追加したカスタム絵文字のようです. 後半に自分で追加したものがありました.
(Slack の サービス側追加のカスタム絵文字は [Customize Your Team の Emoji](https://my.slack.com/customize/emoji) には無いのですが、なんだろう...)

今回は `bot_aloha_01` ～ `~04` までを、こちらの [Shaka Hand Sign Vectors - Download Free Vector Art, Stock Graphics & Images](https://www.vecteezy.com/vector-art/102563-shaka-hand-sign-vectors) 素材を利用させていただき、アロハの挨拶で使われる Shaka sign の カスタム絵文字を追加させていただきました. 素敵なアイコン素材 を ありがとうございます！
(この ジェスチャー? ハンド・サイン? も、"[アロハ](https://ja.wikipedia.org/wiki/%e3%82%a2%e3%83%ad%e3%83%8f_%28%e6%8c%a8%e6%8b%b6%29)" と 思ってましたが、[Shaka sign](https://en.wikipedia.org/wiki/Shaka_sign) って いうんですね. 知らなかった.)


## カスタム絵文字 を ランダムで選び、リアクションする
カスタム絵文字の一覧が取れるようになったので、あとは使いたいカスタム絵文字のセットからランダムで取得するだけになります. API 呼び出しのコールバック関数を以下のように実装しました.
```javascript
    bot.api.emoji.list({
        token: process.env.access_token
    }, (err, response) => {
        let emojis = [];
        for (let emoji in response.emoji) {
            if (emoji.startsWith('bot_aloha_')) {
                emojis.push(emoji)
            }
        }

        bot.api.reactions.add({
            name: emojis[Math.floor(Math.random() * emojis.length)],
            channel: message.channel,
            timestamp: message.ts
        });
    });
```
`emoji.list` API の 戻り `response.emoji` を `for-in` で 回し、`bot_aloha_` で 始まる カスタム絵文字を使う候補の配列に追加します.

候補配列ができたら、使うものをランダムで取得して `bot.api.reactions.add()` の 呼び出しに使います. リアクションはボットの権限で実行できるので、`token` は 渡しません.

`emoji.list` を キャッシュしておいたり、`emoji.list` を ハードコーディングしておくこともできますが、命名ルール(今回は `bot_aloha_` で 始まる) に応じて動くようにしておくことで、命名ルールに則ってカスタム絵文字を追加するだけで拡張できるので、毎回 `emoji.list` を 呼び出すようにしました.

![](/images/slack/emoji-api/02.png)


## プログラム最終系
```javascript
const Botkit = require('botkit');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.bot_access_token
}).startRTM();

controller.hears('hello', [ 'direct_message', 'direct_mention', 'mention' ], (bot, message) => {
    bot.reply(message, 'Hello yourself.');

    bot.api.emoji.list({
        token: process.env.access_token
    }, (err, response) => {
        let emojis = [];
        for (let emoji in response.emoji) {
            if (emoji.startsWith('bot_aloha_')) {
                emojis.push(emoji)
            }
        }

        bot.api.reactions.add({
            name: emojis[Math.floor(Math.random() * emojis.length)],
            channel: message.channel,
            timestamp: message.ts
        });
    });
});
```



- - - -
OAuth で 取得した権限を実際に使ってみました.
Botkit の API 呼び出し `bot.api.[SLACK_API_NAME]()` の 第１引数のオブジェクトに `token: process.env.access_token` を 追加するだけで利用できるので簡単ですね. 権限が不要な場合は `token:` を つけなくてよいのも簡単で助かります. 多くの場合 `token:` 無しで使ってたりするので、必要な時にうっかり付け忘れて `missing_scope` で 怒られるというケースもありますが...
とはいえ、Slack API が 使いやすくなっていることと、Botkit が 便利にしてくれているおかげで、簡単に利用することができました！ありがたいです.
