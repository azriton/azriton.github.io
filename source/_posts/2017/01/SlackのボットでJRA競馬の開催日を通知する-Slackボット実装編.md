---
title: Slack の ボット で JRA 競馬 の 開催日を通知する - Slack ボット 実装編
date: 2017-01-21
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/images/slack/slack.png "Slack")

前回、[JRA の サイト から 開催日 iCalendar を 取得](/2017/01/18/SlackのボットでJRA競馬の開催日を通知する-iCalendar取得編/)し、ついに Slack の ボットへ組み込む準備ができました！ ボットへ組み込み、開催日を教えてもらいましょう.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.9
- unzip 0.1.11
- ical2json 1.0.0
- node-cron 1.2.1
- moment-timezone 0.5.10


## 通知方法の検討
今回は GI レースに限定し、開催日 10日前 の 午前10時 に 通知するようにしたいと思います. Slack の チャンネルは、とりあえず実験用の #sandbox へ ポストするようにして様子見します.
土日などの連続開催がある場合、*開催日 10日前* だと連続して通知が来るようになってしまいますが、GI レースに限るとあまりないのでよしとします. GII や GIII が 入ってくると、土日連続がかなりあるようなので本格的に通知する場合は、同時通知できるようにした方がよさそうです.


## Slack ボット の 実装
```javascript
'use strict';
const Botkit = require('botkit');
const http = require('http');
const cron = require('cron');
const unzip = require('unzip');
const moment = require('moment-timezone');
const ical2json = require('ical2json');

const controller = Botkit.slackbot();
controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {
    moment.locale('ja');
    moment.tz.setDefault('Asia/Tokyo');

    http.get(`http://www.jra.go.jp/keiba/common/calendar/jrarace2017.zip`, (response) => {
        let buffer = '';
        response.pipe(unzip.Parse()).on('entry', (entry) => {
            entry.on('data', (chunk) => {  buffer += chunk;  });
            entry.on('end', () => {
                let ical = ical2json.convert(buffer)['VEVENT'];
                for (let i in ical) {
                    let data = ical[i];
                    if (data['SUMMARY'].includes('GII')) {  continue;  }
                    new cron.CronJob({
                        cronTime: moment(`${data['DTSTART;VALUE=DATE']}T1000+0900`).subtract(10, 'days').toDate(),
                        onTick: () => {
                            let date = moment(this.race['DTSTART;VALUE=DATE']).format('M/D(ddd)');
                            let race = this.race['SUMMARY'].substring(0, this.race['SUMMARY'].indexOf('('))
                            bot.say({
                                text: `${date} は ${race} ＠${this.race['LOCATION']} だよ`,
                                channel: 'sandbox'
                            });
                        },
                        start: true,
                        timeZone: 'Asia/Tokyo',
                        context: {  race: data  }
                    });
                }
            });
        });
    });
});
```
Slack ボット の 基本的な作りは [Slack の ボット で 定時アクション](/2017/01/12/Slackのボットで定時アクション/) の 実装と変わりがありません. また、iCalendar の 取得部分も [Slack の ボット で JRA 競馬 の 開催日を通知する - iCalendar 取得編](/2017/01/18/SlackのボットでJRA競馬の開催日を通知する-iCalendar取得編/) と なります. これらの組み合わせでできています.

`cron.CronJob` が HTTP GET の後に、繰り返しで作られているところがポイントになります.
開催日の情報を `moment-timezone` モジュールでパースし通知したい時刻とタイムゾーンを付けます. そして `subtract(10, 'days')` で 10日前の日付にし、`cronTime` へ `Date` オブジェクトとして渡します. `node-cron` は crontab の 文字列だけでなく `Date` オブジェクトも渡すことができるので、このように特定日の10日前といった指定ができます.

あとは `onTick` で ボットに話してもらうだけなのですが、ボットが話す際に開催情報が必要となります. これは `new cron.CronJob()` する際に、`context` で ジョブが起動した際に渡したいオブジェクトを指定することで可能となります. 今回は `context: { race: data }` とすることで iCalendar の データ `data` オブジェクト を `race` という名前でコンテキストに登録しておきました.
`onTick` の 際には、`this.race` と コンテキストに渡した名前に `this` を つけてアクセスします.

※ 今回は `#sandbox` へ 発言するので `channel: 'sandbox'` としていますが、ID で 指定したほうがよいので `https://slack.com/api/channels.list?token=[API_TOKEN]` へ アクセスして、ID を 調べます. また、発言先のチャンネルへボットが参加している必要があります.


## 通知！
![](/images/slack/keiba/06.png)
今年最初の GI レースは 2月19日(日曜日) フェブラリーステークス ＠東京競馬場 が 通知されました！
ちょっと先なので、実験用に `cronTime` の 指定をいじりました. `T1000+0900` を `1000` でなく動作検証をする時間にし、
`subtract(10, 'days')` を 2月19日 から 動作検証する日まで引いてあげます.
本投稿を書いているタイミング 1月21日 23時ごろで考えると `T2300+0900` で `subtract(29, 'days')` に なります.



- - - -
ボットで JRA の GI レース開催日 を Slack に 通知することができました. これで予め準備の上で観戦に臨めそうで楽しみです. (その前に基本的なことを勉強しておかないと...)

[定時アクションができるようになり](/2017/01/12/Slackのボットで定時アクション/) 定番の天気予報に着手しようと思っていましたが、思わぬ方向の機能を作ることになりましたが、おもしろい物ができたと思います. 思いついた時こそ勉強のチャンスですね.
