---
title: Slack の ボット で 今日の天気を通知する - JSON 取得編
date: 2017-01-24
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
---

![](/images/slack/slack.png "Slack")

ボットを使って、Slack に 今日の天気を通知してもらうようにしたいと思います.
天気関連はいろいろなサービスがあり、それを使うのも手なのですが自分でカスタマイズした細かいところにこだわったツールにしたいと思うので、ボットに教えてもらうことにします.

**作業環境**
- Node.js 6.9.1 LTS


## 天気の情報を提供してくれるサービス
今回は [OpenWeatherMap](http://openweathermap.org/) の API を 使って情報を取得したいと思います.
[Openweathermap - Wikipedia](https://ja.wikipedia.org/wiki/Openweathermap) によると、OpenWeatherMap は 各種気象データを 無料 で API 提供してくれるサービスとのことです. しかも全データは、[CC BY-SA](https://creativecommons.org/licenses/by-sa/4.0/) に 準じるとのことで、情報の改変や商用利用が許可されています. ありがたい！
※ OpenWeatherMap の ToS は こちら [Terms of Service](http://openweathermap.org/terms)

無料で利用できますが、主な利用条件は以下となります.
- 1分間 の API 呼び出し回数は 60回
- 現在の天気 に 加え、3時間ごと 5日間の予報が使える
- 天気情報の更新 は **2時間以内**
※ 情報の更新について、Wikipedia に 10分以内となってますが、10分以内は Pro 以上で、Free は 2時間以内となっている
![](/images/slack/weather/01.png)

*参考情報*
天気情報が提供されているサービスについては、こちらのサイトを参考にさせて頂きました. 素晴らしい情報 ありがとうございます！
- [【WebAPI】天気予報等の気象情報を提供するWebAPIについての概要 | illarena](http://illarena.com/programming/webapi/openweathermap/webapi_tenki/)


## API キー の 取得
OpenWeatherMap の Sing Up サイト [https://home.openweathermap.org/users/sign_up](https://home.openweathermap.org/users/sign_up) へ アクセスします.
全ての項目を入力、ToS/PP を 確認し同意したら `I agree ~~~` に チェックして、[Create Account] ボタンをクリックします.
![](/images/slack/weather/02.png)

利用目的を聞かれるので、法人利用の場合は [Company] も 入力し、[Purpose] を 選択します.
![](/images/slack/weather/03.png)

無事、API キー が 発行されました.
Welcome メール は 届きますが URL クリックによる認証などがなく、簡単にサインアップさせてくれるので助かります.
![](/images/slack/weather/04.png)


## 現在の天気情報を取得する
Botkit に 組み込むので、Node.js で コーディングします.
※ [API_KEY] を 上記で取得した API キー に 置き換えます.
```javascript
const http = require('http');
http.get("http://api.openweathermap.org/data/2.5/weather?id=1850147&units=metric&appid=[API_KEY]", (response) => {
    let buffer = '';
    response.setEncoding('utf8').on('data', (chunk) => {  buffer += chunk;  });
    response.on('end', () => {
        let current = JSON.parse(buffer);
        console.log(current)
    });
});
```

HTTP GET で 取得して、JSON 出力をする簡単なものになります. 特別な実装部分はありません.
URL の パラメーターは以下となります.
- `id=1850147` は、天気情報を取得する 都市 の ID (今回は東京、ID の 取得は後述)
- `units=metric` は、摂氏華氏(°C/°F) を気温を摂氏にするために指定 (デフォルトは華氏)
- `appid=[API_KEY]` で、API キーを指定

実行すると以下のような JSON が 出力されます.
```json
{ coord: { lon: 139.69, lat: 35.69 },
  weather:
   [ { id: 801,
       main: 'Clouds',
       description: 'few clouds',
       icon: '02d' } ],
  base: 'stations',
  main:
   { temp: 6.85,
     pressure: 1019.67,
     humidity: 80,
     temp_min: 6.85,
     temp_max: 6.85,
     sea_level: 1023.45,
     grnd_level: 1019.67 },
  wind: { speed: 2.96, deg: 2.5058 },
  clouds: { all: 12 },
  dt: 1485145194,
  sys:
   { message: 0.0063,
     country: 'JP',
     sunrise: 1485121634,
     sunset: 1485158355 },
  id: 1850147,
  name: 'Tokyo',
  cod: 200 }
```

天気は、`weather[0].main` に なります. なぜ weather が 配列なのかはちょっとわかりませんが...
なお、今回の例では `Clouds` なので曇りです.
JSON の 内容については、次回にしたいと思います.
※ OpenWeatherMap の 現在の天気 API 仕様 は こちら、[Current weather data](http://openweathermap.org/current)


## 3時間ごと 5日間 の 天気予報を取得
先の実装と変わるのは URL の パス が `/weather` から `/forecast` に なる点です.
```javascript
const http = require('http');
http.get("http://api.openweathermap.org/data/2.5/forecast?id=1850147&units=metric&appid=[API_KEY]", (response) => {
    let buffer = '';
    response.setEncoding('utf8').on('data', (chunk) => {  buffer += chunk;  });
    response.on('end', () => {
        let forecast = JSON.parse(buffer);
        console.log(forecast)
    });
});
```

実行すると以下のような JSON が 出力されます.
```json
{ city:
   { id: 1850147,
     name: 'Tokyo',
     coord: { lon: 139.691711, lat: 35.689499 },
     country: 'JP',
     population: 0,
     sys: { population: 0 } },
  cod: '200',
  message: 0.0147,
  cnt: 38,
  list:
   [ { dt: 1485151200,
       main:
        { temp: 6.12,
          temp_min: 6.12,
          temp_max: 6.12,
          pressure: 1022.42,
          sea_level: 1023.53,
          grnd_level: 1022.42,
          humidity: 59,
          temp_kf: 0 },
       weather:
        [ { id: 802,
            main: 'Clouds',
            description: 'scattered clouds',
            icon: '03d' } ],
       clouds: { all: 36 },
       wind: { speed: 7.62, deg: 319.001 },
       sys: { pod: 'd' },
       dt_txt: '2017-01-23 06:00:00' },
     { ... (3時間ごとの繰り返し) }
   ]
}
```

天気は、予報 38回分なのでリスト `list` に 入っています. その中の構造は現在の天気とほぼ同じです.
直近の予報を取得するには `list[0].weather[0].main` に なります. 今回の例では `Clouds` なので曇りです.
※ OpenWeatherMap の 天気予報 API 仕様 は こちら、[5 day weather forecast](http://openweathermap.org/forecast5)


## 都市 ID の 取得方法 と 他の検索方法 について
OpenWeatherMap の 天気情報および予報について、取得する都市や地域の指定は以下の 4種類で指定できます.

| 指定方法 | 　クエリ例 | 概要 |
|:---------|:---------|:-----|
| 都市名   | 　[q=tokyo,jp](http://openweathermap.org/find?q=tokyo,jp) | 都道府県以下は曖昧になり意図とは異なる場合も. |
| 都市 ID  | 　[id=1850147](http://openweathermap.org/city/1850147) | OpenWeatherMap の ID 指定. 確実！ |
| 地理座標 | 　[lat=35.69&lon=139.69](http://openweathermap.org/weathermap?lat=35.69&lon=139.69#) | 明確な指定方法と思われるが緯度経度を調べるのが大変... |
| 郵便番号 | 　zip=1000005,jp | これも明確な指定方法と思われる |

いろいろな方法がありますが当然のことながら、すべての都市や緯度経度に天気観測のステーションがあるわけではなく、近隣のステーション情報へのマッピングがされていると考えると、都市 ID が 一番確実だと考え、今回は 都市 ID で 指定するようにしました.

また、公式にも "We recommend to call API by city ID to get unambiguous result for your city. -- [OpenWeatherMap](http://openweathermap.org/current#cityid)" と 謳われているので、都市 ID で 指定したほうがよいのでしょう.

都市 ID は [http://bulk.openweathermap.org/sample/](http://bulk.openweathermap.org/sample/) から取得します.
![](/images/slack/weather/05.png)

`city.list.json.gz` が 都市情報のデータになります.
全世界の都市データが入っており、2017年1月現在で 全 209,578 件 で、`"country":"JP"` でも 1,402件です. すごい.
そして、なぜか、`御影` `芦屋` `松山市` は 漢字で都市名が入っている. なぜだろう...

以下に 日本 と 東京、`御影`、`芦屋`、`松山市` を 抜粋し、Google マップ による緯度経度検索結果の場所を載せておきます.
`_id` の 値が 都市 ID になります. (実際はスペースなし、投稿用に整形済み)
```json
{ "_id": 1861060, "name": "Japan",         "country": "JP", "coord": { "lon": 139.753098, "lat": 35.68536  }}  // 皇居
{ "_id": 1850147, "name": "Tokyo",         "country": "JP", "coord": { "lon": 139.691711, "lat": 35.689499 }}  // 東京都庁
{ "_id": 1850144, "name": "Tōkyō-to",      "country": "JP", "coord": { "lon": 139.691711, "lat": 35.689499 }}  // 東京都庁
{ "_id": 1864529, "name": "Chiyoda-ku",    "country": "JP", "coord": { "lon": 139.753632, "lat": 35.694019 }}  // 千代田区役所
{ "_id": 7302982, "name": "御影",          "country": "JP", "coord": { "lon": 135.252426, "lat": 34.724258 }}  // 阪急 御影駅？
{ "_id": 7302983, "name": "芦屋",          "country": "JP", "coord": { "lon": 135.30719,  "lat": 34.733921 }}  // ＪＲ 芦屋駅？
{ "_id": 1864985, "name": "Ashiya",        "country": "JP", "coord": { "lon": 135.302643, "lat": 34.728069 }}  // 阪急 芦屋駅？
{ "_id": 7303001, "name": "松山市",        "country": "JP", "coord": { "lon": 132.756729, "lat": 33.83905  }}  // 松山市内？
{ "_id": 1926099, "name": "Matsuyama-shi", "country": "JP", "coord": { "lon": 132.765747, "lat": 33.839161 }}  // 松山市役所
```



- - - -
簡単に利用できる API を 無料で公開してくれるサービスのおかげで、容易に天気情報が取得できました. 毎朝の天気を Slack に 流すだけなら、もう Botkit に 組み込めそうですが、取得できたデータをしっかり把握したいので、次回は JSON の 内容について確認したいと思います.
[Slack の ボットで定時アクション](/2017/01/12/Slackのボットで定時アクション/) が できるようになったところで、定番の天気予報に入らず [Slack の ボット で JRA 競馬 の 開催日を通知](/2017/01/21/SlackのボットでJRA競馬の開催日を通知する-Slackボット実装編/) と いきなり脱線しましたが、軌道修正して基本の天気予報を流せるようにしていきたいと思います.
