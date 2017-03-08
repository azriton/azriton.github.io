---
title: Slack の ボット で JRA 競馬 の 開催日を通知する - iCalendar 取得編
date: 2017-01-18
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

前回、[JRA の サイト から 開催日 JSON を 取得](/2017/01/15/SlackのボットでJRA競馬の開催日を通知する-JSON取得編/)しました. ただ この方法については公開されているものを使っておらず、ウェブサイトのためのデータを勝手に使っているので お行儀が悪いです.
適切なデータを使って開催日の情報を扱えるようにしたいと思います.

**作業環境**
- Node.js 6.9.1 LTS
- unzip 0.1.11
- ical2json 1.0.0


## 開催日情報の取得方法について「再」検討
データを取得する方法について、もう少し検討を進めたいと思います.
[重賞レース一覧](http://www.jra.go.jp/datafile/seiseki/replay/2017/jyusyo.html) の ページがあり、こちらは HTML に 開催日情報 が 記述されています. スクレイピングすることでデータ化することができそうですが、HTML の 構造が変わるなどすると使えなくなってしまうので、最終手段にとっておきたいところです.

よくよくウェブサイトを見ていくと、レーシングカレンダー・ページ の 末尾 に [他のカレンダー と 連携 の リンク](http://www.jra.go.jp/keiba/calendar/index.html#ics_link) が ありました！
![](/images/slack/keiba/04.png)

この [他のカレンダー と 連携](http://www.jra.go.jp/keiba/common/calendar/ics.html) ページを確認すると、iCalendar 形式のファイルをダウンロードすることができるとのことです.
![](/images/slack/keiba/05.png)

これなら公開されているデータなので、安心して使うことができます. 年間開催のスケジュール と 年間の重賞スケジュール の 二つが用意されているようで、今回は重賞スケジュールのファイルを取得することにします.
また "開催中止等に伴うスケジュールの変更には対応しておりません。予めご了承ください。 -- JRA" とのことですが、今回は開催日を通知することが目的で厳密なスケジュール運用は必要としないので、よしとします.


## iCalendar の 取得方法
前回と変わらず `http` モジュールで取得したいと思います.
今回は iCalendar ファイル が Zip で アーカイブされているので、その取扱いが必要となります.
Zip の 解凍は HTTP の レスポンスを Stream で 流し込めるので [unzip](https://github.com/EvanOxfeld/node-unzip) モジュールを使わせてもらいました. また iCalendar の 扱いは JSON に してほしかったので [ical2json](https://github.com/adrianlee44/ical2json) モジュールを使わせてもらいます. それぞれ標準では入っていないので `npm install [module name] --save` で インストールしておく必要があります.
```javascript
const http = require('http');
const unzip = require('unzip');
const ical2json = require('ical2json');

http.get(`http://www.jra.go.jp/keiba/common/calendar/jrarace2017.zip`, (response) => {
    let buffer = '';
    response.pipe(unzip.Parse()).on('entry', (entry) => {
        entry.on('data', (chunk) => {  buffer += chunk;  });
        entry.on('end', () => {
            let ical = ical2json.convert(buffer)['VEVENT'];
            for (let i in ical) {
                let data = ical[i];
                console.log(`${data['DTSTART;VALUE=DATE']} ${data['SUMMARY']}`);
            }
        });
    });
});
```

構造としては、前回 の JSON 取得と変わりありません.
`response` を 直接扱うのではなく `unzip` の `Parse()` に パイプして、HTTP GET で 取得するデータを直接 `unzip` に 流すところがポイントになります.
これにより 一時ファイルに落とさず直接解凍して処理を行うことができます. 今回も解凍して 45 KB ちょっとなので、変数に入れてメモリ上で処理してしまいます.
あとは `ical2json` モジュールが iCalendar の フィールド名 を キーとして JSON に してくれるので、プログラムで簡単に扱うことができます.



- - - -
無事に公開されているデータで開催日を取得することができました. JSON のように データが構造化しきれないので、グレード は 自前で文字列処理が必要となりますが、十分利用できそうです. なにより 1年分が 1回で取れるのがよいですね.
ファイル置き場の URL が 変わらなければ、年数の部分だけ自動処理すれば使いづづけられそうです. 年単位なので いきなり変わることもありそうだけど...

Node.js には いろいろなモジュールがあるので選択に迷いますが、素晴らしいモジュールを提供してくださっている方々のおかげで簡単に実装できるので助かります. ありがとうございます！
