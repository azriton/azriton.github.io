---
title: Slack の ボット で 今日の天気を通知する - 降水予報 実装編
date: 2017-02-04
updated: 2017-02-04
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/assets/slack/slack.png "Slack")

[毎朝の天気が Slack に 通知](/2017/02/01/Slackのボットで今日の天気を通知する-Slackボット実装編/)されるようになりました. これは現在の天気を通知しているので、朝の天気を知ることはできても、その後どうなるかはわかりません. 今回は、その後 雨(雪) が 降るのかの予報も合わせて通知するようにしたいと思います.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.9
- node-cron 1.2.1
- moment-timezone 0.5.10


## 通知方法の検討
OpenWeatherMap で 取得できる予報は、「3時間ごと 5日間」と「日次で 16日間」の 2つがあります. 無料で使えるのは「3時間ごと 5日間」なので、こちらを使って降水予報を実装したいと思います.
![](/assets/slack/weather/01.png)

取得できるデータには降水確率などの情報は無いですが、ダイレクトに天気状況と降水量があります. この降水量を使うことにして 24時間以内に降水量がある場合に、降水予報として通知するようにします.


## Slack ボット の 実装
以下に [前回実装](/2017/02/01/Slackのボットで今日の天気を通知する-Slackボット実装編/) の `onTick:` 部分を抜粋し今回の追加分をあげます.
```javascrip
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

            // ここから追加
            http.get(`http://api.openweathermap.org/data/2.5/forecast?id=${city}&appid=${apikey}&units=metric&cnt=8`, (response) => {
                body = '';
                response.setEncoding('utf8').on('data', (chunk) => {  body += chunk;  });
                response.on('end', () => {
                    let json = JSON.parse(body);
                    for (let i in json.list) {
                        let forecast = json.list[i];
                        if ((forecast.rain && forecast.rain['3h']) || (forecast.snow && forecast.snow['3h'])) {
                            text +=
                                `\n→ 降水予報 ${moment.utc(forecast.dt_txt).fromNow()} ` +
                                `(${moment.utc(forecast.dt_txt).utcOffset(9).format('M/D H:mm')}) / ${current.weather[0].main} ` +
                                `${forecast.rain && forecast.rain['3h'] ? '/ 降雨量 ' + Math.ceil(forecast.rain['3h'] * 10) / 10 + ' mm ' : '' }` +
                                `${forecast.snow && forecast.snow['3h'] ? '/ 降雪量 ' + Math.ceil(forecast.snow['3h'] * 10) / 10 + ' mm ' : '' }`;
                            break;
                        }
                    }
                    bot.say({  text: text,  channel: channel  });
                });
            });
            // ここまで
        });
    });
},
```
予報は `json.list` に 入っているので for 文で 1つずつ処理します. 予報の時刻 `forecast.dt_txt` を 表示用にフォーマットし、後は現在の天気で処理したのとほぼ同じになります. アイコンが並ぶとわかりにくいので、予報のアイコンは不要としました.

ボットが発言する `bot.say()` は、予報の JSON を 取得した後になります.


## 通知！
![](/assets/slack/weather/07.png)
朝の天気に加え 20時間後、明日 の 午前 3:00 に 雨が降るとの予報も表示されました. この予報だと傘の出番はなさそうですね.



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
予報も出せるようになったので、朝から傘が必要か、帰りには必要になるのかが分かるようになりました. 降水確率 ○○％ というよりも、何時に、どのくらいの雨(雪) が 降るのかがはっきりするのでよいさそうです.

ちょっと気になるのが、天気の精度やデータの有無...
データやキャプチャは東京で統一するようにしたいと思っているのですが、天気の事なので思うような状態でないことはあります. その際に他の都市をいろいろと探すのですが、どうも他の天気予報と合ってなかったり、極端な話 [OpenWeatherMap の 地図](http://openweathermap.org/weathermap?basemap=map&cities=true&layer=precipitation) とも合ってない時があるような気も...
また、天気 が Rain に なっているのに、現在の天気 API に `rain.3h` が 入ってなかったりと、なのに Clear で 0.1 mm とか... 不思議だ...
無料で使わせてもらっているので、多くは望まないのですが、精度が悪いようだと困るなぁ. 今度、いろいろと比較を出して調べてみよう.
