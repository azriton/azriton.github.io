---
title: Slack の ボット で 2FA(Two-Factor Authentication / 2要素認証) 未設定ユーザ を 監視する - API 確認編
date: 2017-06-19
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

Slack には Free Plan でも 2FA の 機能が用意されています. アカウントの安全性から 2FA は 設定しておくべきですが、2FA の 必須化は 有料 の Standard Plan からになります. 目の届く範囲での利用でしたら、声がけしていくことで全員に設定してもらうことはできそうですが、ある程度の規模になると難しくなってきます. そんな時こそ、有料プラン！ と 行きたいところですが、この手の話は往々にして時間がかかるもの. その間 ボットに 2FA の 状態を監視、レポートしてもらいたいと思います.


**作業環境**
- Slack
- Node.js 6.11.0 LTS
- Botkit 0.5.2


## まずは 2FA 設定状況 の 確認
管理者権限のユーザで [https://my.slack.com/admin](https://my.slack.com/admin) へ アクセスすると、チーム・メンバーの一覧 と 2FA の 設定状況を確認することができます.
下図の例では [Team Admin] は 2FA が 設定されているため、2FA アイコンがついています. 一方で [You] は  アイコンがついていないので、2FA が 設定されていません. 2FA を 設定するよう指導が必要です！って、自分ですが...
なお、2FA の 設定方法は こちら [2要素認証を設定する – Slack](https://get.slack.help/hc/ja/articles/204509068-2%E8%A6%81%E7%B4%A0%E8%AA%8D%E8%A8%BC%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B) に ヘルプがあります.
![](/images/slack/users-api/01.png)

毎度ウェブから確認するのは手間なので、2FA を 必須化したいところです. Slack には 2FA 必須化オプションが 有料 の Standard Plan から用意されているので、こちらを使うことで 2FA の 設定を強制することができます. 価格情報は [https://my.slack.com/pricing](https://my.slack.com/pricing) から参照でき、2017年6月現在 "850円/月/アクティブ・ユーザー" です.
![](/images/slack/users-api/02.png)

お金の話になると途端に進捗が悪くなる、なんてことは よくある話でして、何とかボットでうまく警告したいところで、ボットでチェックする方法を考えていきます.


## API で ユーザー情報 の 取得
まずは API で ユーザー情報を取得し、どのような情報があるのか確認してみます. ユーザー情報取得 の API は [`users.list`](https://api.slack.com/methods/users.list) と [`users.info`](https://api.slack.com/methods/users.info) が あります.

それぞれのデータを確認するために、ボットを起動した際に API 呼び出しを行ってみます.
※ 11行目 の `U01CAXXXX` は 調べたいユーザ の ID に なります. `bot.api.users.list()` の データ出力を確認してから、表示したいユーザ の ID を 設定します.
```javascript
const Botkit = require('botkit');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {
    bot.api.users.list({}, (err, response) => {
        console.log(response)
    });
    bot.api.users.info({ user: 'U01CAXXXX' }, (err, response) => {
        console.log(response)
    });
});
```

実行すると、以下のような情報がコンソールに出力されます. (順不同、整形済み、name: 'username' のみ抽出)
```console
info: ** API CALL: https://slack.com/api/users.list
{ ok: true,
  members:
   [ { id: 'U01CACXXXX',
       team_id: 'T10ADAXXXX',
       name: 'username',
       deleted: false,
       color: '9f69e7',
       real_name: '',
       tz: 'Asia/Tokyo',
       tz_label: 'Japan Standard Time',
       tz_offset: 32400,
       profile: [Object],
       is_admin: false,
       is_owner: false,
       is_primary_owner: false,
       is_restricted: false,
       is_ultra_restricted: false,
       is_bot: false,
       updated: 1487291465 } ],
  cache_ts: 1497847517 }

info: ** API CALL: https://slack.com/api/users.info
{ ok: true,
  user:
   { id: 'U01CACXXXX',
     team_id: 'T10ADAXXXX',
     name: 'username',
     deleted: false,
     color: '9f69e7',
     real_name: '',
     tz: 'Asia/Tokyo',
     tz_label: 'Japan Standard Time',
     tz_offset: 32400,
     profile: [Object],
     is_admin: false,
     is_owner: false,
     is_primary_owner: false,
     is_restricted: false,
     is_ultra_restricted: false,
     is_bot: false,
     updated: 1487291465 } }
```

残念ながら 2FA に 関する情報は取得できないようです...


## OAuth で 取得した トークン を 使う
ところで API リファレンス を よく見ると、引数 `token` の Description は "Authentication token. Requires scope: `users:read`" と なっています. 先ほどのプログラムでは `token` を 渡していないので、ボット の トークン で アクセスしていたことになります.
では [OAuth で `users:read` を 取得しているトークン](/2017/06/01/SlackのボットにOAuthで権限を追加する/) を API 呼び出しに使ってみます. Slack の API 呼び出し関数 の 引数 に 環境変数で渡されたトークンを `token: process.env.access_token` として渡します.  (以下、API 呼び出し部分を抜粋)
```javascript
    bot.api.users.list({ token: process.env.access_token }, (err, response) => {
        console.log(response)
    });
    bot.api.users.info({ token: process.env.bot_access_token, user: 'U01CAXXXX' }, (err, response) => {
        console.log(response)
    });
```

実行結果を確認します. (順不同、整形済み、name: 'username' のみ抽出)
```console
info: ** API CALL: https://slack.com/api/users.list
{ ok: true,
  members:
   [ { id: 'U01CACXXXX',
       team_id: 'T10ADAXXXX',
       name: 'username',
       deleted: false,
       color: '9f69e7',
       real_name: '',
       tz: 'Asia/Tokyo',
       tz_label: 'Japan Standard Time',
       tz_offset: 32400,
       profile: [Object],
       is_admin: false,
       is_owner: false,
       is_primary_owner: false,
       is_restricted: false,
       is_ultra_restricted: false,
       is_bot: false,
       updated: 1487291465,
       has_2fa: false } ],
  cache_ts: 1497850133 }

info: ** API CALL: https://slack.com/api/users.info
{ ok: true,
  user:
   { id: 'U01CACXXXX',
     team_id: 'T10ADAXXXX',
     name: 'username',
     deleted: false,
     color: '9f69e7',
     real_name: '',
     tz: 'Asia/Tokyo',
     tz_label: 'Japan Standard Time',
     tz_offset: 32400,
     profile: [Object],
     is_admin: false,
     is_owner: false,
     is_primary_owner: false,
     is_restricted: false,
     is_ultra_restricted: false,
     is_bot: false,
     updated: 1487291465 } }
```

21行目に `has_2fa: false` が あります！ `users.list` では `has_2fa` が あり、`users.info` には無いようですが、ユーザーを一覧で取得できる `users.list` に あるのは助かります. これを使えば、2FA を 設定していないユーザーを抽出し、連絡することができそうです.



- - - -
とりあえず、2FA 設定の有無が API から取得できることが確認できました. ここまでくれば ボット実装は、これまでの組み合わせなので簡単にできそうですね. 使いやすい API が あるってことは、偉大だ.
