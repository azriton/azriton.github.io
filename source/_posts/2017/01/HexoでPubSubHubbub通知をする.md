---
title: Hexo で PubSubHubbub 通知をする
date: 2017-01-06
comments: true
categories: ブログ
tags:
- Hexo
- GitHub
---

![](/assets/hexo/hexo-3.2.png "Hexo")

[Hexo の フィード出力を設定](/2016/11/07/Hexoのフィードとサイトマップを設定/)した際に、設定項目で気になったものがありました. "hub - URL of the PubSubHubbub hubs (Leave it empty if you don't use it) -- [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)" です.

そもそも PubSubHubbub 自体を知らなかったのですが、"パブサブハバブ -- [Wikipedia](https://ja.wikipedia.org/wiki/PubSubHubbub)" と 読むそうで、データの変更(ここでは、ブログの更新情報) を リアルタイムに通知するためのプロトコルなのだそうです.

この仕組みを使うことで、Google などの検索エンジンにブログの更新を効率よく通知することができ、インデックス化を早めることができるとのことで、早速導入したいと思います.

**作業環境**
- Windows 7
- Hexo 3.2
- hexo-generator-feed 1.2.0
- GitHub (GitHub Pages / Webhook)


## PubSubHubbub とは？
PubSubHubbub は、分散型 の パブリッシュ(発行)/サブスクライブ(購読) コミュニケーション を 行うためのオープンなプロトコルで、仕様は [https://github.com/pubsubhubbub/PubSubHubbub](https://github.com/pubsubhubbub/PubSubHubbub) で 公開されています. 2017年1月現在のバージョン は [0.4](http://pubsubhubbub.github.io/PubSubHubbub/pubsubhubbub-core-0.4.html) です.

ざっくり言うと、情報の提供者と利用者の間にハブを置くことで、それぞれが分離した作業ができるようにし、またプッシュ型にすることで情報の利用者が提供者のサーバへ定期的なポーリングせず不要な負荷を下げることができるようになります.
利用例として[気象庁の電文公開](http://xml.kishou.go.jp/open_trial/guidance.html)があります.
![](/assets/hexo/pubsubhubbub/01.png)

今回は、この仕組みを使い [Google PubSubHubbub Hub](http://pubsubhubbub.appspot.com/) へ ブログの更新情報をプッシュし、Google の クローラー へ ブログの更新に関する情報を購読してもらいます. これによって Google の クローラー が 定期的にブログの更新を確認しに来てくれていたのを、こちらからクローラーへ来てもらうことができるようになります.

これまでは Google の クローラーが来てくれるのを ただ待っていただけですが、PubSubHubbub を 使うことでクローラーを呼び込むことができ、検索エンジンのインデックス化を格段に早めることができるようになります.


## Hexo に PubSubHubbub を 設定
PubSubHubbub は RSS や Atom フィード で 情報を提供します. hexo-generator-feed が  PubSubHubbub の 情報生成に対応しているので、まずは hexo-generator-feed の 設定を行います.

`config.yml` の hexo-generator-feed に 関する設定に `hub: http://pubsubhubbub.appspot.com` を 追加します.
```yaml:config.yml
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub: http://pubsubhubbub.appspot.com
```

`hexo generate` で サイトを再生成してローカルサーバ [http://localhost:4000/atom.xml](http://localhost:4000/atom.xml) でフィードを確認してみると、`<link href="http://pubsubhubbub.appspot.com" rel="hub"/>` が 増えているのが分かります.
![](/assets/hexo/pubsubhubbub/02.png)


## GitHub の Webhook で PubSubHubbub 通知設定
hexo-generator-feed の 設定はフィードの出力設定で、PubSubHubbub の ハブ (Google PubSubHubbub Hub) への通知は別途行う必要があります.
GitHub Pages で ウェブサイトを公開しているので、今回は GitHub の Webhook を 使って更新時にハブへ通知するようにしたいと思います.

GitHub リポジトリ の Setting ページ から、Webhooks を 表示し、右側の [Add webhook] ボタンをクリックします.
![](/assets/hexo/pubsubhubbub/03.png)

以下の情報を入力し、[Add webhook] ボタンをクリックします.

| 設定項目 | 設定内容 |
|:---------|:---------|
| Payload URL | [PubSubHubbub 通知 URL(※)] |
| Content type | `application/x-www-form-urlencoded` を 選択 |
| Secret | [空欄] |
| Which events... | `Let me select individual events.` → `Page build`  に チェック |

[PubSubHubbub 通知 URL] は `https://pubsubhubbub.appspot.com/publish?hub.mode=publish&hub.url=https://[username].github.io/atom.xml` で、[username] を 自分のユーザ名に置き換える、もしくは `hub.url=` 以降に自分のサイトのフィード の URL にします.
心配な場合は [https://pubsubhubbub.appspot.com/publish](https://pubsubhubbub.appspot.com/publish) で 正しいか検証することができます.

また [Which events...] は `Page build` だけにし、GitHub Pages の ページがビルドされた時だけ通知するようにします. Push だと、ドラフトの記事を GitHub に 上げた際にも通知されてしまい、不要な通知がハブへ行ってしまいます.
![](/assets/hexo/pubsubhubbub/04.png)

Webhook が 追加されました. タイミングによっては通知のテストが行われておらずチェックがついてません. URL 部分をクリックし設定から通知テストを確認するかリロードして確認します.
![](/assets/hexo/pubsubhubbub/05.png)

通知テストの結果は Wehbook の 設定画面の一番下にあり、Response が `204` に なっていれば成功です.
実動作の確認としては CircleCI から再ビルドするか、`hexo deploy` で サイトを更新します. 正しくデプロイできると、この画面 の Recent Deliveries が増えます.
![](/assets/hexo/pubsubhubbub/06.png)



- - - -
ブログ に PubSubHubbub の 通知を追加できました. これにより、より早く記事が検索できるようになるといいですね. また読んでいただけるような、しっかりした記事をかけるようにしていきたいと思います. どうぞ 今後ともよろしくお願いいたします.
