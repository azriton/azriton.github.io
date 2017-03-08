---
title: Slack の ボット を 自動停止させない
date: 2016-12-23
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

Slack に 設置したボット. しばらくするといつの間にかオフラインになっている場合があります...
せっかく作ったボット機能をいつでも簡単に使うというわけにいかないので対策を考えたいと思います.

**作業環境**
- Slack
- Node.js 6.9.1 LTS
- Botkit 0.4.2


## いつの間にかオフライン？
設置したボットに翌日話しかけてみたところ、応答がありませんでした. 残念...
よく見るとオフラインになっています.
![](/images/slack/bot/down.png)

ログを確認したところ `notice: RTM close event: 1006 :` なる出力があり、その後の再接続に失敗しているようです.
起動時と停止時に時間を出力するようにしたところ、約８時間ぐらいで止まっている感じでした. (もしかしたら途中でボットを呼んだから、なんとなく切りよくと考えると６時間？)
```shell-session
c:\Develop\repos\slack-bot> set token=[API_TOKEN]
c:\Develop\repos\slack-bot> node index.js
Mon Dec 19 2016 15:49:08 GMT+0900 (東京 (標準時))
info: ** No persistent storage method specified! Data may be lost when process shuts down.
info: ** Setting up custom handlers for processing Slack messages
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...attempting to connect to RTM!
notice: RTM websocket opened
info: ** API CALL: https://slack.com/api/chat.postMessage
notice: RTM close event: 1006 :
Mon Dec 20 2016 07:29:10 GMT+0900 (東京 (標準時))
error: Abnormal websocket close event, attempting to reconnect
notice: ** BOT ID: bot ...reconnect attempt #1 of 3 being made after 1000ms
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...reconnect attempt #2 of 3 being made after 6297ms
info: ** API CALL: https://slack.com/api/rtm.start
notice: ** BOT ID: bot ...reconnect attempt #3 of 3 being made after 9096ms
info: ** API CALL: https://slack.com/api/rtm.start
error: ** BOT ID: bot ...reconnect failed after #4 attempts and 9096ms
```


## 何が起こった？
ログに `RTM close event` と あるように、RTM が クローズされたとのこと. これは日付を出力するために `rtm_close` イベントをフックしているのでわかっている部分で、では何故イベントが発生したのでしょうか.
`rtm_close` イベント を 発生させているのは、ログその後のメッセージから `Slack_web_api.js` の 以下の部分と思われます.
```javascript
bot.rtm.on('close', function(code, message) {
    botkit.log.notice('RTM close event: ' + code + ' : ' + message);
    if (pingTimeoutId) {
        clearTimeout(pingTimeoutId);
    }
    botkit.trigger('rtm_close', [bot]);

    /**
     * CLOSE_ABNORMAL error
     * wasn't closed explicitly, should attempt to reconnect
     */
    if (code === 1006) {
        botkit.log.error('Abnormal websocket close event, attempting to reconnect');
        reconnect();
    }
});
```

