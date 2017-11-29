---
title: Windows の プロキシ を 自動設定するタスクを作成
date: 2017-11-25
comments: true
categories: 開発環境
tags:
- Windows
---

![](/images/misc/cyber.jpg "Cyber Space")

最近、利用するネットワーク環境が変えることが多くなり、プロキシ設定がめんどくさくなってきたのでスクリプトを作ってみました.

**作業環境**
- Windows 10 64bit


## プロキシ の 設定箇所
Windows で ブラウザなどを使う場合にプロキシを設定するのは [設定] - [ネットワークとインターネット] - [プロキシ] や、ブラウザのオプションから設定をします.
また、Node.js などは環境変数 `HTTP_PROXY` や `HTTPS_PROXY` を 使うので、環境変数を設定します.
ブラウザだけではないので、ネットワークを切り替えプロキシの有無を変えたりするとなると、結構手間がかかります...

Windows の プロキシ設定を切り替えるためのツールはありそうだったのですが、環境変数の変更までとなると なかなか見つからず、簡単なスクリプトで済ませることにしました.


## プロキシ の 自動設定スクリプト
今回は Windows 10 に 特別なものを入れないで動かしたいので、Windows PowerShell を 使うことにします.
ここでは `configure-proxy.ps1` というファイル名で作成しました.
```powershell
$PROXY_HOST="proxy.example.com"
$PROXY_PORT=8080
$LOCAL_ADDR="<local>"

$REG_KEY="HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings"

try {
    $ignore = [System.Net.Dns]::GetHostAddresses(${PROXY_HOST}) | ? { $_.AddressFamily -eq "InterNetwork" }

    Set-ItemProperty -path ${REG_KEY} ProxyEnable -value 1
    Set-ItemProperty -path ${REG_KEY} ProxyServer -value "${PROXY_HOST}:${PROXY_PORT}"
    Set-ItemProperty -path ${REG_KEY} ProxyOverride -value ${LOCAL_ADDR}
    Set-Item -path env:HTTP_PROXY -value "http://${PROXY_HOST}:${PROXY_PORT}"
    Set-Item -path env:HTTPS_PROXY -value ${env:HTTP_PROXY}
    [Environment]::SetEnvironmentVariable("HTTP_PROXY", ${env:HTTP_PROXY}, [EnvironmentVariableTarget]::User)
    [Environment]::SetEnvironmentVariable("HTTPS_PROXY", ${env:HTTP_PROXY}, [EnvironmentVariableTarget]::User)
} catch {
    Set-ItemProperty -path ${REG_KEY} ProxyEnable -value 0
    Remove-Item -path env:HTTP_PROXY
    Remove-Item -path env:HTTPS_PROXY
    [Environment]::SetEnvironmentVariable("HTTP_PROXY", "", [EnvironmentVariableTarget]::User)
    [Environment]::SetEnvironmentVariable("HTTPS_PROXY", "", [EnvironmentVariableTarget]::User)
}

$source=@"
[DllImport("wininet.dll")]
public static extern bool InternetSetOption(int hInternet, int dwOption, int lpBuffer, int dwBufferLength);
"@
$wininet = Add-Type -memberDefinition ${source} -passthru -name InternetSettings
${wininet}::InternetSetOption([IntPtr]::Zero, 95, [IntPtr]::Zero, 0)|out-null
${wininet}::InternetSetOption([IntPtr]::Zero, 37, [IntPtr]::Zero, 0)|out-null

exit 0
```

先頭の３行が設定部分になります.
`$PROXY_HOST` と `$PROXY_PORT` に 利用する プロキシ・サーバ の ホスト名 と ポート番号を記述します.
`$LOCAL_ADDR` は "ローカル アドレスにはプロキシ サーバーを使用しない" に チェックする場合は `<local>` を いれます. また、"次で始まるアドレスにはプロキシを使用しない" に 値を入れる場合は、同じく `$LOCAL_ADDR` に セミコロン区切りで設定します. 例えば下記のようにします.
`$LOCAL_ADDR="*.example.com;*.example.net;<local>"`

８行目の `GetHostAddresses(${PROXY_HOST})` で プロキシ・サーバ の IP アドレス を 引きます.
これは、現在のネットワークでプロキシ・サーバが引けるかを確認するためになります.
未使用変数 `$ignore` で 受けているのは、変数で受けないと DNS を 引いた情報が標準出力へ流れるのを防ぐためです.

IP アドレスが引けたら、後続の処理で以下を行いプロキシが利用できる設定をします.
コマンドラインからも呼べるように現在のセッションと永続化の両方の環境変数を設定しています.
- レジストリ に プロキシの利用を有効化し、プロキシ・サーバの情報、ローカルや除外アドレスの設定
- 現在のセッションにおける 環境変数 `HTTP_PROXY` と `HTTPS_PROXY` を 設定
- ユーザの環境変数 `HTTP_PROXY` と `HTTPS_PROXY` を 設定

