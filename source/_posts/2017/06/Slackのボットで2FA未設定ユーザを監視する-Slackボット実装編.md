---
title: Slack の ボット で 2FA(Two-Factor Authentication / 2要素認証) 未設定ユーザ を 監視する - Slack ボット 実装編
date: 2017-06-24
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

Slack の Free Plan でも、[API から 2FA の 設定状況が見れる](/2017/06/19/Slackのボットで2FA未設定ユーザを監視する-API確認編/)ことが分かりました. それを使ってボットが 2FA の 設定状況を監視しレポートするようにしたいと思います.

**作業環境**
- Slack
- Node.js 6.11.0 LTS
- Botkit 0.5.2


## 通知方法の検討
セキュリティに関することなのでキッチリ警告していきたいものの、あまり頻度が高いのも考え物. 加減が難しいところです...
今回は、未設定者に DM で 週１回 月曜日 の 14時 に 通知し、Slack の チーム管理者 向けに `#sandbox` へ 未設定者一覧の支援依頼のメッセージを出力します. (未設定者を公開で晒すのも微妙なので、実際には管理者用チャンネルとかにした方が良いかもしれないですね)


## Slack ボット の 実装
```javascrip
const Botkit = require('botkit');
const cron = require('cron');

const controller = Botkit.slackbot();

const channel = '#sandbox';
const url = 'https://get.slack.help/hc/ja/articles/204509068-2%E8%A6%81%E7%B4%A0%E8%AA%8D%E8%A8%BC%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B';

controller.spawn({
    token: process.env.bot_access_token
}).startRTM((err, bot, payload) => {

    new cron.CronJob({  cronTime: '00 00 14 * * 1',  timeZone: 'Asia/Tokyo',  start: true,  onTick: () => {

        let admins = [];
        let reports = [];
        bot.api.users.list({  token: process.env.access_token  }, (err, response) => {
            for (let member of response.members) {
                if (member.deleted || member.is_bot || member.name == 'slackbot') {
                    continue;
                }

                if (member.is_admin) {
                    admins.push(member.id)
                }

                if (!member.has_2fa) {
                    reports.push(member.id)
                }
            }

            if (reports.length != 0) {
                bot.say({
                    channel: channel,
                    text: `2FA の 未設定 ${reports.length} 人、み～つけた！ ` +
                          '管理者の人～、設定の仕方を助けてくださいませ. ' +
                          `設定の説明は <${url}|こっち> に あるよ. \n` +
                          `未設定者: ${createMentions(reports)}`
                });
            }

            for (let report of reports) {
                bot.startPrivateConversation({  user: report  }, (response, convo) => {
                    convo.say('Slack の 2FA(Two Factor Authentication / 二要素認証) 設定をしてくださいな. ' +
                              `設定の説明は <${url}|こっち> に あるよ. \n` +
                              `あとは ${bot.team_info.name} Slack チーム の 管理者 ${createMentions(admins)} に 聞くのもありだね.`
                    );
                });
            }
        });
    }});

    function createMentions(userIds) {
        let mentions = [];
        for (let userId of userIds) {
            mentions.push(` <@${userId}>`);
        }
        return mentions;
    }
});
```

まずは、`cron` モジュールを使ったボットにするため `controller.spawn().startRTM()` 内で処理を実装するボットを作ります. この辺りは [Slack の ボット で 定時アクション](/2017/01/12/Slackのボットで定時アクション/) で 作ったものになります.

続いて定期処理を作るために `new cron.CronJob()` に `cronTime: '00 00 14 * * 1'` と 処理、その他オプションを渡します. `cronTime` は 秒 分 時 日 月 曜日 なので、`'00 00 14 * * 1'` は 左３つで14時ちょうど、右３つで毎週月曜日となります.

14行目からメインのコードになります. チーム管理ユーザ と レポート対象ユーザ を 保持するための配列を用意します.
続いてユーザー一覧を取得する Slack API を [前回](/2017/06/19/Slackのボットで2FA未設定ユーザを監視する-API確認編/)の通り `bot.api.users.list()` で `token` に OAuth で 権限を与えられている方のトークンを渡します.

API から取得した ユーザー一覧を `for-of` で 回しながら `admins` と `reports` の 配列を作成していきます. 削除済み と ボット と "slackbot" は チェック不要なので `continue` します. "slackbot" さん は、なぜかボット判定が効かないので名前で判別しています.
続いて、`is_admin` で チーム管理者の判定をします. オーナーも `is_admin` が `true` で 管理者に入ります. (もし外すなら `is_primary_owner` と `is_owner` で オーナー判定できます)
最後に今回の肝である `has_2fa` で レポート対象ユーザーの判定をします. 通常は無いはずですが、管理者が 2FA 未設定のケースを想定して `is_admin` と `has_2fa` は `else if` ではなく `if` で 分けてます.

32行目から、管理者向けの通知になります.
レポート対象ユーザー `reports` が 空でなければ `bot.say()` で 指定チャンネルへ未設定者のリストを通知します.
※ 今回 `const channel = '#sandbox';` と チャンネル名指定してますが、正式には  `https://slack.com/api/channels.list?token=[API_TOKEN]` で 調べた ID を 使うべきです

42行目から、未設定者に対する DM になります.
未設定者の配列 は `for-of` で 回し、DM を 送る `bot.startPrivateConversation()` を 呼び出します.
`bot.say()` のように、直接メッセージを出せず、Conversation の 形式で送ります. そのため `bot.startPrivateConversation()` の 第２引数で渡すコールバック関数で受け取る 第２引数 `convo` に対して `say()` を 呼び出します.



## 通知！
まずは管理者向けにチャンネルへポストされた方になります. 今回は実験用なので `#sandbox` に 晒されてます.
![](/images/slack/users-api/03.png)

続いて DM を 受けている側になります. ちゃんと相談先のチーム管理者へのメンションも出ています. 安心ですね.
![](/images/slack/users-api/04.png)



- - - -
これで、2FA 未設定者を ~~×炙り出して晒す~~ ◎検出して通知 できるようになりました.
有料プランのように強制はできないものの、毎週通知されることと、管理者へもフォローするよう通知が行くので、おのずと設定されることに期待したいと思います.

もし強化するとしたら、全チャンネルから BAN して、チャンネル参加を拒否する鬼ボットにするとかですかね... なんか面白そうだなぁ. 作ってみたくなってきた.
