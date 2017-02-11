---
title: Slack の ボット で JRA 競馬 の 開催日を通知する - JSON 取得編
date: 2017-01-15
comments: true
categories: ボット
tags:
- Slack
- Botkit
---

![](/images/slack/slack.png "Slack")

2016年 の 有馬記念 を 見て、急に競馬が気になりだしました. とはいえ、ちゃんとやったこともないし、いつやっているのかも知らない、といったレベルなので、次週の開催予定をボットに通知してもらうようにして、下調べぐらいはしてから見れるようにしたいと思います.
ボットにしゃべってもらうにはデータが必要なので、まずは開催日のデータ探しと取得方法について考えます.
→ 公開されているデータを取得するよう [Slack の ボット で JRA 競馬 の 開催日を通知する - iCalendar 取得編](/2017/01/18/SlackのボットでJRA競馬の開催日を通知する-iCalendar取得編/) の 記事を追加しました. (2017年1月18日追記）

**作業環境**
- Node.js 6.9.1 LTS


## 開催日情報の取得方法について検討
JRA の サイトに [レーシングカレンダー](http://www.jra.go.jp/keiba/calendar/) があり、さまざまな情報が載っています. しかし開催情報を取得できるような Web API は なさそうです. また、そのような情報を提供しているサイトも見当たりませんでした. (なんか、ありそうな気もするんですが...)

レーシングカレンダー の ソースを見ると、[JavaScript で カレンダー生成](http://www.jra.go.jp/keiba/common/calendar/cal.js)を行っていることが分かります. ソースには 2015 と ありますが、2017年も順調に機能しているようです.
![](/images/slack/keiba/01.png)

その中に JSON ファイルを取得している部分があります！ この [JSON ファイル](http://www.jra.go.jp/keiba/common/calendar/json/201701.json) を 使う手もありそうです.
![](/images/slack/keiba/02.png)

過去の JSON ファイルを探っていくと、[2012年1月](http://www.jra.go.jp/keiba/common/calendar/json/201201.json) が 一番古いようです. カレンダーの描画部分は 2015年にリニューアルしたとして、システム自体は 2012年には稼働していたのでしょうか.
![](/images/slack/keiba/03.png)


## JSON の 取得方法
とりあえず、この JSON を 取得してみたいと思います.
Node.js で HTTP リクエストするには、`http` モジュールを使います. 標準で組み込まれているので簡単に使い始められます. もう少し手軽に扱ったり、複雑なことをするには `request` が よいようですが、今回は簡単な HTTP GET なので `http` で いきたいとおもいます.
```javascript
let http = require('http');
http.get(`http://www.jra.go.jp/keiba/common/calendar/json/201701.json`, (response) => {
    let buffer = '';
    response.setEncoding('utf8').on('data', (chunk) => {  buffer += chunk;  });
    response.on('end', () => {
        let json = JSON.parse(buffer)[0].data;
        for (i in json) {
            for (j in json[i].info[0].gradeRace) {
                let data = json[i];
                let race = data.info[0].gradeRace[j];
                console.log(`${data.date}(${data.day}) ${race.grade} ${race.name}`);
            }
        }
    });
});
```

一時ファイルに落としても良かったのですが、ファイルとして取っておく必要もなく 1 JSON ファイルあたり 7 KB ちょっとなので変数で直接持ってしまいます.
`http` は `data` イベントで読み込んだ分の情報を返します. 全てを読み込んだ結果ではないことに注意です. 読み込みが終わるまで変数に追加し保持しておきます.
全てが読み込み終わると `end` イベントが発生するので、このタイミングで保持しておいたデータを処理します. 今回は読み込んだ情報を JSON オブジェクトにし、1行ごとに開催日とグレード、レース名をコンソールに出すようにしました.

これを 月ごとの JSON ファイルを取得するようにすれば、開催日の情報をボットに通知してもらうことができそうです.



- - - -
JRA の 開催日情報を取得できました. これを活用することで事前にレース開催日を知ることができるので、心して観戦に望めそう
ところで この JSON、JRA の ウェブサイトのカレンダーを描画するためのデータをもらっているわけですが、公式に公開されているものではないので勝手に使うのは お行儀がよくないです. やはりちゃんと公開されているデータを使いたいところです.
