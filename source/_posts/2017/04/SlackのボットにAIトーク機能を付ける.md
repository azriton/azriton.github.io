---
title: Slack の ボット に AIトーク機能を付ける
date: 2017-04-20
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/images/slack/slack.png "Slack")

Slack の ボット で 自然言語での会話できるようにしてみたいという野望があるものの、個人ではなかなか太刀打ちできません. こんなときは巨人の肩、ですね. というこで、最近公開された リクルートテクノロジーズさん の AI を 使って、Slack の ボットにトーク機能を付けてみたいと思います. はたしてちゃんと会話が成立するボットになるのか、楽しみですね♪

**作業環境**
- Slack
- A3RT Talk API
- Node.js 6.10.2 LTS
- Botkit 0.5.2


## A3RT API とは ？
[リクルートテクノロジーズさん が 公開されている AI の API](https://a3rt.recruit-tech.co.jp) で、機械学習 や ディープラーニング(深層学習) を 用いた API だそう. ホットペッパービューティー や カーセンサー などで活用されれいたのだとか. 2017年4月現在、6種類 の API が 提供されていて、今回は その中の Talk API "様々なアプリケーション上でユーザーとの対話を自動化するAPIです。入力文から応答文を自動生成します。日常会話レベルでの応答が可能です。 -- [プレスリリース](http://recruit-tech.co.jp/news/images/20170316_PressRelease.pdf)" を 使わせていただきました.


## A3RT Talk API の API KEY 発行申請
さっそく、A3RT Talk API を 利用するために、API KEY を 発行してもらいます.

[Talk API](https://a3rt.recruit-tech.co.jp/product/talkAPI) の ページへアクセスし、[API KEY 発行] を クリックします.
![](/images/slack/a3rt-talk/01.png)

メール確認の入力ページが表示されるので、まずは [利用規約、プライバシーポリシーに同意する] を クリックします.
![](/images/slack/a3rt-talk/02.png)

画面内にウィンドウが表示され、利用規約 と プライバシーポリシー が 表示されます. 確認して、同期できたら [同意する] ボタン を クリックします. 同意できない場合は利用できないので、その場合は利用をあきらめましょう...
![](/images/slack/a3rt-talk/03.png)

利用規約 と プライバシーポリシー に 同意すると、ウィンドウが閉じるので、利用者申請をするメールアドレスを入力し、[送信] ボタンをクリックします.
![](/images/slack/a3rt-talk/04.png)

ステップ２へ進み、確認メールが送信されたとの画面が表示されます.
ここで、先ほど入力したアドレスにメールが届くのを待ちます.
![](/images/slack/a3rt-talk/05.png)

メールに書かれている URL へ アクセスすると、メールアドレスの確認完了の画面が表示されます. 続いて同じメールアドレスに API KEY が 送られてきます.
API KEY を 受け取ったら、申請完了になります.
![](/images/slack/a3rt-talk/06.png)


## Slack の ボット に 設置
基本動作の確認確認として、Botkit の readme.md [#Basic Usage](https://github.com/howdyai/botkit/blob/master/docs/readme.md#basic-usage) を 使います. そして `fail` というキーワードでメンションをかけられたら、処理が失敗するコードを追加します.
余談ですが、最近 GitHub の Botkit Readme.md が 変わって、基本動作のソースが `docs/readme.md` へ 移動し、またソースが少しわかりにくくなってしまいました. 困るなぁ...
```javascrip
const Botkit = require('botkit');
const request = require('request');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.token
}).startRTM();

controller.on('ambient', (bot, message) => {
    request({
        url: 'https://api.a3rt.recruit-tech.co.jp/talk/v1/smalltalk',
        method: 'POST',
        form: { apikey: process.env.a3rt_talk_apikey, query: message.text },
        json:  true
    }, (err, response, body) => {
        if (body.status == 0) {
            bot.reply(message, `${body.results[0].reply} (${Math.ceil(body.results[0].perplexity * 100) / 100})`);
        } else {
            bot.reply(message, `エラーたよ:fearful: [${body.status} ${body.message}]`);
        }
    });
});
```

今回はチャンネル内で自由に会話応答したいので `ambient` の イベントを受けるようにしました. メンションでの応答にすると毎度つけるのがめんどくさいというのもあり. とはいえ、メンション以外のすべてに応答するので、ほぼ常時反応します. 本番で使う場合は専用ボットにしてチャンネルを限定したほうが良いかもしれません.

会話を受け取ったら Talk API を 呼び出して応答します. Talk API は HTTP POST で `https://api.a3rt.recruit-tech.co.jp/talk/v1/smalltalk` へ リクエストします. リクエスト・パラメータ は `apikey` と `query` です.
`apikey` は、発行申請した際にメールで受け取ったものになります.
`query` は、Talk API へ 渡す会話の文字列です. この入力に対して応答を考えて返してくれます. Slack の 入力なので `message.text` を 渡します.

今回は `request` モジュールを使って実装しました. `json: true` を 設定すると、レスポンスを自動的に JSON オブジェクト として返してくれるので実装が簡単になります.
レスポンスの本体は `body` に 入ってきます. HTTP Status も しっかり切られているのですが、empty reply 応答テキストが空 の 場合 も `200 OK` で 返ってきて無反応な場合が分かりにくかったので、API の Status コードで応答を振り分けました.

`body.status == 0` は 正常に応答しているので、そのまま Slack へ リプライします. 応答メッセージは `body.results[0].reply` に 入っています. 2017年4月現在 `results` は １つしか返ってきませんが、いずれ複数返ってくるようになるのでしょうか. また、`body.results[0].perplexity` に 予測性能 が 入っています. 数値が高いほど確度がよさそうに感じますが、詳細はドキュメントにありませんでした. 今回はテスト用に出力しています. 数値に合わせて絵文字をあてても面白いかもしれません.

`body.status` が `0` 以外の場合は、何かしらのエラーになります. 今回はデバッグ用に Slack へ リプライさせています. 実際にはログに出せばよいでしょう.
注意点としては API が 返事を作れなかった場合は `body.status` が `2000` で 返ります. この場合に、応答を返さないで放置するか、自前で何かの応答を作りこむか判断ポイントになります. (ちなみに何も出力しないで放置バージョンを作ったら、ボットに話しかけたつもりで無視されたような形になり、寂しかったです...)


## いざ、起動！
起動の仕方は、通常の Node.js の 起動に変わりありません. 今回は Slack トークン と A3RT Talk API KEY を 環境変数から受け取るようにしているので、その設定が必要です. 下記の `REPLACE_THIS_WITH_YOUR_TOKEN` を Slack ボット の トークンに、`REPLACE_THIS_WITH_YOUR_APIKEY` を A3RT Talk API の API KEY に 置き換えて実行します.
```shell-session
token=REPLACE_THIS_WITH_YOUR_TOKEN a3rt_talk_apikey=REPLACE_THIS_WITH_YOUR_APIKEY node index.js
Initializing Botkit v0.5.2
info: ** No persistent storage method specified! Data may be lost when process shuts down.
info: ** Setting up custom handlers for processing Slack messages
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...attempting to connect to RTM!
notice: RTM websocket opened
```

※ Windows コマンドプロンプト の 場合は、下記のように `set` で 明示的に環境変数を設定してから実行します.
```shell-session
set token=REPLACE_THIS_WITH_YOUR_TOKEN
set a3rt_talk_apikey=REPLACE_THIS_WITH_YOUR_APIKEY
node index.js
```


## トーク、トーク♪
![](/images/slack/a3rt-talk/07.png)
Slack の 入力に対してちゃんと応答してくれました！ 会話がかみ合っているような、そうでもないようなのは気にしないでおきましょう.



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
リクルートテクノロジーズさん が API を 公開してくださっているおかげで簡単に応答するボットを作ることができました. 登録もメールだけなので容易で助かります. クラウドサービスのサインアップとかになると大変なので、このぐらい手軽に使わせていただけるとうれしいですね. ほかの API も 試してみようっと.
