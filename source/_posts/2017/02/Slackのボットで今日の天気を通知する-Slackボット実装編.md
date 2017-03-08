---
title: Slack の ボット で 今日の天気を通知する - Slack ボット 実装編
date: 2017-02-01
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

OpenWeatherMap の サービスを使って、[天気情報を取得できる](/2017/01/24/Slackのボットで今日の天気を通知する-JSON取得編/)ようになり、[JSON の 内容も確認](/2017/01/29/Slackのボットで今日の天気を通知する-JSON確認編/) しました.
いよいよ Slack の ボットに組み込んで、毎朝の天気を通知してもらいましょう.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.9
- node-cron 1.2.1
- moment-timezone 0.5.10


## 通知方法の検討
天気は毎日の事なので、毎朝 7:00 に 通知してもらうことにしたいとおもいます. 土日はもう少し遅くてよいかもしれませんが... とりあえず使ってみて、調整しましょう.
通知内容は、天気、気温、降雨量(降雪量) にし、せっかくなので OpenWeatherMap の アイコン画像も表示します.


## Slack ボット の 実装
```javascrip
'use strict';
const Botkit = require('botkit');
const http = require('http');
const cron = require('cron');
const moment = require('moment-timezone');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {
    moment.locale('ja');
    moment.tz.setDefault('Asia/Tokyo');

    const city = '1850147';       // Tokyo
    const channel = 'sandbox';    // #sandbox
    const apikey = '[API_KEY]';   // Please replace with your API_KEY

    new cron.CronJob({
        cronTime: '00 00 7 * * *',
        onTick: () => {
            http.get(`http://api.openweathermap.org/data/2.5/weather?id=${city}&units=metric&appid=${apikey}`, (response) => {
                let body = '';
                response.setEncoding('utf8').on('data', (chunk) => {  body += chunk;  });
                response.on('end', () => {
                    let current = JSON.parse(body);
                    let text =
                            `${moment.unix(current.dt).format('H:mm')} 現在 ${current.name} の 天気` +
                            `<http://openweathermap.org/img/w/${current.weather[0].icon.replace('n', 'd')}.png?${moment().unix()}| > ` +
                            `${current.weather[0].main}(${current.weather[0].description}) / ` +
                            `気温 ${Math.round(current.main.temp)} ℃ ` +
                            `${current.rain && current.rain['3h'] ? '/ 降雨量 ' + Math.ceil(current.rain['3h'] * 10) / 10 + ' mm ' : '' }` +
                            `${current.snow && current.snow['3h'] ? '/ 降雪量 ' + Math.ceil(current.snow['3h'] * 10) / 10 + ' mm ' : '' }`;
                    bot.say({  text: text,  channel: channel  });
                });
            });
        },
        start: true,
        timeZone: 'Asia/Tokyo'
    });

});
```
Slack ボット の 基本的な作りは [Slack の ボット で 定時アクション](/2017/01/12/Slackのボットで定時アクション/) の 実装と変わりがありません.
※ 今回は `#sandbox` へ 発言するので `const channel = 'sandbox';` としていますが、ID で 指定したほうがよいので `https://slack.com/api/channels.list?token=[API_TOKEN]` へ アクセスして、ID を 調べます. また、発言先のチャンネルへボットが参加している必要があります.

今回は Unix Timestamp の 処理のために、`moment-timezone` を 使いました. Unix Timestamp から、指定したタイムゾーンでの表記にするには、`moment` ではなく `moment-timezone` が 必要となります. また、クラウド環境などでタイムゾーンが異なる環境で動作させる場合も、タイムゾーンを指定しておく必要があります. 今回はロケールに関する出力はありませんが、合わせて設定しておきます.  設定方法は以下のコードとなります.
```
moment.locale('ja');
moment.tz.setDefault('Asia/Tokyo');
```

続いて、`cron.CronJob` の `onTick` で OpenWeatherMap の API へ HTTP GET して、天気情報を取得して、Botkit で 話してもらう処理になります.
[天気情報 の JSON も 確認](/2017/01/29/Slackのボットで今日の天気を通知する-JSON確認編/)できましたので、この内容を文章化するだけになります. その辺が `let text = ...;` に なりますが、JSON (ここでは `current` 変数) から取り出してつなぐだけですが、文字列の連結なのでかなり、ごった煮状態です...

文章を作っているところを 1行ずつ確認したいと思います.
```
`${moment.unix(current.dt).format('H:mm')} 現在 ${current.name} の 天気` +
```
日付を扱う [Moment.js](https://momentjs.com/) を 使い、データ計算時間 の Unix 秒 を フォーマットし、`${current.name}` で 都市名を取り出しています.
都市名は ID を 指定している時点でわっているので明示しても良いのですが、都市 ID を 切り替えるだけで動作できるので、JSON から取り出しています.

```
`<http://openweathermap.org/img/w/${current.weather[0].icon.replace('n', 'd')}.png?${moment().unix()}| > ` +
```
OpenWeatherMap の アイコンを表示する部分になります. `<url|text>` の 形式 で ポストすることで、URL の 代わりに文字列のリンクをポストできます. 今回はリンクを出す必要がないので `<url| >` と スペースを入れることで潰しています. スペースなしだと URL が 表示されたのでスペースにしました.
アイコンのファイル名ですが、`${current.weather[0].icon.replace('n', 'd')}.png` としています. 午前 7時だと夜用アイコンが表示されるので、常に昼用のアイコンを使うようにしています.
また URL の 最後の `?${moment().unix()}` ですが、Slack が 同じ画像 の URL が ポストされた際に展開されない(画像表示されない) という動作をしました. そのため 現在 の Unix timestamp を 付けています. `current.dt` を つけるのも手です. (開発時に困ったので Unix timestamp にしました)

```
`${current.weather[0].main}(${current.weather[0].description}) / ` +
```
現在 の 天気グループ と 天気状態 の 出力になります. (どちらかだけ、でもよかったかも...)

```
`気温 ${Math.round(current.main.temp)} ℃ ` +
```
気温です. 読みやすさ重視で、ざっくり小数点以下を四捨五入して、整数表示にしてます.

```
`${current.rain && current.rain['3h'] ? '/ 降雨量 ' + Math.ceil(current.rain['3h'] * 10) / 10 + ' mm ' : '' }` +
`${current.snow && current.snow['3h'] ? '/ 降雪量 ' + Math.ceil(current.snow['3h'] * 10) / 10 + ' mm ' : '' }`;
```
降水量です. 雨 と 雪 で データが異なり、また両方に値が入っているケースがあります. しかも、`current.rain` と `current.snow` に 空オブジェクトが入るケースがあったので、`current.rain && current.rain['3h']` と 両方を確認して、値が存在したら出力するようにしています.
また四捨五入すると 天気 が 雨 なのに `0 mm` に なるケースもあるので、切り上げにしています.


## 通知！
![](/images/slack/weather/06.png)
天気が通知されました.



- - - -
これで定時アクションの定番、毎朝の天気情報 が Slack に 通知できるようになりました！

天気情報が英語な部分が気になりますが、OpenWeatherMap は 他言語対応されているものの日本語には対応していないので、とりあえずアプリ側で対応する方法を考えてみたいとおもいます.

また単に天気情報を通知するだけでしたら、もっと良いアプリや [IFTTT](https://ja.wikipedia.org/wiki/IFTTT) 連携などでもっと手軽に作れます. せっかく自前で実装したのですから、もう少し工夫を追加してみたいと思います.
