---
title: Slack の ボットによる代理ポストで、簡易匿名化
date: 2016-12-26
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/images/slack/slack.png "Slack")

ようやく常時稼働するボットを Slack に 常駐できるようになりました. 今回は Slack で 匿名発言する方法について考えたいと思います.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.2


## Slack で 匿名発言
そもそもチャットなのに、なんで匿名で発言する必要があるのか、といった話もありますが、チームや組織の改善などを忌憚なくディスカスするには匿名はある程度意味があるようです.
行き過ぎると匿名のネット掲示板のように荒れるというケースもあるようですが、Slack は 招待性のチームで作られるので、ある程度は自制・自浄されることは期待できそうです.

Slack で 匿名発言する方法ですが、公式でその機能はありません. したがって何らかの仕掛けを作る必要があります.
API を 使って発言する方法なども考えられますが、せっかくボットを常駐させたので代理で発言してもらって、匿名化してみたいと思います.


## ボット で 代理発言
Slack の ボットは、[以前に設置したボット](/2016/12/17/Slackにボットを設置する/) を 使うことにします.
ボットへの代理発言依頼は、ダイレクト・メッセージを使うことにします.

匿名で発言する先のチャンネルを選択(or 作成) します. 今回は `sandbox` に しました.
続いて チャンネル の ID を 調べるために、以下の URL へ アクセスします. `[API_TOKEN]` は ボットの起動に使用している API トークンです.
`https://slack.com/api/channels.list?token=[API_TOKEN]`

ページへアクセスすると JSON 形式 の チャンネル・リストが表示されるので、目的のチャンネルの ID を コピーします. 以下に今回使用する `sandbox` の 部分を抜粋します. `"id": "C3ADKXXXX",` の `C3ADKXXXX` に 当たる部分になります.
```json
{
    "ok": true,
    "channels": [{
        "id": "C3ADKXXXX",
        "name": "sandbox",
        "is_channel": true,
        "created": 1481422490,
        "creator": "U3A3DXXXX",
        "is_archived": false,
        "is_general": false,
        "is_member": false,
        "members": [ "U3A3DXXXX", U3AQBXXXX ],
        "topic": { "value": "", "creator": "", "last_set": 0 },
        "purpose": { "value": "", "creator": "", "last_set": 0 },
        "num_members": 2
    }]
}
```

ボットの代理発言を実装します.
以下のコードをボットの実装に追加します. [CHANNEL_ID] は 上記で取得した発言先チャンネル の ID です.
```javascript
controller.on('direct_message', (bot, message) => {
    bot.reply(message, '匿名でポストしました.');
    bot.startConversation({  channel : '[CHANNEL_ID]' }, (err, convo) => {
        convo.say(message);
    });
});
```

実装の内容としては以下となります.
- `controller.on()` で `direct_message` イベント に 反応するようにします.
- `bot.reply()` で ダイレクト・メッセージに返事をします.
- `bot.startConversation()` で ID 指定したチャンネルで会話を開始します.
- `convo.say(message);` で 指定チャンネルへ `message`、ユーザから入力された内容を そのままに指定チャンネルへ発言します.


## 匿名で発言！
DIRECT MESSAGES の ボット名をクリックし、発言したい内容を入力します. (つまりボットに対してダイレクト・メッセージを送ります)
![](/images/slack/anonymizer/01.png)

ダイレクト・メッセージを送るとボットから返事があり、すぐに匿名発言先のチャンネル (ここでは sandbox) に 発言があるハイライトがされます.
![](/images/slack/anonymizer/02.png)

チャンネルを開くと、先ほどボットにダイレクト・メッセージした内容を、ボットが発言しています.
匿名発言ができるようになりました.
![](/images/slack/anonymizer/03.png)



- - - -
ボットを使って匿名発言ができるようになりました. Botkit が いろいろやってくれるので簡単ですね.

ただし匿名とはいえ単にボットで代理発言をさせているだけなので、ボット・プログラムでデバッグログなりを出すことで発言を残すことはできますので、完全匿名とまではいかないですが、自由な発言から議論が広がるといいですね.
