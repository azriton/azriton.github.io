---
title: Slack ボット の AIトーク機能 を Docomo の API に 切り替える
date: 2017-05-24
updated: 2017-05-24
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/assets/slack/slack.png "Slack")

Slack の ボット を リクルートテクノロジーズさん の API を 使って[自然言語での会話できるよう](/2017/04/20/SlackのボットにAIトーク機能を付ける/)にしてみました. AI が 流行っている 2017年昨今、様々なサービスがあるのでいろいろ試してみたいと思います. 今回は NTTドコモさん の [AIトーク の API](https://dev.smt.docomo.ne.jp/?p=docs.api.page&api_name=dialogue&p_name=api_usage_scenario) を 試してみました.

**作業環境**
- Slack
- docomo Developer support 雑談対話 API
- Node.js 6.10.2 LTS
- Botkit 0.5.2


## docomo Developer support とは ？
[NTTドコモさん が 公開されている AI の API](https://dev.smt.docomo.ne.jp) で、 "ドコモやパートナー企業が持つ様々なアセットを「API」として汎用化して提供 -- docomo Developer support" してくれるものだそうです. 会話を意識した API では 音声認識や音声合成による読み上げなどの API があり、また 画像認識 や 面白いところでは、山座同定 - 画像に写っている山の名前を取得、仮想フェンス、交通情報 などなど、様々な API が 用意されています. NTTドコモさん の スマートフォン・アプリで使われている機能＋αで提供されているような感じでしょうか.

今回は その中の 雑談対話 API "自然な会話をします。 -- [API/ツール](https://dev.smt.docomo.ne.jp/?p=docs.api.index&ref=toppage_service_secton)" を 使わせていただきました. ウェブサイト で おすすめ されている [Repl-AI](https://repl-ai.jp/) の 方が高機能なイメージがありましたが、シナリオベースで動かすような感じで、チャット上での雑多な会話に応答するには 雑談対話 API が よさそうでした.


## docomo Developer support に ユーザ登録
API の 利用にあたり、まずは docomo Developer support に ユーザ登録する必要があります.

[ログイン](https://dev.smt.docomo.ne.jp/?p=login) の ページへアクセスし、SNSアカウントでログイン / 新規登録 を します. 今回は [メールアドレスで新規登録] 選択しました.
![](/assets/slack/docomo/01.png)

メールアドレスの入力ページが表示されるので、[メールアドレス] と [確認用] に それぞれ 入力します.
[所属されている法人・組織に関する情報を登録する] は、今回は個人利用なのでチェックを外しました. 登録すると利用制限が緩和されるようです. 最初からチェックが入っており、会社名などが必須で表示されているので焦りますが、チェックを外すことで会社名などの項目は消えます.
最後に [文字認証] の 画像に表示されている文字列を入力し、利用規約を確認し、同意できたら [同意して確認画面へ] ボタンをクリックします.
![](/assets/slack/docomo/02.png)

確認画面が表示されるので、メールアドレスに間違えがないか確認し [仮登録する] ボタンをクリックします.
![](/assets/slack/docomo/03.png)

メール送付のステップに進み、確認メールが送信されたとの画面が表示されます.
ここで、先ほど入力したアドレスにメールが届くのを待ちます.
![](/assets/slack/docomo/04.png)

メールに書かれている URL へ アクセスすると、パスワード入力の画面が表示されます. 注意事項に従ったパスワードを決め入力し [パスワードを設定する] ボタンをクリックします.
![](/assets/slack/docomo/05.png)

パスワードが設定できると、新規アカウント登録完了の画面が表示します. 続いて API key を 取得するので、[ログインする] ボタンをクリックします.
![](/assets/slack/docomo/06.png)


## 雑談対話 API の 利用申請 と API key の 取得
アカウント作成の流れから [ログイン画面](https://dev.smt.docomo.ne.jp/?p=login) は 表示されますが、自動ログインや入力はないので、先ほど取得した アカウントのメールアドレスとパスワードでログインします.
![](/assets/slack/docomo/07.png)

マイページ画面が表示されるので、画面中ほどの API利用申請・管理 から [新規API利用申請へ] ボタンをクリックします.
![](/assets/slack/docomo/08.png)

アプリケーションの登録画面が表示されるので、情報を入力します.
審査の話が書かれていますが、今回はすぐに API key が 発行されました.
開発用の仮登録なので審査はなく、本登録時に審査があるのでしょうかね. 審査の説明があってちょっとドキドキしましたが...

コールバック URL は、今回は OAuth を 使わないので注釈の通り `https://dummy` と 入力しました. OAuth を 使うと、個人に応じた会話をしてくれるようです.
提供者名とサポートメールアドレスは OAuth 画面で使われるようで、今回は OAuth は 使わないのですが必須なので入力します.
入力後 [API 機能選択へ] ボタンをクリックします.
![](/assets/slack/docomo/09.png)

API 機能選択画面が表示されます. 今回は [雑談対話] のみを選択し [利用する API の 利用規約に同意して、次へ] ボタンをクリックします. 同意すべき利用規約が画面内にないのですが、アカウント作成時に同意した [ご利用規約](https://dev.smt.docomo.ne.jp/?p=policy) で よいのかと思います.
![](/assets/slack/docomo/10.png)

利用規約に同意できたら、利用申請画面が表示されます. 選択した機能が表示されていることを確認し [利用申請する] ボタンをクリックします.
![](/assets/slack/docomo/11.png)

利用申請が完了しました！ 続いて API key を 確認するため [登録アプリケーション一覧へ] ボタンをクリックします.
![](/assets/slack/docomo/12.png)

API利用申請・管理 画面が表示されます. アプリケーション情報 に 先ほど登録したアプリケーション名があり、client id、client secret、API key が 表示されています. 今回は API key のみ使用するので控えておきます.
![](/assets/slack/docomo/13.png)


## Slack の ボット を 改修
[前回のトーク機能](/2017/04/20/SlackのボットにAIトーク機能を付ける/#Slack-の-ボット-に-設置)を以下のように変更します.
```javascrip
const Botkit = require('botkit');
const request = require('request');

const controller = Botkit.slackbot();

controller.spawn({
    token: process.env.token
}).startRTM();

var contexts = {};
controller.on('ambient', (bot, message) => {
    request.post({
        url: `https://api.apigw.smt.docomo.ne.jp/dialogue/v1/dialogue?APIKEY=${process.env.docomo_apikey}`,
        json: {
            utt: message.text,
            context: contexts[message.user],
            t: 20
        }
    }, (err, response, body) => {
        if (body && body.utt) {
            contexts[message.user] = body.context;
            bot.reply(message, body.utt);
        } else {
            bot.reply(message, `エラーたよ:fearful: [${body}]`);
        }
    });
});
```

`ambient` の イベントを受けるのは変わらず、`request` 先が変わります.
`url` の API URL が 変わり、URL に 続いて `?APIKEY=${process.env.docomo_apikey}` で API key を 渡します. リクエスト・パラメータに入れたかったのですが、どうしてもうまく動かなかった orz
リクエスト・パラメータ は `json` で 以下のデータを送ります.
- `utt` は、API へ 渡す会話の文字列です. この入力に対して応答を考えて返してくれます. Slack の 入力なので `message.text` を 渡します.
- `context` は、会話の継続を識別する ID とのことで API からのレスポンスに含まれる `context` を 再度渡すことで会話の継続ができるようです. Slack では 複数のユーザーが会話してくるので、ユーザーごとに `context` を とっておき渡すようにしました. (こうすると会話の横入りができないので、全員で共通でもよかったかも)
- `t` は、キャラクター指定になります. `20` で 関西弁キャラ、`30` で 赤ちゃんキャラ、指定なし で デフォルトキャラ だそうです. 会話の雰囲気を変えてみたかったので関西弁にしてみました.
他にも会話しているユーザー の ニックネーム や 血液型、星座 などなど 多数のパラメーターが設定できます. 詳細は [機能別リファレンス](https://dev.smt.docomo.ne.jp/?p=docs.api.page&api_name=dialogue&p_name=api_1#tag01) を ご確認ください.

レスポンスの本体は `body` に 入ってきます. `body.utt` が レスポンスの会話なので、`bot.reply()` に 流します. また `body.context` に 会話のコンテキストが入っているので、`request` で 使用した `var contexts` に Slack の ユーザである `message.user` を キーに保持しておきます.
この形だとボットを再起動するなどして変数がクリアされると消えてしまいますが、永続化するほどの情報ではないので変数保持としました.


## いざ、起動！
[前回](/2017/04/20/SlackのボットにAIトーク機能を付ける/#いざ、起動！)同様 Slack トークン と API Key を 環境変数から受け取るので環境変数を設定して起動します.
```shell-session
token=REPLACE_THIS_WITH_YOUR_TOKEN docomo_apikey=REPLACE_THIS_WITH_YOUR_APIKEY node index.js
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
set docomo_apikey=REPLACE_THIS_WITH_YOUR_APIKEY
node index.js
```


## トーク、トーク♪
![](/assets/slack/docomo/14.png)
[前回](/2017/04/20/SlackのボットにAIトーク機能を付ける/#トーク、トーク♪)の流れ同様の会話で始めましたが、いきなりよくわからない応答です. そして好きなんですかね、オフィス街...
会話が詰まってくると疑問を投げかけてきて話題を変えるような応答もしてきます. これはこれで面白い感じです.



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
ボットのフレームワーク に AI の サービス と、組み合わせるだけで簡単に会話ボットが作ることでき、また AI も Web API なので、切り替えも簡単にできました. フレームワークうやサービスを提供してくれてるおかげですね. ありがたいです.

個人的には、リクルートテクノロジーズさん の API に 比べて、NTTドコモさん の API のが、より自然な会話応答をしているようにも感じました. 途中で話を変えてきたり、ニュースをブチ込んできたりと色々と楽しいです. 例のヒツジさん あたりで培われている技術なんですかね.

サービスごとの特色があって面白いです. 次はどこの AI と 会話してみようかなぁ.
