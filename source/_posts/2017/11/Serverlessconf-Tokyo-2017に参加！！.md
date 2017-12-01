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
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4873118069" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51wqAE5rXxL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4873118069" target="_blank" >サーバーレスシングルページアプリケーション ―S3、AWS Lambda、API Gateway、DynamoDB、Cognitoで構築するスケーラブルなWebサービス</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Ben Rady オライリージャパン 2017-06-23    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4873118069" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%2583T%2581%255B%2583o%2581%255B%2583%258C%2583X%2583V%2583%2593%2583O%2583%258B%2583y%2581%255B%2583W%2583A%2583v%2583%258A%2583P%2581%255B%2583V%2583%2587%2583%2593%2520%2581%255CS3%2581AAWS%2520Lambda%2581AAPI%2520Gateway%2581ADynamoDB%2581ACognito%2582%25C5%258D%255C%2592z%2582%25B7%2582%25E9%2583X%2583P%2581%255B%2583%2589%2583u%2583%258B%2582%25C8Web%2583T%2581%255B%2583r%2583X%26__mk_ja_JP%3D%2583J%2583%255E%2583J%2583i%26url%3Dnode%253D2275256051" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F15000580%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-87-311806-2%2520%257C%25204-873-11806-2%2520%257C%25204-8731-1806-2%2520%257C%25204-87311-806-2%2520%257C%25204-873118-06-2%2520%257C%25204-8731180-6-2" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>




- - - -
そんなこんなで、終わった Serverlessconf Tokyo 2017 でした.
もっとガッツリと話を聞きたかったんですが、生憎と帰還指示が発動され退散に. (ところで、この帰還指示はサーバーレスで発動されているのだろうか)

最近は資料を公開してくださっている発表者さんが多いので助かります.
しっかりと読みこなし、サーバーレスの熱いウェーブに乗っていけるように頑張ります.

そして、来年こそはテック・チャレンジに発表、何かちゃんと挑戦できるように準備をしておかないと.


最後とはなりますが、発表者さま、スポンサーさま、スタッフさま、みなさま、ステキなイベントをありがとうございます！！
