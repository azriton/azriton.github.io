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
OAuth の 権限を使ったボットの作成は、ちょっと長くなりそうなので今回は権限の取得まで...
もうちょっと やりたかったけど仕方ない. ボットが色々な権限を持つことで Slack 内でのちょっとしたお手伝いができるようになるので便利になりそうですね.
