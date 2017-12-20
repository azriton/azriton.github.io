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

![](/assets/slack/slack.png "Slack")

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
![](/assets/slack/anonymizer/01.png)

ダイレクト・メッセージを送るとボットから返事があり、すぐに匿名発言先のチャンネル (ここでは sandbox) に 発言があるハイライトがされます.
![](/assets/slack/anonymizer/02.png)

チャンネルを開くと、先ほどボットにダイレクト・メッセージした内容を、ボットが発言しています.
匿名発言ができるようになりました.
![](/assets/slack/anonymizer/03.png)



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
ボットを使って匿名発言ができるようになりました. Botkit が いろいろやってくれるので簡単ですね.

ただし匿名とはいえ単にボットで代理発言をさせているだけなので、ボット・プログラムでデバッグログなりを出すことで発言を残すことはできますので、完全匿名とまではいかないですが、自由な発言から議論が広がるといいですね.
