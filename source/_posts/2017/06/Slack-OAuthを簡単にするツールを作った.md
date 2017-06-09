---
title: Slack OAuth を 簡単にするツールを作った
date: 2017-06-07
comments: true
categories: ボット
tags:
- Slack
---

![](/images/slack/slack.png "Slack")

前回 [Slack の ボット に OAuth で 権限を付与する方法](/2017/06/01/SlackのボットにOAuthで権限を追加する/) について書いたものの、ブラウザで頑張るのは結構めんどくさいよなぁと. なのでツールを作ってみました. まだ細かいところまで作り切れてないですが、少し使う分にはいけそうなので、ちょっと脱線してトークン活用の前にツールをご紹介.


**作業環境**
- Slack
- Windows 10
- Google Chrome


## Slack OAuth Helper
命名苦手なので、まんまの名前です.
GitHub リポジトリ [https://github.com/azriton/slack-oauth-helper](https://github.com/azriton/slack-oauth-helper) に ソース一式をアップし、GitHub Pages を master ブランチで設定したので、[https://azriton.github.io/slack-oauth-helper/](https://azriton.github.io/slack-oauth-helper/) で 動作しています. GitHub すごい！

設置を簡単にしたかったので HTML 1枚 で できています. 外部依存は [Modulr.css](https://decorator.io/modulr/) という CSS ライブラリ と [Font Awesome](http://fontawesome.io/) で、JavaScript は ライブラリなしです.
HTML5 で 追加された `<template>` タグ や SessionStorage, Selectors API などを使っていますが、最近のブラウザだったら大丈夫だと思います. たぶん. 環境の都合で、Windows 10 の Google Chrome と Microsoft Edge でしか動作確認してないです. すみません.
なんか、`document.querySelectorAll()` の `for-of` で Chrome と Edge で 動きが違ったりしたので若干不安ではありますが...


## 使い方
### アプリ・ボット の Redirect URL を 修正
ここではアプリ・ボットは登録されているものとして、Redirect URL の 変更から始めたいと思います.

Slack の Apps [https://api.slack.com/apps](https://api.slack.com/apps) へ アクセスし、利用するボットを選択します.
![](/images/slack/oauth-helper/01.png)

ボットの詳細画面が表示されるので、左メニューから [OAuth & Permissions] を クリックします.
![](/images/slack/oauth-helper/02.png)

画面中ほどの [Redirect URLs] にある、URL の 右にある [ペンのアイコン] を クリックします.
![](/images/slack/oauth-helper/03.png)

URL が 編集できるようになるので、`https://azriton.github.io/slack-oauth-helper/` を 入力し、[Save] ボタンをクリックします. 最後の `/` を 忘れないようにご注意ください.
![](/images/slack/oauth-helper/04.png)

編集が完了します. 下にある [Save URLs] ボタンをクリックします. (こっちの Save も しないと確定されないのでご注意. よく忘れてトラブりました...)
![](/images/slack/oauth-helper/05.png)


### Slack OAuth Helper の 利用
[https://azriton.github.io/slack-oauth-helper/](https://azriton.github.io/slack-oauth-helper/) へ アクセスします.
[Client ID] に アプリ・ボット の Client ID を 入力します.
[OAuth Scopes] で 必要な権限にチェックを入れていきます. ここでは [前回と同じスコープ](/2017/06/01/SlackのボットにOAuthで権限を追加する/#OAuth-で-権限を追加) に チェックしました.
権限選択後、[認可リクエスト] ボタンをクリックします.
![](/images/slack/oauth-helper/06.png)

Slack の Slack の OAuth 権限確認画面 が 表示されます. 権限の詳細を確認し、問題なければ [Authorize] ボタンをクリックします/
![](/images/slack/oauth-helper/07.png)

Slack OAuth Helper に 戻ります. トークン発行済みの場合は権限が更新されているので完了となります.
未発行の場合は、[トークン発行 URL] に 表示されている URL を コピーします. テキストフィールド右 の ファイル・アイコン を クリックすると、クリップボードへコピーされます. ブラウザのアドレスバーへペーストし `REPLACE_THIS_WITH_YOUR_APP_CLIENT_SECRET` を アプリ・ボット の Client Secret に 置き換えてからアクセスします.
![](/images/slack/oauth-helper/08.png)

※ トークン発行については、あえてフォームを作りませんでした. HTML 単独で動作しているので Client Secret を 抜くことはないのですが、この類のデータは不要なら入力欄は作りたくないという考えからになります. ここだけは手間が残りますが、**大事なものは知らない場所に入力しない** ってことでご容赦ください.


## Known Bugs とか、ToDo とか
- フォーム で `required` に なっているのに、効いてません. 未入力でも Slack OAuth へ 遷移して、エラーになります. orz
- Slack OAuth Helper を 開く URL に パラメーター を つけると、設定を復元して画面表示できる機能があるけど、URL を 作る機能が未実装です. orz
- トークン発行済み って スイッチ を 用意して、Slack OAuth 後の戻り画面を切り替えたい.
いずれ GitHub Issues に 入れておかないと...


- - - -
これで Slack の OAuth 権限の設定が少し簡単になりました. 最小権限で作り始めて、必要になってから追加するので、なんだかんだで権限が欲しくなり、そのたびに URL を 作るのもしんどかったので、思い切って作ってよかった.

自分が権限を持っている Slack チーム だと 自己完結できるので良いのですが、一般ユーザの権限しかないところでボットを作っていたりすると、OAuth を お願いしなければならないケースもあり、その際に管理者さまがすぐにやってくれるとよいのですが、いろいろとあると進まないので、こんな画面で渡すと意外と早かったりして助かります. 管理者さまへ簡単に渡せるように設定復元 URL を 作る機能を早く実装しないと.
