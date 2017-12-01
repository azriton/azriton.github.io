---
title: Slack の ボット で 2FA(Two-Factor Authentication / 2要素認証) 未設定ユーザ を 監視する - API 確認編
date: 2017-06-19
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
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
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
とりあえず、2FA 設定の有無が API から取得できることが確認できました. ここまでくれば ボット実装は、これまでの組み合わせなので簡単にできそうですね. 使いやすい API が あるってことは、偉大だ.
