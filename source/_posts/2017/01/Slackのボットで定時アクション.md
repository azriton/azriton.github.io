---
title: Slack の ボット で 定時アクション
date: 2017-01-12
comments: true
categories: ボット
tags:
- Slack
- Botkit
---

![](/images/slack/slack.png "Slack")

Slack に 設置したボットを一定時間ごとに発言するようにしたいと思います.
定時アクションができるようになると、天気の情報を毎朝ポストしてもらうなど活用の幅が広がります.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.9
- node-cron 1.2.1


## Node.js に cron を 追加
Linux などの OS で 定期的な処理を行うには [cron → crontab - Wikipedia](https://ja.wikipedia.org/wiki/Crontab) が 使われます. Node.js でも同様に `cron` と呼ばれるモジュールが[いくつか](https://www.npmjs.com/search?q=cron)あります.
今回は [Kelektiv](https://github.com/kelektiv)さん の [node-cron](https://github.com/kelektiv/node-cron) を 使いたいと思います.

ボットのプロジェクト・ディレクトリで以下のコマンドを実行し `node-cron` モジュールをインストールします.
```shell-session
c:\Develop\repos\slack-bot> npm install cron --save
`-- cron@1.2.1
```

*メモ*
`node-cron` という名前のモジュールは [Kelektiv](https://github.com/kelektiv)さん と [Merencia](https://github.com/merencia)さん の 2つがあるので注意が必要です.
今回は タイムゾーンが指定できることと 、日付指定の実行ができることなどから [Kelektiv](https://github.com/kelektiv)さん のを使わせていただきました.
特にタイムゾーンを意識する必要がない場合は、[Merencia](https://github.com/merencia)さん のも シンプルな実装でよいかもしれません.


## Botkit での 定時処理
とりあえず、月曜日～金曜日 の 毎朝 9:00 に 挨拶をするようにしたいと思います.
```javascript
const Botkit = require('botkit');
const cron = require('cron');

const controller = Botkit.slackbot();

controller.spawn({
    token : process.env.token
}).startRTM((err, bot, payload) => {
    new cron.CronJob({
        cronTime: '00 00 09 * * 1-5',
        onTick: () => {
            bot.say({
                channel: 'random',
                text: ':smiley: おはようございます！'
            });
        },
        start: true,
        timeZone: 'Asia/Tokyo'
    });
});
```

- ボット起動時に cron の 処理を登録したいので、`startRTM()` の 中で `new cron.CronJob()` します.
- `cronTime` は [Cron Ranges](https://github.com/kelektiv/node-cron#cron-ranges) に従い、起動したい時間を指定します. 今回は「月曜日～金曜日 の 毎朝 9:00」なので、前半 の `00 00 09 ~~~` で 9:00 を 指定し、後半 の `~~~ * * 1-5` で 毎月毎日(`* * ~~~`) に 加えて 最後の `1-5` で 月曜日～金曜日(0 は 日曜日) を 指定します.
- `onTick` に `cronTime` で 指定したタイミングで行う処理を記述します. 今回は Botkit に `#random` チャンネル へ 挨拶を発言しさせます.
- `start` は `true` にすることで、そのまま cron の 処理を開始させます. 処理開始を明示的に行いたい場合は `false` にし、開始したい場所で `start()` を 呼び出しますが、今回は即時に処理を開始してよいので `true` にしました.
- `timeZone` で `cronTime` に 指定するタイムゾーンを明示します. 最近はクラウド環境を使ったりするのでタイムゾーンは意識的に書いておいた方がトラブルに合いにくい気がします.

※ 今回は `#random` へ 発言するので `channel: 'random'` としていますが、ID で 指定したほうがよいので `https://slack.com/api/channels.list?token=[API_TOKEN]` へ アクセスして、ID を 調べます. また、発言先のチャンネルへボットが参加している必要があります.


## 実行！
通常通り起動し、`cronTime` で 指定した時間を待ちます. 無事、ボットが挨拶をしてくれたでしょうか！？
![](/images/slack/bot/cron.png)



- - - -
Node.js の モジュールを使うことで簡単に、ボットの定時アクションを実装することができました！ モジュールを作ってくださる方々のおかげで、やりたいことがすぐにできるので助かります. 感謝感謝です.
次回はオーソドックスに天気を知らせてくれる機能を追加しようかな～
