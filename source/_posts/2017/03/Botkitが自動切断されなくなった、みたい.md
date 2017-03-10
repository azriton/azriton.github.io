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
結局 よくわからない、という結論で何とも情けないところではありますが、とりあえず自動切断されなくなり、ワークアラウンドも削除できるので良しとしよう...
