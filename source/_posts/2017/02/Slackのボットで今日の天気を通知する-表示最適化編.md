---
title: Slack の ボット で 今日の天気を通知する - 表示最適化編
date: 2017-02-07
comments: true
categories: ボット
tags:
- Slack
- Botkit
---

![](/images/slack/slack.png "Slack")

[朝の天気と、その後の降水予報が Slack に 通知](/2017/02/04/Slackのボットで今日の天気を通知する-降水予報実装編/)できるようになりました！
これで毎朝の天気を確認できるわけですが、その情報を見るツールとしてスマホを使った時に少し気になる点がありました.

**作業環境**
- Slack
- Android 6.0
- Slack 2.27.0 (Android App)
- Node.js 6.9.1 LTS
- Botkit 0.4.9


## スマホ表示の確認
![](/images/slack/weather/08.png)
ちゃんと表示されています！ が 天気アイコン、デカい！
分かりやすいと言えば分りやすいですが、ちょっと大きすぎですね. 1日分で、ほぼ画面が埋め尽くされてしまっています. もう少しコンパクトに表示するようにしたいと思います.


## 表示方法の検討
Slack の ボット は 発言する際に、アイコン と 名前 を 変えることができます. ([chat.postMessage method | Slack](https://api.slack.com/methods/chat.postMessage))
これを使うことで、現在ロボットのアイコンが表示されている部分 を 天気アイコン にし、名前の部分に天気情報を入れることにすれば、すっきりしそうです.

Botkit での 実装方法ですが、その辺についての説明はありませんでした. 残念.
近いところとしては [Slack-specific fields and attachments](https://github.com/howdyai/botkit#botreply) に リプライにアタッチする話があります. アタッチの中に　`username` や `icon_url` が 登場しますが、アタッチしたいわけではないのでちょっと異なりそうです.

仕方がないのでソースに当たることにします.
ボットを話させているのは [Slackbot_worker.js#send() -- Botkit v0.4.9](https://github.com/howdyai/botkit/blob/v0.4.9/lib/Slackbot_worker.js#L323) です. こちらを参照すると `slack_message` 変数を作っているところに見事に `username` や `icon_url` が あります. つまり、この関数の引数 `message` に 渡してあげることで設定できそうです. 以下に `Slackbot_worker.js#send()` を 抜粋.
```javascrip
bot.send = function(message, cb) {
    botkit.debug('SAY', message);

    /**
     * Construct a valid slack message.
     */
    var slack_message = {
        type: message.type || 'message',
        channel: message.channel,
        text: message.text || null,
        username: message.username || null,
        parse: message.parse || null,
        link_names: message.link_names || null,
        attachments: message.attachments ?
        JSON.stringify(message.attachments) : null,
        unfurl_links: typeof message.unfurl_links !== 'undefined' ? message.unfurl_links : null,
        unfurl_media: typeof message.unfurl_media !== 'undefined' ? message.unfurl_media : null,
        icon_url: message.icon_url || null,
        icon_emoji: message.icon_emoji || null,
    };
    // (省略)...
};
```

`username` や `icon_url` の 渡し方ですが、こちらは、もうあまり考えることは無いですね. 既に `channel` や `text` を 渡しているわけですから、それらと一緒に渡すだけになります.


## Slack ボット の 実装
以下にボットの発言部分 `bot.say()` の 実装を抜粋します.
```javascrip
bot.say({
    channel:   channel,
    text:      text,
    username:  `${current.weather[0].main}(${current.weather[0].description})`,
    icon_url:  `http://openweathermap.org/img/w/${current.weather[0].icon.replace('n', 'd')}.png`
});
```

`channel` と `text` に 合わせて、`username` と `icon_url` を 設定するだけです.
前回は `text` に アイコン の URL を 使っていたので、Unix timestamp を パラメータでつけていましたが、今回は常に展開されるので Unix timestamp は 不要です.


## 通知！
![](/images/slack/weather/09.png)
アイコンと天気情報がコンパクトにまとまりました.
実際には "Today" の 線が引かれたりするので、さらに見やすくなると思われます.



- - - -
今回はプログラムのテストで朝の情報を夜に出していますが、Slack への ポスト・タイミング と 天気予報の計算時刻は、そう大きくはずれないので "6:52 現在" などを削ってもよいかもしれませんね.
