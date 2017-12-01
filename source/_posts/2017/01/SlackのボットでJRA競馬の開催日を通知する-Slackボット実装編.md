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
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
ボットで JRA の GI レース開催日 を Slack に 通知することができました. これで予め準備の上で観戦に臨めそうで楽しみです. (その前に基本的なことを勉強しておかないと...)

[定時アクションができるようになり](/2017/01/12/Slackのボットで定時アクション/) 定番の天気予報に着手しようと思っていましたが、思わぬ方向の機能を作ることになりましたが、おもしろい物ができたと思います. 思いついた時こそ勉強のチャンスですね.
