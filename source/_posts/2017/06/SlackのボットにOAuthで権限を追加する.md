---
title: Slack の ボット に OAuth で 権限を追加する
date: 2017-06-01
updated: 2017-06-07
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/images/slack/slack.png "Slack")

Slack の ボット を 自然言語で会話させたり、リマインド系の処理をさせたりと色々できるようになりました. もう少し踏み込んで [Slack の API](https://api.slack.com/methods) を 活用した処理をできるようにしたいと思います. そうなると、これまでの Bot トークンでは権限が足りず、処理ができません. まずは準備として Slack の ボット に OAuth で 権限を追加したいと思います.


**作業環境**
- Windows 10
- Slack
- A3RT Talk API
- Node.js 6.10.2 LTS
- Botkit 0.5.4


## アプリ・ボット の 作成
OAuth で ボットに権限を追加するには、アプリ・ボット(勝手に命名！ `App` じゃ わかりにくいので.) を 作って、そちらで動作させる必要があります. これまでのボットは残念ながらサヨナラに (T_T とはいえ、Slack でのアカウントだけなので、作ってきたプログラムは そのままに動作させられます.

アプリ・ボット作成のページ [https://api.slack.com/apps/new](https://api.slack.com/apps/new) へ アクセスします.
[App Name] に アプリ・ボット の 名前を入力し、[Development Slack Team] で 自分の Slack チーム を 選択します.
[I plan to submit~] は、作成したボットを Slack App Directory で 公開する場合にチェックをつけますが、今回はなしで [Create App] ボタンをクリックして進めました.
![](/images/slack/oauth/01.png)

アプリ・ボットが作成され基本情報の画面が表示されます. [Client ID] と [Client Secret] は 後で使うのでひかえておきます. Client Secret は [Show] ボタンをクリックすると表示されます. 特に大事なので取り扱いに注意します.
![](/images/slack/oauth/02.png)

左メニューから [OAuth & Permission] を クリックし、画面中央の [Redirect URL(s)] に `http://localhost:3000/slack/auth/redirect` を 入力し、[Save Changes] を クリックします.
これは Slack の OAuth から リダイレクト・バックされる際の URL に なります. 通常はアプリを動かしておくのですが、今回は手動でブラウザ内完結するので `localhost` に しました. 詳細は こちら、[Sign in with Slack](https://api.slack.com/docs/sign-in-with-slack) も ご参照ください.
![](/images/slack/oauth/03.png)

続いて Slack に ボットのユーザを追加します. 左メニューから [Bot Users] を クリックします.
[Default username] に Slack の ボット・ユーザー名を入力します. 既存のユーザー名と重複すると、自動的に連番が付与されるので注意してください.
入力後 [Add Bot User] を クリックします.
![](/images/slack/oauth/04.png)


## OAuth で 権限を追加
Slack の [OAuth Scopes](https://api.slack.com/docs/oauth-scopes) の ページから、必要な権限をピックアップします. 今回は、今後使っていきたい想定で `bot`, `channels:history` ,`emoji:read`, `files:read`, `files:write:user`, `users:read` の権限を取得することにしました.
※ 本来は必要になり次第、以下の手順で追加していくべきですが、一連の流れで作っていくのでまとめて取得しました. あまりとりすぎるとボットができる範囲が大きくなりすぎるので、何かあった際の影響も大きくなるため権限の設定は注意が必要です.

**2017年6月7日 追記**
以下の手順を簡易化するためのツールを作りました. よろしかったら、こちらもご利用ください.
Slack OAuth Helper - [https://azriton.github.io/slack-oauth-helper/](https://azriton.github.io/slack-oauth-helper/)

必要な権限のスコープが決まったのでブラウザで `https://slack.com/oauth/authorize?client_id=REPLACE_THIS_WITH_YOUR_APP_CLIENT_ID&scope=REPLACE_THIS_WITH_YOUR_OAUTH_SCOPE` へ アクセスします.
`REPLACE_THIS_WITH_YOUR_APP_CLIENT_ID` は、先ほどアプリ・ボットを作成した際にひかえた [Client ID] を、
`REPLACE_THIS_WITH_YOUR_OAUTH_SCOPE` は、上記で選択した OAuth Scope を カンマつなぎ (e.g. `bot,channels:history,emoji:read,files:read,files:write:user,users:read`)で置き換えます.

そうすると、Slack の OAuth 権限確認画面が表示されます. 与える権限の内容を確認し問題なければ [Authorize] ボタンをクリックして認可します. (複数の Slack チーム に サインインしている場合はチーム選択が先に表示されるので、アプリ・ボットを使うチームを選択します.)
![](/images/slack/oauth/05.png)

ブラウザが `localhost` へ リダイレクトされ、ページが表示できないエラーが表示されます. 慌てずに、アドレスバー の URL から `code` の 値をコピーします.
※ このリダイレクトされた先は、先にアプリ・ボットを作成した際に [OAuth & Permission] の [Redirect URL(s)] に 入力したものになります. 本来は OAuth で 連携したアプリに戻してプログラムで処理するのですが、今回は手動でやりきります.
![](/images/slack/oauth/06.png)

ブラウザで、`https://slack.com/api/oauth.access?client_id=REPLACE_THIS_WITH_YOUR_APP_CLIENT_ID&client_secret=REPLACE_THIS_WITH_YOUR_APP_CLIENT_SECRET&code=REPLACE_THIS_WITH_COPYED_CODE` へ アクセスします.
置き換えは以下となります.
- `REPLACE_THIS_WITH_YOUR_APP_CLIENT_ID` は、先にアプリ・ボットを作成した際にひかえた [Client ID]
- `REPLACE_THIS_WITH_YOUR_APP_CLIENT_SECRET` は、先にアプリ・ボットを作成した際にひかえた [Client Secret]
- `REPLACE_THIS_WITH_COPYED_CODE` は、上記ブラウザのリダイレクトで戻されたアドレスバーの URL `code` の 値

正しく入力できるとトークンが含まれた JSON が 返ります. `access_token` と `bot_access_token` を、それぞれひかえておきます.
`access_token` は Slack の API を 呼び出す際に使うトークンで、`bot_access_token` は ボットを起動する際に使用するトークン(これまで使ってきたトークンと同じ位置づけ) です.
![](/images/slack/oauth/07.png)


## とりあえず、ボット を 起動
今回は OAuth の 権限を持ったボットの作成で準備段階なので、基本動作 の Botkit readme.md [#Basic Usage](https://github.com/howdyai/botkit/blob/master/docs/readme.md#basic-usage) の まま `index.js` を 作ります.
```javascript
const Botkit = require('botkit');
const controller = Botkit.slackbot();

controller.spawn({
    token : process.env.bot_access_token
}).startRTM();

controller.hears('hello', [ 'direct_message', 'direct_mention', 'mention' ], (bot, message) => {
    bot.reply(message, 'Hello yourself.');
});
```

ボットのプログラムができたら起動します.
`[API_TOKEN]` は Slack からコピーした、ボットの API Token に なります.
```shell-session
c:\Develop\repos\slack-bot> set access_token=[ACCESS_TOKEN]
c:\Develop\repos\slack-bot> set bot_access_token=[BOT_ACCESS_TOKEN]
c:\Develop\repos\slack-bot> npm install --save botkit
c:\Develop\repos\slack-bot> node index.js
Initializing Botkit v0.5.4
info: ** No persistent storage method specified! Data may be lost when process shuts down.
info: ** Setting up custom handlers for processing Slack messages
info: ** API CALL: https://slack.com/api/rtm.connect
notice: ** BOT ID: dev-anonymous-poet ...attempting to connect to RTM!
notice: RTM websocket opened
```



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
OAuth の 権限を使ったボットの作成は、ちょっと長くなりそうなので今回は権限の取得まで...
もうちょっと やりたかったけど仕方ない. ボットが色々な権限を持つことで Slack 内でのちょっとしたお手伝いができるようになるので便利になりそうですね.
