---
title: Slack の ボット で 定期的に降水予報を通知する
date: 2017-02-10
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

[朝の天気と、その後の降水予報が Slack に 通知](/2017/02/04/Slackのボットで今日の天気を通知する-降水予報実装編/)できるようになりました.
これで傘を持っていくかを判断できるようになりましたが、朝は晴れていて日中晴れの予報でも夕方に天気が崩れることもあります. 何てことだ orz

既に傘は持ってきていないわけですが、あらかじめ天気が崩れることが分かれば、予定を変更して早く帰るなりの作戦が取れます. (まぁ、作戦を立てたとして実行できるかは別なのですが...)

今回は、降水予報の実装 を 定期的に実行して、雨もしくは雪が降る予報があった場合に Slack に 通知するようにしたいと思います.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.9
- node-cron 1.2.1
- moment-timezone 0.5.10


## 通知方法の検討
OpenWeatherMap で 取得できる予報は、「3時間ごと 5日間」と「日次で 16日間」の 2つがあります. 無料で使えるのは「3時間ごと 5日間」なので、こちらを使って降水予報を実装したいと思います.
![](/images/slack/weather/10.png)

取得できるデータには降水確率などの情報は無いですが、天気状況に加えダイレクトに降水量があります. この降水量を使うことにして 9時間以内に降水量がある場合に、降水予報として通知するようにします.


## Slack ボット の 実装
定期実行のスケジュールが異なるため、毎朝の天気情報とは異なる cron ジョブを作成します.
```javascrip
new cron.CronJob({
    cronTime: '00 00 9-21 * * 1-5',
    onTick: () => {
        http.get(`http://api.openweathermap.org/data/2.5/forecast?id=${city}&appid=${apikey}&units=metric&cnt=3`, (response) => {
            let body = '';
            response.setEncoding('utf8').on('data', (chunk) => {  body += chunk;  });
            response.on('end', () => {
                let json = JSON.parse(body);
                for (let i in json.list) {
                    let forecast = json.list[i];
                    if ((forecast.rain && forecast.rain['3h']) || (forecast.snow && forecast.snow['3h'])) {
                        let text = '→ ' + labels.next(forecast.dt_txt) + labels.desc(forecast) + labels.prec(forecast);
                        http.get(`http://api.openweathermap.org/data/2.5/weather?id=${city}&units=metric&appid=${apikey}`, (response) => {
                            let body = '';
                            response.setEncoding('utf8').on('data', (chunk) => {  body += chunk;  });
                            response.on('end', () => {
                                let current = JSON.parse(body);
                                bot.say({
                                    channel:   channel,
                                    text:      text,
                                    username:  `${current.weather[0].main}(${current.weather[0].description})`,
                                    icon_url:  `http://openweathermap.org/img/w/${current.weather[0].icon.replace('n', 'd')}.png`
                                });
                            });
                        });
                        break;
                    }
                }
            });
        });
    },
    start: true,
    timeZone: 'Asia/Tokyo'
});
```
基本的な構造は、朝の天気予報 の OpenWeatherMap API 呼び出し順が逆になるだけで、ほぼ同じです.
`cronTime` は 平日の 9時～21時に、1時間ごとに実行するようにしました.
API 呼び出しは、最初に `/forecast` を `cnt=3` で 3時間毎 の データ を 3回分で 約 9時間を取得しています. その中に降水情報があったら アイコン と ユーザ名 に 使う、現在の天気 `/weather` を 取得します.


- - - -
予報も出せるようになったので、朝から傘が必要か、そして日中の天気の変化について知ることができ、降水確率 ○○％ というよりも、何時に、どのくらいの雨(雪) が 降るのかが分かるので、意外とよさそう.
これまで実装してきたものを組み替えるだけで簡単に作れました. API さまさま、ですね. ありがたい.
