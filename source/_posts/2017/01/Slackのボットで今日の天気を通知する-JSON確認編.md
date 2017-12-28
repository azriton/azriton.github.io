---
title: Slack の ボット で 今日の天気を通知する - JSON 確認編
date: 2017-01-29
updated: 2017-01-29
comments: true
categories: ボット
tags:
- Slack
- Botkit
- Node.js
- JavaScript
---

![](/assets/slack/slack.png "Slack")

OpenWeatherMap の サービスを使って、[天気情報を取得できる](/2017/01/24/Slackのボットで今日の天気を通知する-JSON取得編/)ようになりました.
今回は取得した JSON の 内容を確認し、必要な情報が使えるようにしたいと思います. 雨と雪の情報もほしかったので、札幌市の天気を取得しました.

**作業環境**
- Node.js 6.9.1 LTS


## 現在の天気情報 JSON
```json
{ coord: { lon: 141.35, lat: 43.06 },
  weather:
   [ { id: 501,
       main: 'Rain',
       description: 'moderate rain',
       icon: '10d' } ],
  base: 'stations',
  main:
   { temp: 4.01,
     pressure: 994.64,
     humidity: 97,
     temp_min: 4.01,
     temp_max: 4.01,
     sea_level: 1008.09,
     grnd_level: 994.64 },
  wind: { speed: 9.56, deg: 194.002 },
  rain: { 3h: 4.905 },
  clouds: { all: 88 },
  dt: 1485498017,
  sys:
   { message: 0.0083,
     country: 'JP',
     sunrise: 1485467687,
     sunset: 1485502824 },
  id: 2128295,
  name: 'Sapporo-shi',
  cod: 200 }
```

