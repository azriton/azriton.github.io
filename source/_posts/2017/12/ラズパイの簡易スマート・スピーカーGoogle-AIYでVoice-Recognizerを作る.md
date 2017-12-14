
---
title: ラズパイ の 簡易スマート・スピーカー Google AIY で Voice Recognizer を 作る
date: 2017-12-13
comments: true
categories: 電子工作
tags:
- Raspberry Pi
- Google AIY
---

<link rel="stylesheet" href="/assets/ribbon.css" />
<div class="ribbon"><span><a class="title" href="https://qiita.com/advent-calendar/2017/ouch-hack">おうちハック Advent Calendar 2017</a> 13日目</span></div>

![](/images/raspi/google-aiy/google-aiy.jpg "Google AIY Voice Kit")

この記事は「[おうちハック Advent Calendar 2017](https://qiita.com/advent-calendar/2017/ouch-hack)」 13日目になります.
せっかくの Advent Calendar なので、何かネタをと思って考えてたのですが、うまく作れなかった... ので、Google AIY の キットを使っての話にしたいと思います.


**作業環境**
- Raspberry Pi 3 Model B
- Google AIY Voice Kit
- GCP
- Xming (必要に応じて)


## 最初は何を作りたかったのか
本題に入る前に、そもそも何を作ろうと思い、作れなかったのかについて少々. 作りたかったものはこれ、 [Google AIY](https://aiyprojects.withgoogle.com/) の [Voice Kit](https://aiyprojects.withgoogle.com/voice) を ラズパイ・ゼロ で実現！です.
![](/images/raspi/google-aiy/voice-recognizer/01.jpg)

Voice Kit については、予約購入できたので [作って](/2017/11/09/ラズパイの簡易スマート・スピーカーGoogle-AIYを組み立てる/) いました. (なかなか作業時間が取れず、ついに１ヶ月もかかってますが...)

その完成を目指すのもよいかと思ったのですが、同様のことがラズパイ・ゼロできたらコンパクトでよいと思い挑戦してました. なぜか [Speaker pHat](https://shop.pimoroni.com/products/speaker-phat) が 壊れてしまい、どうしても動作してくれないためタイムアウト.　Voice Kit の 完成を目指すことにしました.　無念...

[Google Home](https://store.google.com/product/google_home) 簡易版ってだけでなく、下記のような面白いこともできそうだったのになぁと. いずれ再挑戦です！

Tomy Mr Money Google AIY Assistant | CIRCUITBEARD
https://circuitbeard.co.uk/2017/11/18/tomy-mr-money-google-aiy-assistant/

I turned a Furby into an Amazon Echo. Introducing: Furlexa - howchoo
https://howchoo.com/g/otewzwmwnzb/amazon-echo-furby-using-raspberry-pi-furlexa


## Google AIY Voice Kit とは？
本題の Google AIY Voice Kit ですが、これは Raspberry Pi 3 と 組み合わせることで、簡易的なスマート・スピーカーを作ることができるキットです.
ラズパイなので、さらに自分で機能を追加できるので Google Home ではできない おうちハック が 目指せます！ 例えばラズパイ・カメラをつけることもできます. ラズパイ本体の GPIO は Voice Hat で 埋まっていますが、Voice Hat に Servo を つけたりもできるようです.

品物は 例によって [Pimoroni](https://shop.pimoroni.com/products/google-aiy-voice-kit) さん 始め、国内でも　[スイッチサイエンス](https://www.switch-science.com/catalog/3550/) さん や [KSY](https://raspberry-pi.ksyic.com/news/page/nwp.id/65) さん で購入できます. (2017年12月現在、国内は品薄ですが)

また Raspberry Pi 3 (と マイクロ SD カード、電源) が 別途必要で、箱の中に入れるので ほぼ占有されます.
その他、工具としては小さいドライバ と 両面テープ が 必要となります.

[必要なもの は ページの最後にまとめ](/2017/12/13/ラズパイの簡易スマート・スピーカーGoogle-AIYでVoice-Recognizerを作る/) たので、よろしければ ご確認ください.

国内の販売店さん は 日本語の説明書をつけてくれるようですが、Pimoroni さん で 購入した場合は 英語の冊子になります.
こちらの [公式 オンライン・ドキュメント](https://aiyprojects.withgoogle.com/voice) と ほぼ同じで、写真がたくさんついているので英語が苦手でも作ることができるかと思います.

私の作成記はこちらになります.
- [ラズパイ の 簡易スマート・スピーカー Google AIY を 組み立てる](/2017/11/09/ラズパイの簡易スマート・スピーカーGoogle-AIYを組み立てる/)
- [ラズパイ の 簡易スマート・スピーカー Google AIY の 初期セットアップ](/2017/11/15/ラズパイの簡易スマート・スピーカーGoogle-AIYの初期セットアップ/)

今回は GCP(Google Cloud Platform) に 接続して、Voice Recognizer を 完成させます.


## GCP への サインアップ と プロジェクトの作成
GCP の Cloud Console [https://console.cloud.google.com/](https://console.cloud.google.com/) へ アクセスします.
画面上部 [プロジェクトを選択] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/02.png)

プロジェクトの選択ダイアログが表示されるので、ダイアログ右上 [＋] ボタンをクリックします.
![](/images/raspi/google-aiy/voice-recognizer/03.png)

新しいプロジェクト作成画面が表示されるのでプロジェクト名を入力し、[作成] を クリックします.
今回は voice-recognizer としました.
![](/images/raspi/google-aiy/voice-recognizer/04.png)

最初の画面に戻るので、画面上部 [プロジェクトを選択] を クリックしてプロジェクトの一覧を表示します.
作成したプロジェクトの名前をクリックします.
![](/images/raspi/google-aiy/voice-recognizer/05.png)


## Google Assistant API の 有効化
左メニュー の [API と サービス] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/06.png)

画面中央上部 の [API と サービスの有効化] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/07.png)

API ライブラリが 表示されるので [API と サービスを検索] に `assistant` を 入力します.
![](/images/raspi/google-aiy/voice-recognizer/08.png)

Google Assistant API が 表示されるので、クリックします.
![](/images/raspi/google-aiy/voice-recognizer/09.png)

Google Assistant API の 詳細 が 表示されるので、[有効にする] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/10.png)


## OAuth 2.0 の 設定
Google Assistant API の 画面で "認証情報が必要" と 警告されている通り、認証設定が必要となります.
左メニュー から [認証情報] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/11.png)

画面上側の [OAuth 同意画面] を 選択します.
これを設定しないと認証情報の作成時に警告され進めないので、あらかじめ作成しておきます.
![](/images/raspi/google-aiy/voice-recognizer/12.png)

最小限 [ユーザーに表示するサービス名] を 入力し、[保存] ボタンをクリックします.
![](/images/raspi/google-aiy/voice-recognizer/13.png)

認証情報の画面に戻るので、画面中央 [認証情報の作成] を クリックし、[OAuth クライアント ID] を 選択します.
![](/images/raspi/google-aiy/voice-recognizer/14.png)

クライアント ID の 作成画面が表示されるので、以下の情報を設定し、[作成] を クリックします.
- アプリケーションの種類: その他
- 名前: 任意の文字列 (今回は voice-recognizer と しました、クライアントだからホスト名のがよかったかな)
![](/images/raspi/google-aiy/voice-recognizer/15.png)

OAuth クライアント の 情報が表示されます. 
この情報はファイルとしてダウンロードするので [OK] を クリックして進めます.
![](/images/raspi/google-aiy/voice-recognizer/16.png)

認証情報の一覧に先ほど作成した ID が あるので、右側の 下矢印 を クリックしてダウンロードします.
このファイルは後程使うのと、サービスにアクセスるための重要情報になるので大切に管理します.
![](/images/raspi/google-aiy/voice-recognizer/17.png)


## Gooogle My Activity の 設定
アクティビティ管理 [https://myaccount.google.com/activitycontrols](https://myaccount.google.com/activitycontrols) へ アクセスし、以下の項目を有効化します.
- ウェブとアプリのアクティビティ (Web and app activity)
- 端末情報 (Device information)
- 音声アクティビティ (Voice and audio activity)
![](/images/raspi/google-aiy/voice-recognizer/18.png)


## Voice Kit の 設定
先ほど作成した認証情報のファイルを Voice Kit の `/home/pi/assistant.json` へ コピーします.
デモ・スクリプトが用意されているので起動します. デモは以下の３つです.
- `assistant_library_demo.py` : 「おーけー ぐーぐる」の 音声に反応して起動し、会話をしてくれます
- `assistant_grpc_demo.py` : Voice Kit の ボタンに反応して起動し、会話をしてくれます
- `cloud_speech_demo.py` : Google Cloud Speech API を使って、独自のアプリを作るためのデモです

今回は 簡易 Google Home ということで、 `assistant_library_demo.py` を 起動します.
なお、最初に起動するときにブラウザが立ち上がり Google の OAuth を 行います. そのため Voice Kit に ディスプレイとキーボードをつなぐ必要があります. 今回は無かったので [Xming](http://www.straightrunning.com/XmingNotes/) で 代用しました.

また 公式 オンライン・ドキュメント の [Using your device](https://aiyprojects.withgoogle.com/voice#users-guide-3-using-your-device) だと デスクトップ の [Start dev terminal] を ダブルクリックして、 `src/assistant_library_demo.py` を 実行するだけで起動するように書かれていますが、SSH 経由のためか `ImportError: No module named 'google'` エラーとなってしまいました. `venv` 環境に入れないとダメとのことで、こちらの Issues [#133 executables breaking on ImportError after fresh clone on latest Raspbian Strech · Issue #133 · google/aiyprojects-raspbian](https://github.com/google/aiyprojects-raspbian/issues/133) に 上がっていました. 助かりました.

SSH から 以下のように実行しました.
```console
pi@raspberry:~ $ cd ./AIY-voice-kit-python
pi@raspberry:~/AIY-voice-kit-python $ source env/bin/activate
(env) pi@raspberry:~/AIY-voice-kit-python $ src/assistant_library_demo.py
Say "OK, Google" then speak, or press Ctrl+C to quit...
```

実行するとブラウザが立ち上がり、Google 認証画面が表示されます.
![](/images/raspi/google-aiy/voice-recognizer/19.png)

先ほど設定した Google アカウントでサインインします.
![](/images/raspi/google-aiy/voice-recognizer/20.png)

OAuth で 認可を求められます. 先ほど設定した [Voice Recognizer] の 名前が表示され、Google アシスタントの利用許可が求められています. [許可] を クリックします.
![](/images/raspi/google-aiy/voice-recognizer/21.png)

認証フロー環境のめっせーが出るのでブラウザを閉じます.
![](/images/raspi/google-aiy/voice-recognizer/22.png)


あとは、「おーけー ぐーぐる」して話しかけて楽しみましょう！
ただし英語で orz


以上、おうちハック Advent Calendar 2017 13日目でした.

----

[おうちハック Advent Calendar 2017](https://qiita.com/advent-calendar/2017/ouch-hack) 、いろいろな人のハックがあり、とても勉強になります.

明日 14日目 は [@ponkio-o](https://qiita.com/ponkio-o)さん、「ラズパイとSuicaで玄関を開ける」とのこと。楽しみです！
*2017.12.14 追記*　14日目は @ponkio-o さん のところ、間違えて記載しておりました。訂正します。申し訳ありません。


----


## Google AIY Voice Kit に 必要なもの

### Raspberry Pi 3
ラズパイがないと始められないので必要になります. 箱の中に入れてしまうので、ほぼ占有されます. 
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41zcKgUQXtL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01CFHHYF4%2Fref%3Dnosim" target="_blank" >Raspberry Pi 3 MODEL B 【RS正規流通品】</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Raspberry Pi 2016-01-28    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Raspberry Pi 3 の 電源
Raspberry Pi 3 は 5V/3A の 電源が必要になります. スマホの充電アダプタでは出力が足りない場合もあるので確認が必要です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/41p5wekKaIL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01N8ZIJL8%2Fref%3Dnosim" target="_blank" >Raspberry Pi用電源セット(5V 3.0A)－Pi3フル負荷検証済</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> TechShare     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FRaspberry%2520Pi%25203%2520%25E9%259B%25BB%25E6%25BA%2590%25203A%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### マイクロ SD カード
ラズパイ の OS や ストレージに必要です. 16GB あれば十分だと思いますが、用途次第なので お好みのサイズで用意します.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51JBMptiJgL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB015J44QS8%2Fref%3Dnosim" target="_blank" >【Amazon.co.jp限定】Transcend microSDHCカード 16GB Class10 UHS-I対応 無期限保証 Nintendo Switch / 3DS 動作確認済 TS16GUSDU1PE (FFP)</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> トランセンド・ジャパン 2015-10-02    </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FTranscend%2520microSDHC%25E3%2582%25AB%25E3%2583%25BC%25E3%2583%2589%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### ドライバーセット
スピーカー配線のコネクタをしめるのに使います. 細いドライバーであればなんでも大丈夫です.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0016VCJLU%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/512vdu5RFFL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0016VCJLU%2Fref%3Dnosim" target="_blank" >ベッセル(VESSEL) ドライバーセット ファミドラ8 TD-800</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> ベッセル     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%25E3%2583%2589%25E3%2583%25A9%25E3%2582%25A4%25E3%2583%2590%25E3%2583%25BC%25E3%2582%25BB%25E3%2583%2583%25E3%2583%2588%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2F%25E3%2583%2589%25E3%2583%25A9%25E3%2582%25A4%25E3%2583%2590%25E3%2583%25BC%25E3%2582%25BB%25E3%2583%2583%25E3%2583%2588%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### 両面テープ
マイクを箱の天板に貼り付けるのに使います. 持っていそうで意外となかったりも. (今回は無かった...)
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB000IGZZ4M%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51n8VDAfiyL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB000IGZZ4M%2Fref%3Dnosim" target="_blank" >ニチバン 両面テープ ナイスタック 一般タイプ 15mm×20m NW-15</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> ニチバン     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%25E3%2583%258B%25E3%2583%2581%25E3%2583%2590%25E3%2583%25B3%2520%25E4%25B8%25A1%25E9%259D%25A2%25E3%2583%2586%25E3%2583%25BC%25E3%2583%2597%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2F%25E3%2583%258B%25E3%2583%2581%25E3%2583%2590%25E3%2583%25B3%2520%25E4%25B8%25A1%25E9%259D%25A2%25E3%2583%2586%25E3%2583%25BC%25E3%2583%2597%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>

### Google  AIY
Google  AIY Voice Kit そのもの. 一応載せておきますが 出始めなので Amazon.co.jp さん だと、高いですね. [KSY](https://raspberry-pi.ksyic.com/news/page/nwp.id/65) さん 待ちでしょうか. [Pimoroni](https://shop.pimoroni.com/products/google-aiy-voice-kit) さん は 2017年12月現在 在庫があるので ￡20.83 + 送料 ￡5.50 ＝ 約4,000円です.
Pimoroni さん での お買い物方法は、こちら「[Raspberry Pi Zero の 購入](/2016/11/13/Raspberry-Pi-Zeroの購入/)」も ご参照いただければ幸いです.
<div class="kaerebalink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="kaerebalink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0779BJY1N%2Fref%3Dnosim" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/411Ax0K6zqL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="kaerebalink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="kaerebalink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB0779BJY1N%2Fref%3Dnosim" target="_blank" >Google AIY Voice Kit - 日本語補足説明書付き</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="kaerebalink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="http://kaereba.com" rel="nofollow" target="_blank">カエレバ</a></div></div><div class="kaerebalink-detail" style="margin-bottom:5px;"> Google     </div><div class="kaerebalink-link1" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3DGoogle%2520AIY%26__mk_ja_JP%3D%25E3%2582%25AB%25E3%2582%25BF%25E3%2582%25AB%25E3%2583%258A" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/kl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=54&pc_id=54&pl_id=616&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fsearch.rakuten.co.jp%2Fsearch%2Fmall%2FGoogle%2520AIY%2F-%2Ff.1-p.1-s.1-sf.0-st.A-v.2%3Fx%3D0" target="_blank" >楽天市場で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=54&pc_id=54&pl_id=616" width="1" height="1" style="border:none;"></div></div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
これで最近はやりのスマートスピーカーが我が家にもやってきました.
欲しいなぁと思っていたところではありましたが、やっぱり自分で作ったものがよかったので Google AIY の Voice Kit が 出てくれてとても嬉しいです.

今回は Google Home ならぬ Google Assist を 作りましたが、Amazon Echo ならぬ Amazon Alexa も ラズパイに入れられるので試してみたいところです.

スマスピ(?) で おうちハック を 加速させよう！
