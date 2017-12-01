---
title: Botkit が 自動切断されなくなった、みたい
date: 2017-03-10
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

Botkit で Slack の ボットを作成した際に、ボットがいつの間にか止まっているということがあり、[ワークアラウンドのコードで対処](/2016/12/23/Slackのボットを自動停止させない/)していました.
2017年3月現在 の 最新 v0.5.1 に したところ、自動停止しないようになったようです.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.5.1


## 稼働状況を確認
コネクションの接続/切断時にコンソール・ログを出力するだけのボットを設置し放置してみました.
```javascript
'use strict';
const Botkit = require('botkit');
const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {  console.log(`start: ${new Date()}`)  });

controller.on('rtm_close', (bot, err) => {  console.log(`close: ${new Date()}`)  });
```

稼働時のログは下記となります. v0.5.0 から Botkit の バージョンがログ出力されるようになったみたいです.
```text
Initializing Botkit v0.5.1
info: ** No persistent storage method specified! Data may be lost when process shuts down.
info: ** Setting up custom handlers for processing Slack messages
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...attempting to connect to RTM!
notice: RTM websocket opened
start: Fri Mar 03 2017 13:15:21 GMT+0000 (UTC)
```

開始日時のログ `start: Fri Mar 03 2017 13:15:21 GMT+0000 (UTC)` が 出ている通り、3月3日 に 稼働をさせて、このポストを書いている時点 3月10日まで `rtm_close` イベントは呼ばれておらず、また Slack 上でもボットがオンラインであることを確認しています. このボットを仕掛けたのは発言の少ない Slack チームだったので、この期間はボットの連続稼働実験のために完全に会話をしない状態を作り出すことができました. そのため会話に関することは無通信だったと考えてよいはずです.
前回の v0.4.2 では 6時間ぐらいで自動切断されていたのに対して、今回は 7日間は稼働しているので自動切断されなくなったと考えてよさそうです.


## Botkit v0.4.2 から v0.5.1 までの更新
何時、何処で自動切断されなくなったのか [Change Log](https://github.com/howdyai/botkit/blob/master/changelog.md) を 確認したところ、v0.4.4 で "Make stale connection detection configurable [PR #505](https://github.com/howdyai/botkit/pull/505)" を マージしています.
このタイトルからは、コネクションの自動切断の改善には思えないのですが、他にはコネクションに関する話はなさそうなので、これが気になります.

また [コードの比較](https://github.com/howdyai/botkit/compare/v0.4.2...v0.5.1) からも、コネクションに関係しそうなのは `lib/Slackbot_worker.js` 周りの変更だと思われます.

これらに関連しているであろう Pull Request #505 を 確認したいと思います.
確認したところ... いきなり出てきた THRASHLIFE に 圧倒、なかなか凄いプルリクです.
![](/images/slack/bot/botkit-pr505.png)

それはさておき、肝心の内容ですが、どうもコネクションを維持するための Ping/Pong の 処理が正しく行えていないことについて改善のプルリクに思われます. ただプルリクの書き出で "A LOT of work that was maxing out CPU" と CPU 負荷をかけたケースであることや、最後 jonchurch さん の コメントで "weird edge case" と あるので、今回のように無通信が続いたことによる自動切断とは関連があるとは思えないのですが "the node process wasn't processing the pong response" が どうしても気になります.

v0.4.2 を 使っていた時も、ほぼ暇なボットで会話の通信も少なかったので CPU 負荷はかかってなかったとは思うので、説明からすると関係はなさそうではあるのですが、他のコード変更が影響しているとは思えないですし. なんだろう...

diff を 確認したいと思います.
![](/images/slack/bot/botkit-pr505-diff.png)

`pingIntervalId = setInterval((...), 5000)` が ` pingTimeoutId = setTimeout((...), 5000);` に 変わったところが大きい違いでしょうか. `setInterval` による繰り返しから、`setTimeout` の 遅延処理にして処理内で `setTimeout` を 再呼び出しに変わっています. プルリクでも "replace the use of an interval with a timeout" と言っているので、そう変更したのでしょう.

はたして、この変更が自動切断を防止してくれるようになったのでしょうか... わからない orz



## 関連する記事の更新
- [Slack の ボット を 自動停止させない](/2016/12/23/Slackのボットを自動停止させない/)
- [Slack で プレミアムフライデー・ボット してみる](/2017/02/22/Slackでプレミアムフライデー・ボットしてみる/)



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
結局 よくわからない、という結論で何とも情けないところではありますが、とりあえず自動切断されなくなり、ワークアラウンドも削除できるので良しとしよう...