| ノード                 | 参考値                         | 概要                     |
|:-----------------------|:-------------------------------|:-------------------------|
| `coord`                | `{ lon: 141.35, lat: 43.06 }`  | 都市の緯度経度           |
| `weather.id`           | `501`                          | 天気状態 の ID*          |
| `weather.main`         | `Rain`                         | 天気状態 の グループ名*  |
| `weather.description`  | `moderate rain`                | 天気状態*                |
| `weather.icon`         | `10d`                          | 天気アイコン ID*         |
| `main.temp`            | `4.01`                         | 気温                     |
| `main.pressure`        | `994.64`                       | 気圧、単位は hPa         |
| `main.humidity`        | `97`                           | 湿度、単位は %           |
| `main.temp_min`        | `4.01`                         | 現時点での最低気温       |
| `main.temp_max`        | `4.01`                         | 現時点での最高気温       |
| `main.sea_level`       | `1008.09`                      | 海上の気圧、単位は hPa   |
| `main.grnd_level`      | `994.64`                       | 地上の気圧、単位は hPa   |
| `wind.speed`           | `9.56`                         | 風速、単位は メートル/秒 |
| `wind.deg`             | `194.002`                      | 風向、北 0 の 時計回り   |
| `clouds`               | `{ all: 88 }`                  | 曇り度合、単位は %       |
| `rain.3h`              | `4.905`                        | 過去3時間の降雨量        |
| `snow.3h`              |                                | 過去3時間の降雪量        |
| `dt`                   | `1485498017`                   | データ計算時間、Unix,UTC |
| `sys.country`          | `JP`                           | 国コード                 |
| `sys.sunrise`          | `1485467687`                   | 日の出時間、Unix,UTC     |
| `sys.sunset`           | `1485502824 `                  | 日の入り時間、Unix,UTC   |
| `id`                   | `2128295`                      | 都市 ID                  |
| `name`                 | `Sapporo-shi`                  | 都市名                   |
* [Weather condition codes](http://openweathermap.org/weather-conditions) に 対応表がある
※ API ドキュメントに `Internal parameter` とあるものは除く


## 3時間ごと 5日間 の 天気予報 JSON
```json
{ city:
   { id: 2128295,
     name: 'Sapporo-shi',
     coord: { lon: 141.346939, lat: 43.064171 },
     country: 'JP',
     population: 0,
     sys: { population: 0 } },
  cod: '200',
  message: 0.1264,
  cnt: 38,
  list:
   [ { dt: 1485507600,
       main:
        { temp: 0.15,
          temp_min: 0.15,
          temp_max: 0.15,
          pressure: 1000.35,
          sea_level: 1013.94,
          grnd_level: 1000.35,
          humidity: 91,
          temp_kf: 0 },
       weather:
        [ { id: 500,
            main: 'Rain',
            description: 'light rain',
            icon: '10n' } ],
       clouds: { all: 56 },
       rain: { 3h: 0.2975},
       snow: { 3h: 0.1575},
       wind: { speed: 8.3, deg: 258.503 },
       sys: { pod: 'n' },
       dt_txt: '2017-01-27 09:00:00' },
     { ... (3時間ごとの繰り返し) }
   ]
}
```

| ノード                     | 参考値                                | 概要                     |
|:---------------------------|:--------------------------------------|:-------------------------|
| `city.id`                  | `2128295`                             | 都市 ID                  |
| `city.name`                | `Sapporo-shi`                         | 都市名                   |
| `city.coord`               | `{ lon: 141.346939, lat: 43.064171 }` | 都市の緯度経度           |
| `city.country`             | `JP`                                  | 国コード                 |
| `cnt`                      | `38`                                  | レコード件数             |
| `list.dt`                  | `1485507600`                          | データ計算時間、Unix,UTC |
| `list.main.temp`           | `0.15`                                | 気温                     |
| `list.main.temp_min`       | `0.15`                                | 計算時の最低気温         |
| `list.main.temp_max`       | `0.15`                                | 計算時の最高気温         |
| `list.main.pressure`       | `1000.35`                             | 気圧、単位は hPa         |
| `list.main.sea_level`      | `1013.94`                             | 海上の気圧、単位は hPa   |
| `list.main.grnd_level`     | `1000.35`                             | 地上の気圧、単位は hPa   |
| `list.main.humidity`       | `91`                                  | 湿度、単位は %           |
| `list.weather.id`          | `500`                                 | 天気状態 の ID*          |
| `list.weather.main`        | `Rain`                                | 天気状態 の グループ名*  |
| `list.weather.description` | `light rain`                          | 天気状態*                |
| `list.weather.icon`        | `10n`                                 | 天気アイコン ID*         |
| `list.clouds.all`          | `56`                                  | 曇り度合、単位は %       |
| `list.wind.speed`          | `8.3`                                 | 風速、単位は メートル/秒 |
| `list.wind.deg`            | `258.503`                             | 風向、北 0 の 時計回り   |
| `list.rain.3h `            | `0.2975`                              | 過去3時間の降雨量        |
| `list.snow.3h`             | `0.1575`                              | 過去3時間の降雪量        |
| `list.dt_txt`              | `2017-01-27 09:00:00`                 | データ計算時間、UTC      |
* [Weather condition codes](http://openweathermap.org/weather-conditions) に 対応表がある
※ API ドキュメントに `Internal parameter` とあるものは除く


## データの利用について検討
現在の天気と予報では JSON の パラメータ名が異なるものの、おおよそで同じような内容になっています.

天気を表す部分では `weather.main` が 中心で、グループ名(`main`) もしくは 天気状態(`description`) や アイコンを使って天気を表す感じでしょうか.
アイコンは [http://openweathermap.org/img/w/10d.png](http://openweathermap.org/img/w/10d.png) の 最後の部分 `10d` に JSON で 取得した値を入れることで取得できます. 昼夜で `10d`、`10n` のようにアイコンが変わるようです.

気温などは `main` に 入っています. ここは単位を付けるだけで簡単に使えそうです. `temp_min` と `temp_max` は 大きい都市で気温差が出るようなケースに値が変わるようです.

風、特に風向きについては北を 0° として[表現するよう](https://ja.wikipedia.org/wiki/%E9%A2%A8%E5%90%91)です. 現在の天気の JSON では `wind: { speed: 9.56, deg: 194.002 }` と なっていたので、南南西の風 9.5 メートル/秒 ですね.

降雨量 と 降雪量 は `rain.3h` と `snow.3h` に 過去3時間のデータが入ります. 降っていない場合は JSON に パラメータがないか、`3h` の パラメータがない状態となります. プログラム言語によって工夫が必要かもしれません. API ドキュメントには単位が乗ってませんでしたがミリメートルと思われます. 雨と雪の両方のデータが入っているケースがあり、その場合はどんな状況なのか、ちょっと気になります.

現在の天気のデータ計算時間は `dt` に 入っており、Unix,UTC と なっているのですが、Unix **秒** です. `dt` や `sunrise`、`sunset` を 扱う場合は、プログラム言語の処理系に合わせての対応が必要です.



- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51SYfM4adrL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >はじめてみようSlack 使いこなすための31のヒント</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">Slack研究会 パーソナルメディア 2016-08-03    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4893623265" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01L7HCBT2%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14364488%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>            <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-89-362326-3%2520%257C%25204-893-62326-3%2520%257C%25204-8936-2326-3%2520%257C%25204-89362-326-3%2520%257C%25204-893623-26-3%2520%257C%25204-8936232-6-3" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51g9K9r7quL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Slack入門 [ChatOpsによるチーム開発の効率化]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">松下 雅和,小島 泰洋,長瀬 敦史,坂本 卓巳 技術評論社 2016-06-28    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774182389" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01HI2TD28%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F14263497%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>           <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418238-4%2520%257C%25204-774-18238-4%2520%257C%25204-7741-8238-4%2520%257C%25204-77418-238-4%2520%257C%25204-774182-38-4%2520%257C%25204-7741823-8-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
必要なものがそろっており、JSON で 簡単にアクセスできるので助かります.
あとは、このデータを使わせてもらって、必要な時に必要な情報を流せるボットを作るだけですね！
