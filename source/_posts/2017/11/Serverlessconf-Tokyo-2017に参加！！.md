---
title: Serverlessconf Tokyo 2017 に 参加！！
date: 2017-11-03
comments: true
categories: 勉強会
tags:
---

![](/images/study/serverlessconf-tokyo-2017-01.png "Serverlessconf Tokyo 2017")

Serverlessconf Tokyo 2017 に 参加もとい、出席しました. [ブログを書くまでが #ServerlessConf](https://twitter.com/yoshidashingo/status/926703624062775296)、確かにですね. 今回、あまり参加できてなかったので内容の薄い感じがツラいですが、書くまでが勉強会！ 頑張ります！！


## Serverlessconf Tokyo 2017
いま自分の中で最も熱いアーキテクチャのサーバレース！ そのグローバルなイベント Serverlessconf の 東京イベント が  [Serverlessconf Tokyo 2017](http://tokyo.serverlessconf.io/).

2017年11月2日(木) ～ 3日(金) で 開催され、初日の 2日(木) は ワークショップで、サーバーレスのエキスパートたちとアプリ開発とか、話を聞いたりディスカスしたりできる時間. メイン は 3日(金)、多くのスピーカさんたちの話が聞けるイベントです.

なお、グローバル版 の ページは、こちら [http://serverlessconf.io/](http://serverlessconf.io/). (今日現在だと、Tokyo は 2016 までしかないですね.)

この熱いイベント、参加するしかない！っす.
勉強させていただきます！!


## ワークショップ と 会場
今回 ワークショップ が 一番参加できたのですが、ワークショップ の テーマは以下
1. Serverless Tech Challenge with AWS Serverless Services
2. Create A Trainable Bot as a Service with Azure Functions and Logic Apps
3. Build your own serverless video sharing website with Lambda, API Gateway and Firebase
4. Develop a Serverless Weatherbot with IBM Cloud Functions, Apache OpenWhisk and the Serverless Framework

普段は AWS を 使うことが多いので、1. の AWS Tech Challenge が 気になったのですが、Bot と Microsoft Bot Framework の キーワードにひかれて 2. の Azure を 選択しました. Azure も 使いこなせるようになってマルチ・クラウド使いへの入門に...

と、言うと前向きですが、若干 "Tech Challenge" の キーワードにビビッたところもあります (^^;
言い訳すると、有料セミナー の Tech Challenge とか、怖いじゃないですか...
とはいえ、Bot だけに限らず、 Azure に 興味があるのは事実です. GCP とかも手を広げたいところです.

会場は [DMM.com Labo](https://dmm-corp.com/company/labo/) さん. 始めて伺いましたが、すごく素晴らしい場所です.
[http://tokyo.serverlessconf.io/home.html#location](http://tokyo.serverlessconf.io/home.html#location)
![](/images/study/serverlessconf-tokyo-2017-02.png)

ウッディーな感じに、全方面に緑が配置された感じの場所で、写真奥のディスプレイ横には階段状のスペースにクッションなどもあります.
奥の左手にはカウンターのようなスペースがあり、コーヒーとかレンジが使える感じでした.

社員さんは、業務時間に自由に使えるとのことで「いいなぁ～～」が 止まりませんでした. **いいなぁ～～！**


## Create A Trainable Bot as a Service with Azure Functions and Logic Apps
Bot ワークショップ の 内容は、チュートリアルに則って Bot アプリ作りです.

ワークショップ の マテリアルは、こちら.
マイクロソフトさん の 公式ブログでも公開されているので、大丈夫でしょう.
master ブランチ は 英語版で、日本語翻訳版は `lang/jp` ブランチです.
こちらの手順に則って、Azure Functions に ボットを作っていきます.
https://github.com/Azure-Samples/azure-serverless-workshop-team-assistant
https://github.com/Azure-Samples/azure-serverless-workshop-team-assistant/tree/lang/jp

説明がしっかり書かれているので、手順通りに進めれば大丈夫です！
ボット は スクワイアー(squire)さん、騎士の従者さんです.
まずは、ボット に 話しかけると槍(ランスのAA) を 渡してくれます、助かります、スクワイアーさん！
```console
Here's your lance!
      mmm
     mmmmmmmmm
mmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmm
     mmmmmmmmm
     mmm
```

チュートリアルに従って、作っていけば大丈ぶ...
が、ハマった orz

やっぱ Azure 使い慣れていないってのが大きかったでしょうか.
あとマイクロソフトさんからアシストしてくれる方がたくさんいらっしゃったのですが、ちゃんと助けを求めなかったのがダメですね. セミナーとかではないのだから、ちゃんと助けを求めたり、いろいろ話をしないとですね.

なお、マイクロソフト の 中村 憲一郎さん が 解説を用意してくださりました.
ありがとうございます. こちらをもとに精進します.
- [Azure Serverless ワークショップ Deep Dive : モジュール 1 – 2]()
- [Azure Serverless ワークショップ Deep Dive : モジュール 3](https://blogs.msdn.microsoft.com/kenakamu/2017/11/02/azure-serverless-workshop-deep-dive-module-3/)
- [Azure Serverless ワークショップ Deep Dive : モジュール 4](https://blogs.msdn.microsoft.com/kenakamu/2017/11/02/azure-serverless-workshop-deep-dive-module-4/)
- [Azure Serverless ワークショップ Deep Dive : モジュール 5](https://blogs.msdn.microsoft.com/kenakamu/2017/11/02/azure-serverless-workshop-deep-dive-module-5/)
- [Azure Serverless ワークショップ Deep Dive : モジュール 6](https://blogs.msdn.microsoft.com/kenakamu/2017/11/02/azure-serverless-workshop-deep-dive-module-6/)
- [Azure Serverless ワークショップ Deep Dive : モジュール 7,8](https://blogs.msdn.microsoft.com/kenakamu/2017/11/02/azure-serverless-workshop-deep-dive-module-7-8/)


## Serverless Tech Challenge with AWS Serverless Services
ちょっと気になってた AWS テック・チャレンジ、お隣のスペースでやっていたので少しだけ拝見させていただきました.
テーマは下記から選択して、チーム開発するというものでした.
事前チームマッチングしていたようでしたが、メンバーの方が遅れていたのか「ひとりでマッチングサービス開発しましょう！」「いや、まずはチームのマッチングを～」みたいな話が聞こえて楽しそうでしたｗ
- 掲示板
- SNS
- マッチング
- Chat

優勝賞品は、なんと Amazon の ギフト券 10万円！ すごいなぁ.
今度はちゃんと参加できるようにスキルを磨いておきたいところです.


## Nov 3 / Conference
この時間がメイン、なのに諸事情により ざっと眺めて終了. 悔やまれます.
イベント会場ではマナーモード、ではなく **機内モードに** ですね...
休日の呼び出しは控えるマナーが求められる昨今ですが、自衛も必要だ.

スポンサーさん の 出展ブースを回り、いろいろいただきました. ありがとうございます.
![](/images/study/serverlessconf-tokyo-2017-03.jpg)

面白かったのは [スカイアーチネットワークス](https://www.skyarch.net/) サバカン 改め クラウド ラムダカレー！ "美味なコード" が とても気になりますｗ
AWS Lambda に かけているので `Lamb` なのは仕方ないですが、やっぱり `Rum` の方が気になります. すみません.
![](/images/study/serverlessconf-tokyo-2017-04.jpg)

マイクロソフトさん の フリスビー？ と [Flic](https://flic.io/) ボタン.
丸い白いのがボタンで、押すと赤い LED が かすかに光ます.
スマホ・アプリと連動してなんか動くらしい...
![](/images/study/serverlessconf-tokyo-2017-05.jpg)

えっと 一生懸命説明してくださったのですが、私が英語をわからないので... すみません.
こちらのツイートの カニオ さん に 説明頂いたのに.
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">Microsoftブースで<br>Microsoft Azure (MSのクラウドサービス)と<br>Azure Functions (AWSでいうラムダ)<br>の説明をするカニオさん！<br><br>ってかなんか来場者さん皆デフォルトで英語理解できる人ばかりで凄い！さすが<a href="https://twitter.com/hashtag/serverlessTokyo?src=hash&amp;ref_src=twsrc%5Etfw">#serverlessTokyo</a> <a href="https://t.co/Lu1BoMC0xx">pic.twitter.com/Lu1BoMC0xx</a></p>&mdash; ちょまど@MS入社して20ヶ月 (@chomado) <a href="https://twitter.com/chomado/status/926271427514286080?ref_src=twsrc%5Etfw">2017年11月3日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

てか、

"ってかなんか来場者さん皆デフォルトで英語理解できる人ばかりで凄い！さすが#serverlessTokyo"

orz、理解できるどころか、まともに聞こえてもいない人が単独でつまずいてたんですけどね...

カニオ さん、一生懸命説明してくださりありがとうございます！
この手の手バイスとか大好きなので、しっかり研究して楽しませていただきます！！



- - - -
そんなこんなで、終わった Serverlessconf Tokyo 2017 でした.
もっとガッツリと話を聞きたかったんですが、生憎と帰還指示が発動され退散に. (ところで、この帰還指示はサーバーレスで発動されているのだろうか)

最近は資料を公開してくださっている発表者さんが多いので助かります.
しっかりと読みこなし、サーバーレスの熱いウェーブに乗っていけるように頑張ります.

そして、来年こそはテック・チャレンジに発表、何かちゃんと挑戦できるように準備をしておかないと.


最後とはなりますが、発表者さま、スポンサーさま、スタッフさま、みなさま、ステキなイベントをありがとうございます！！
