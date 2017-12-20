---
title: Slack で プレミアムフライデー・ボット してみる
date: 2017-02-22
updated: 2017-03-10
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/assets/slack/hanakin/premium-friday.png "Premium Friday")

2017年2月24日 金曜日、それは "プレミアムフライデー" なる施策の開始日である. うん、ウチは関係ないらしいのだけど！ とうことで、関係ないボット を Slack に 乗せてみた.


**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit ~~0.4.9~~ 0.5.1
- node-cron 1.2.1
- moment-timezone 0.5.10


## プレミアムフライデーって、なにそれ？おいしいの？
まずもって関係ないので、よく知りません. 人によっては美味しいものであったり、おもしろい物であるようです.

[経済産業省によりますと](http://www.meti.go.jp/press/2016/12/20161212001/20161212001.html)以下の施策だそうです.
> 個人が幸せや楽しさを感じられる体験（買物や家族との外食、観光等）や、そのための時間の創出を促すことで、
> (1) 充実感・満足感を実感できる生活スタイルの変革への機会になる
> (2) 地域等のコミュニティ機能強化や一体感の醸成につながる
> (3)（単なる安売りではなく）デフレ的傾向を変えていくきっかけとなる
> といった効果につなげていく取組です。

なんか漠然として、よくわかりませんが、[こちらのサイト](https://premium-friday.go.jp/#section_about) を サマると以下でしょうか.
- 月末の金曜日は早く仕事を終え退社する
- 早く退社した時間は、余暇として楽しむ
- ちょっと豊かな月末金曜日を過ごしてハッピーになる

いいなぁ、ハッピ～. こちらもアバウトな感じでよくわからない.
とりあえず聞いたことある話からすると、以下のようです.
- 月末の金曜日は 15時までに退社する
- 退社後は食事や娯楽などを楽しむ
- 消費が活性化され、楽しんだ人 も 景気 も ハッピー になる

なるほど. いいなぁ、ハッピ～.


## ボットの仕様を考える
月末の金曜日は 15時に変えることを促す通知をするのが通常仕様とすることになりそうです. 一方で関係ない身としては 15時に帰れと言われても困りますので、何かしらの工夫が必要です.

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">プレミアムフライデー（月末の金曜日は15時に帰宅しよう！　という制度）の事をニュースで見たワイ <a href="https://t.co/BnVO7aesRY">pic.twitter.com/BnVO7aesRY</a></p>&mdash; ☆←ヒトデ@社畜祭り始めました (@hitodeblog) <a href="https://twitter.com/hitodeblog/status/832432499980537856">2017年2月17日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

いただきます！ こちらのネタ！！
ということで、まずは `username` と `icon` を プレミアムフライデーなアカウントにして 15時退社をアナウンス. 続いて通常ボット・アカウントに戻って上記ネタでリプライする、自作自演ボットにしたいと思います. これなら関係無い感が出てるボットになって、きっと朝から はっぴー な 気分になれ...


## ボット実装
※ [自動切断のワークアラウンド・コードを削除](/2017/03/10/Botkitが自動切断されなくなった、みたい/)する更新をしました (2017年3月10日)
```javascript
'use strict';
const Botkit = require('botkit');
const cron = require('cron');
const http = require('http');
const moment = require('moment-timezone');

const controller = Botkit.slackbot();
moment.locale('ja');
moment.tz.setDefault('Asia/Tokyo');

controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {
    new cron.CronJob({  cronTime: '00 00 11 * * 5',  timeZone: 'Asia/Tokyo',  start: true,  onTick: () => {
        let now = moment();
        if (now.month() != now.clone().add(7, 'days').month()) {
            bot.say({
                channel: 'random',
                text: '今日、月末の金曜日は午後３時に退社して余暇を楽しもうという ' +
                      '<https://premium-friday.go.jp/#section_about|プレミアムフライデー> だよ！ ' +
                      '退社時間が早まることで消費が活性化するし、働き方改革にもつながる施策なんだ. ' +
                      'さぁ～ みんな３時には帰って、買い物、食事や旅行などして普段よりも豊かな生活を送ろう！！',
                username: 'プレミアムフライデー ボット',
                icon_url: 'http://www.meti.go.jp/press/2016/12/20161212001/20161212001-a.jpg'
            }, (err, response) => {
                bot.say({
                    channel: 'random',
                    text: `<http://[YOUR_IMG_URL]/image.jpg?${moment().unix()}| >`
                });
            });
        }
    }});
});
```

Botkit や Moment.js の セットアップ系はいつも通りで、定時処理は毎度の `new cron.CronJob()` で ジョブを作成します.
今回は「月末の金曜日 11時」に 処理をしたいのですが、`cronTime` だけでの表現が浮かびませんでしたので、まずは「金曜日 11時」に 処理を起動するようにしました.
続いて現在日時を取得して、7日後が同じ月か `now.month() != now.clone().add(7, 'days').month()` で 判定することで月末の金曜日なのかを確認しています.

月末の金曜日だったら、まずは「プレミアムフライデー ボット」が 広報の通知をするために `username` と `icon_url` を 変更してポストします.

「プレミアムフライデー ボット」が 発言した後に返事を返したいので、以下のように `bot.say()` の 中で再発言させています. `bot.say()` を 並べてしまうと、タイミングによっては通常ボットの返事が先に来てしまうことがありえるので、`bot.say()` の コールバックから発言する処理になります.
```javascript
bot.say({ /* プレミアムフライデーの発言 */ }, (err, response) => {
    bot.say( /* 通常ボットからの返事 */ );
});
```

通常ボットからの返事では、`<http://[YOUR_IMG_URL]/image.jpg?${moment().unix()}| >` と `<url| >` の 書式で URL を 半角スペースに置き換えてポストする記法を使っています. これによって URL 無しで画像だけポストできます. `?${moment().unix()}` は、Slack が 同じ URL だと 2回目以降は折りたたんでしまって、最初から画像が見えないので、実行時の Unix timestamp を つけることで URL を 変えて展開できるようにしています.
※ 画像は自前で用意して適当なところへ置いておきます.


## 実験！
`cronTime` を 調整して、いざ実験. ちゃんと表示されました.
とりあえず実験用にはネタ元のたツイートから画像を拝借させて頂きましたが、後は画像を自前のネタに変えて適当なとこにアップしてボットを設置するだけですね. 関係ない人々の月末の週末が楽しみです♪
![](/assets/slack/hanakin/01.png)



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
今週末に迫ってきたプレミアムフライデー. 急に騒がしくなってくる中で関係ない身としては流れに乗れない寂しさから勢いボットを作って乗ってみました. 帰るころにはハッピーな人で溢れかえっているか、後の祭りか...
みなさま、よい週末を.
