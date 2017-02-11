---
title: CircleCI の 通知 を Slack へ 送る
date: 2016-12-14
comments: true
categories: ブログ
tags:
- CircleCI
- Slack
---

![](/images/circleci/circleci.png "CircleCI")

CircleCI で ビルドが失敗した際にメールで通知されます. この通知をチャットの Slack へ 流れるようにしたいと思います. 今回は、これまで作成してきた Hexo の 自動ビルドとデプロイが失敗した場合に Slack へ 通知するようにします.

**作業環境**
- CircleCI
- Slack


## Slack 側 の 設定
Slack へ ログインし、こちら [https://my.slack.com/apps/A0F7VRE7N-circleci](https://my.slack.com/apps/A0F7VRE7N-circleci) から [Install] ボタンをクリックして CircleCI の 連携 を 追加します.
※ https://my.slack.com の `my` は、それぞれ 自分の Slack へ 行ける 特殊なキーワードなので置き換え不要です. これ便利な機能ですよね！
![](/images/circleci/slack/01.png)

CircleCI の 通知をポストするチャンネルを選択します. チーム開発などを行っている場合は、開発用のチャンネルへポストするようにします. 今回はブログのビルドとデプロイなので自分のプライベート・チャンネル(DM) へ ポストするようにしました. [Add CircleCI Integration] ボタンをクリックします.
![](/images/circleci/slack/02.png)

無事、Slack に CircleCI 連携が追加されました. 各種設定が行える画面が表示されるので、通知名やアイコンなどを必要に応じて変更します. 変更した際には画面下の [Save Integration] を クリックします.
最後に、Step 2 に ある Webhook URL を コピーしておきます.
![](/images/circleci/slack/03.png)


## CircleCI 側 の 設定
CircleCI へ ログインし、連携するプロジェクトの設定ボタンをクリックします.
![](/images/circleci/slack/04.png)

プロジェクトの設定画面の左メニューから [Chat Notifications] を クリックし、右の詳細から [Slack] の [Webhook URL] へ Slack の 設定からコピーした Integration の Webhook URL を 貼り付けます.
必要に応じてオプションを選択し、[& Test Hook] ボタンをクリックします.
今回はチャンネルの上書きは必要ないので [Override room?] を [OFF] にし、ビルドに失敗したときと復旧したときの通知に限りたいので [Fixed/Failed Only] を [ON] に しました.
![](/images/circleci/slack/05.png)

Slack へ 通知を送るようになったので、メールの通知を止めます.
画面左の全体メニューから 歯車アイコン の [Account Settings] を クリックします.
アカウント設定画面の左メニューから [Notifications] を クリックし、右の詳細から [Don't send me emails] を クリックします.
![](/images/circleci/slack/06.png)


## Slack に テストの通知が来てる！
![](/images/circleci/slack/07.png)



- - - -
これで CircleCI からの通知は、ビルドに失敗＆復旧したときだけ Slack の DM へ 流れてくるようになりました.
Slack は いろいろな連携ができるので、引き続きいろいろな挑戦をしたいと思います.
