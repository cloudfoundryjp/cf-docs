---
title: ストリーミング・ログ
---

_このページは[beta of the go cf CLI](http://blog.cloudfoundry.com/2013/11/09/announcing-cloud-foundry-cf-v6/)またはgcfを使っていることが前提になっています。

### 特徴

Cloud Foundryのログの機能は以下の通りです:

1. アプリケーションのログのtail(最新部の表示)
1. アプリケーションのログの表示
1. syslogを用いてサード・パーティのログのアーカイブや分析のサービスへアプリケーションのログを継続的に渡す

### 使い方

<pre class="terminal">
gcf logs APP_NAME [--recent]
</pre>

`gcf logs APPNAME`でアプリケーションのSTDOUTとSTDERRをリアル・タイムで見ることができます。たとえば:

<pre class="terminal">
$ gcf logs myapp
2013-12-03T20:52:36.64-0800 [App/0]   ERR 108.216.154.88, 10.10.66.252 - - [04/Dec/2013 04:52:36] "GET / HTTP/1.1" 200 1358 0.0020
2013-12-03T20:52:36.66-0800 [App/0]   ERR 108.216.154.88, 10.10.66.252 - - [04/Dec/2013 04:52:36] "GET / HTTP/1.1" 200 1358 0.0288
2013-12-03T20:52:36.66-0800 [RTR]     OUT env-test.cfapps.io - [04/12/2013:04:52:36 +0000] "GET / HTTP/1.1" 200 1358 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.57 Safari/537.36" 10.10.66.252:48779 response_time:0.067069415 app_id:c66ecb53-5aff-4f7d-b7a4-b0143c4b6ade
...
</pre>

終了するにはCtrl-C (^C)を押してください。

アプリケーションがクラッシュした場合、最近のログが見られると便利です。
そのためには--recentオプションを付けてください。たとえば:

<pre class="terminal">
gcf logs myapp --recent
</pre>

### 注意
ログの機能はアプリケーションのSTDOUT、STDERR、および関連のメッセージのみを表示します。アプリケーションがログを特定のファイルではなくSTDOUTやSTDERRに書くよう設定する必要があります。STDOUTとSTDERR以外のログはサポートの範囲外です。

Cloud Foundryのログの収集と保存はベスト・エフォートです。クライアントが保持できるよりも多くのログがある場合、一部が失われることがあります。CLIのtailやsyslogが扱える範囲で、アプリケーションのログは利用可能です。