IP アドレスが引けなかった場合は `catch` ブロックへ処理が飛びます.
DNS で ルックアップできなかったので プロキシ・サーバ が 利用できないものとして、プロキシの設定を解除します.
- レジストリ の プロキシ利用を無効化します　(設定した値は残し、使わないものとしています)
- 現在のセッションにおける 環境変数 `HTTP_PROXY` と `HTTPS_PROXY` を クリア
- ユーザの環境変数 `HTTP_PROXY` と `HTTPS_PROXY` を クリア

最後にレジストリに設定したプロキシ情報をブラウザに反映するための処理を行います.
こちらの部分につきましては、下記の記事を参考にさせていただきました. ありがとうございます！
- [WindowsでProxy利用のon/offを切り替える処理をPowerShellで書く - pslaboが試したことの記録](http://pslabo.hatenablog.com/entry/2017/09/07/071928)
- [IEのプロキシ除外設定有効のタイミングについて - TechNet フォーラム](https://social.technet.microsoft.com/Forums/ja-JP/aa0781d6-0d10-4c9b-bb7c-08ec925e1f05/ie?forum=internetexplorerja#8026a613-fb9a-46c9-ae7a-817733fa7193)


## タスク スケジューラ から PowerShell を 実行する ラッパー・スクリプト
上記コマンドを必要に応じて実行することでプロキシ設定をだいぶ軽減できますが、やはり自動設定してくれるようにしたいところです.
そのために タスク スケジューラ を 使うのですが、タスク スケジューラ から PowerShell を 実行すると、コマンド プロンプト らしきものが 一瞬見えてしまいます. それを防ぐためにラッパーのスクリプトを作成します.
こちらは [「windows」で「PowerShell」を「一切画面表示せず」に「タスクスケジューラに登録」する方法を再確認 - Qiita](https://qiita.com/Fushihara/items/1576f08825f39706174f) さん の 情報を参考させていただきました. ありがとうございます！

ここでは `ps-executer-for-task-scheduler.js` というファイル名で作成しました.
```javascript
shell = WScript.createObject("WScript.Shell");
ret = shell.Run("\"C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe\" -NoProfile -ExecutionPolicy RemoteSigned -File \"" + WScript.Arguments.Item(0) + "\"", 0, true);
WScript.Quit(ret);
```

`powershell.exe` を このスクリプトの引数で渡されたファイルで実行するというものになります.
`-NoProfile -ExecutionPolicy RemoteSigned` は スクリプトの実行ポリシーを一時的に下げるものになります. 今回は自分のスクリプトを実行することがわかっていますが、そうでない場合はよく確認してから実行する必要があります.
(と、これを見てくださっている方には **自分の** スクリプト **ではない** ですね. ご確認ください...)


## タスク スケジューラ に 設定
スタートメニュー で "タスク" で 検索すると、タスク スケジューラ が でてきます.
![](/images/windows/proxy-config-task/01.png)

タスク スケジューラ ウィンドウ 右 の 操作 から、[タスクの作成] を クリックします. ("基本タスクの作成"ではないこと、注意です)
![](/images/windows/proxy-config-task/02.png)

タスクの作成 の [全般]タブ で、タスクの名前を設定します. ここでは Configure Proxy としました.
![](/images/windows/proxy-config-task/03.png)

[条件]タブ を 表示し、[電源] に ついているチェックを外します.
チェックがついていると外出時などでネットワーク構成が変わっているときなど、肝心な時に動作してくれなくなるので大事です.
![](/images/windows/proxy-config-task/04.png)

[操作]タブ を 表示し、[新規] ボタンをクリックします.
![](/images/windows/proxy-config-task/05.png)

[プログラム/スクリプト] に 先ほど作成した ラッパー・スクリプト `ps-executer-for-task-scheduler.js` を 指定します.
[引数の追加] に PowerShell 本体 の `configure-proxy.ps1` を ダブルクォートで囲って入力します.
最後に、[開始] に スクリプトがあるディレクトリを入力します.
ここでは、`C:\Temp\windows-proxy-config-task` ディレクトリにスクリプトがにあるものとして設定しています.
![](/images/windows/proxy-config-task/06.png)

入力できたら [OK] ボタンをクリックして戻ります.
![](/images/windows/proxy-config-task/07.png)

[トリガー] タブ を 表示し、[新規] ボタンをクリックします. (ここから先は操作が重くなったり、処理に時間がかかったりすることがあります)
![](/images/windows/proxy-config-task/08.png)

[タスクの開始] で `イベント時` を 選択します.

設定を以下のように選択します.
- [ログ] は `Microsoft-Windows-NetworkProfile/Operational`
- [ソース] は `NetworkProfile`
- [イベント ID] は `10000`

詳細設定で、[遅延時間を指定する] を 念のため `1 秒間` に しておきます.
ネットワークが切り替わった後に発動するので問題ないはずですが、ちょっとだけ待ってから動かします.
![](/images/windows/proxy-config-task/09.png)

設定したら OK を クリックします.

※ 上記で設定した イベント ID の `10000` は ネットワークに接続された場合に通知される ID に なります. ネットワーク切断時 は `10001` です.　ソフトウェア で ネットワーク接続を切り替え、切断して元のネットワークに戻った場合にもタスクを実行したい、といったケースがある場合は、再度 [新規] を クリックして ID `10001` で 同じものを登録しておきます.
![](/images/windows/proxy-config-task/10.png)

タスク スケジューラ の ウィンドウに戻ります.
ウィンドウ 左 から [タスク スケジューラ ライブラリ] を クリックすると、今回登録したタスクが表示されます.
![](/images/windows/proxy-config-task/11.png)

ウィンドウ 中央 下段 から [履歴] タブを選択するとタスクのログが見れます.
まずは登録されたログが表示されています.
![](/images/windows/proxy-config-task/12.png)

ネットワークを切り替え、ログのテーブル表示部分から 右クリック [最新の情報に更新] を すると、登録したタスクが実行されていることが確認できます.
![](/images/windows/proxy-config-task/13.png)


## リポジトリ
とりあえず、スクリプトだけ GitHub [https://github.com/azriton/windows-proxy-config-task](https://github.com/azriton/windows-proxy-config-task) に アップしました.
README などは 追々書いていきたいと思います.


- - - -
<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774180017" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/51KA9o5eMkL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774180017" target="_blank" >[改訂新版]Windowsコマンドプロンプトポケットリファレンス</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">山近 慶一 技術評論社 2016-03-04    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2F4774180017" target="_blank" >Amazonで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB01D2PZY82%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkrakuten" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -50px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=862013&p_id=56&pc_id=56&pl_id=637&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fbooks.rakuten.co.jp%2Frb%2F13796297%2F" target="_blank" >楽天ブックスで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=862013&p_id=56&pc_id=56&pl_id=637" width="1" height="1" style="border:none;"></div>          <div class="shoplinkseven" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 -100px no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860693&p_id=932&pc_id=1188&pl_id=12456&s_v=b5Rz2P0601xu&url=http%3A%2F%2F7net.omni7.jp%2Fsearch%2F%3FsearchKeywordFlg%3D1%26keyword%3D4-77-418001-4%2520%257C%25204-774-18001-4%2520%257C%25204-7741-8001-4%2520%257C%25204-77418-001-4%2520%257C%25204-774180-01-4%2520%257C%25204-7741800-1-4" target="_blank" >7netで調べる<img src="//i.moshimo.com/af/i/impression?a_id=860693&p_id=932&pc_id=1188&pl_id=12456" width="1" height="1" style="border:none;"></a></div>                          </div></div><div class="booklink-footer" style="clear: left"></div></div>

<div class="booklink-box" style="text-align:left;padding-bottom:20px;font-size:small;/zoom: 1;overflow: hidden;"><div class="booklink-image" style="float:left;margin:0 15px 10px 0;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB017LJOKTE" target="_blank" ><img src="https://images-fe.ssl-images-amazon.com/images/I/516asannWdL._SL160_.jpg" style="border: none;" /></a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="booklink-info" style="line-height:120%;/zoom: 1;overflow: hidden;"><div class="booklink-name" style="margin-bottom:10px;line-height:120%"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB017LJOKTE" target="_blank" >うごかして学ぶWindows PowerShell[Kindle版]</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"><div class="booklink-powered-date" style="font-size:8pt;margin-top:5px;font-family:verdana;line-height:120%">posted with <a href="https://yomereba.com" rel="nofollow" target="_blank">ヨメレバ</a></div></div><div class="booklink-detail" style="margin-bottom:5px;">柏原基規  2015-11-04    </div><div class="booklink-link2" style="margin-top:10px;"><div class="shoplinkkindle" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//af.moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fexec%2Fobidos%2FASIN%2FB017LJOKTE%2F" target="_blank" >Kindleで調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div><div class="shoplinkamazon" style="margin-right:5px;background: url('//img.yomereba.com/yl.gif') 0 0 no-repeat;padding: 2px 0 2px 18px;white-space: nowrap;"><a href="//moshimo.com/af/c/click?a_id=860699&p_id=170&pc_id=185&pl_id=4062&s_v=b5Rz2P0601xu&url=http%3A%2F%2Fwww.amazon.co.jp%2Fgp%2Fsearch%3Fkeywords%3D%2582%25A4%2582%25B2%2582%25A9%2582%25B5%2582%25C4%258Aw%2582%25D4Windows%2520PowerShell%26__mk_ja_JP%3D%2583J%2583%255E%2583J%2583i%26url%3Dsearch-alias%253Dstripbooks" target="_blank" >Amazon[書籍版]で調べる</a><img src="//i.moshimo.com/af/i/impression?a_id=860699&p_id=170&pc_id=185&pl_id=4062" width="1" height="1" style="border:none;"></div>                                               </div></div><div class="booklink-footer" style="clear: left"></div></div>



- - - -
これでネットワークを切り替えた場合に、プロキシ設定が切り替わるようになりました！
ネットワークが切り替わったというイベントと、１秒の遅延設定をしているので、切り替わったと思う瞬間より少し遅れがある場合もありますが、十分使える範囲かと思います.