そうなると、今度は `bot.rtm` が `close` イベントを発火した部分を確認する必要があります.
この `bot.rtm` は `bot.rtm = new Ws(res.url, null, {agent: agent});` で、`var Ws = require('ws');` です.
Node.js の WebSocket ライブラリ [ws](https://github.com/websockets/ws) です. ちょっと深くなってきました...
そして、残念ながらクローズの条件となる部分を特定することができませんでした... (技術力足りてない orz)


## では、エラー・コード 1006 とは？
６～８時間放置したら止まったのできっとタイムアウトしたのでしょう. と 思い込むことにして、気を取り直しエラー・コードの `1006` について調べたいと思います.
```javasrcript
if (code === 1006) {
    botkit.log.error('Abnormal websocket close event, attempting to reconnect');
    reconnect();
}
```

`Abnormal websocket close event` と メッセージが出力されている通り、異常終了した感があります.
こちらもコードを追いかけたところ、先の ws の ファイル `WebSocket.js` に 以下のようなコードがあります.
なんか、デフォルト で `1006` を セットしてメッセージは出さない気がします. ログでも `~: 1006 :` と `1006` の　後にメッセージが付きそうなのに何も出てなかったのは、このことなのでしょう. メッセージをつぶすなんて...
```javascript
// If the connection was closed abnormally (with an error), or if
// the close control frame was not received then the close code
// must default to 1006.
if (error || !this._closeReceived) {
  this._closeCode = 1006;
}
this.emit('close', this._closeCode || 1000, this._closeMessage || '');
```

しかし、この `1006` ハードコードもされ、デフォルトで使われ不思議な感じがします.
この `1006` の 謎をたどると [getting the reason why websockets closed - Stack Overflow](http://stackoverflow.com/questions/19304157/getting-the-reason-why-websockets-closed) で 以下のように書かれています.
> Close Code 1006 is a special code that means the connection was closed abnormally (locally) by the browser implementation
> -- [getting the reason why websockets closed - Stack Overflow](http://stackoverflow.com/questions/19304157/getting-the-reason-why-websockets-closed)

`Close Code 1006 ` だそうで、リンク先を見ると以下のように書かれています.
> 7.4.1.  Defined Status Codes
>      1006 is a reserved value and MUST NOT be set as a status code in a
>      Close control frame by an endpoint.  It is designated for use in
>      applications expecting a status code to indicate that the
>      connection was closed abnormally, e.g., without sending or
>      receiving a Close control frame.
> -- [RFC 6455 - The WebSocket Protocol](https://tools.ietf.org/html/rfc6455#section-7.4.1)

ちょっと解りにくいので W3C の WebSocket も あたりました.
"In all of these cases, the the WebSocket connection close code would be 1006"、全部 `1006` と 行ってますね... そして上記の RFC 6455 を 見ろと. うーんなるほど.
> User agents must not convey any failure information to scripts in a way that would allow a script to distinguish the following situations:
>
> - A server whose host name could not be resolved.
> - A server to which packets could not successfully be routed.
> - A server that refused the connection on the specified port.
> - A server that failed to correctly perform a TLS handshake (e.g., the server certificate can't be verified).
> - A server that did not complete the opening handshake (e.g. because it was not a WebSocket server).
> - A WebSocket server that sent a correct opening handshake, but that specified options that caused the client to drop the connection (e.g. the server specified a subprotocol that the client did not offer).
> - A WebSocket server that abruptly closed the connection after successfully completing the opening handshake.
> In all of these cases, the the WebSocket connection close code would be 1006, as required by the WebSocket Protocol specification. [WSP]
>
> Allowing a script to distinguish these cases would allow a script to probe the user's local network in preparation for an attack.
> -- [The WebSocket API](https://www.w3.org/TR/websockets/#feedback-from-the-protocol)

攻撃者から守るために全部 `1006` にしてメッセージも出さないということなのだそうで、詳細を知るにはデバッグしていくしかないので、いったん諦めます...


## 暫定の対策
とりあえず止まらないようにワークアラウンドを設定したいと思います.
Botkit の GitHub Issues [Slack RTM retry/reconnect not working #261](https://github.com/howdyai/botkit/issues/261)に 同じような話がありワークアラウンドがありました.
議論の流れからすると `reconnect` に 関する [Pull Request #532](https://github.com/howdyai/botkit/pull/532) が 上がっておりマージはされているようですが、そのリリースは 0.4.2 には含まれていないので、将来のバージョンアップに期待しつつ、今は [@garymoon の コード](https://github.com/howdyai/botkit/issues/261#issuecomment-258954201)を 使わせていただきます. ありがとうございます！
```javascript
const controller = ...;
const bot = ...;

function start_rtm() {
    bot.startRTM((err,bot,payload) => {
        if (err) {
            console.log('Failed to start RTM')
            return setTimeout(start_rtm, 60000);
        }
        console.log("RTM started!");
    });
};

controller.on('rtm_close', (bot, err) => {
    start_rtm();
});

start_rtm();
```



- - - -
これによりノンストップで稼働することができるようになりました. なぜリトライに失敗するのかはデバッグしてかないとわからないので、いずれ確認したいと思いますが、まずはボットに機能を足してボットライフを楽しみましょう♪
