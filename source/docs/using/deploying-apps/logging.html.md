---
title: ストリーミング・ログ
---

_このページは[beta of the go cf CLI](http://blog.cloudfoundry.com/2013/11/09/announcing-cloud-foundry-cf-v6/)またはgcfを使っていることが前提になっています。

### 特徴

Cloud Foundryのログの機能は以下の通りです:

1. アプリケーションのログのtail(最新部の表示)
1. アプリケーションのログの表示
1. syslogを用いてサード・パーティのログのアーカイブや分析のサービスへアプリケーションのログを継続的に渡す

### アプリケーションのログの最新の部分を参照

<pre class="terminal">
gcf logs APP_NAME [--recent]
</pre>

`gcf logs APPNAME`でアプリケーションのSTDOUTとSTDERRをリアル・タイムで見ることができます。
たとえば:

<pre class="terminal">
$ gcf logs myapp
2013-12-03T20:52:36.64-0800 [App/0]   ERR 108.216.154.88, 10.10.66.252 - - [04/Dec/2013 04:52:36] "GET / HTTP/1.1" 200 1358 0.0020
2013-12-03T20:52:36.66-0800 [App/0]   ERR 108.216.154.88, 10.10.66.252 - - [04/Dec/2013 04:52:36] "GET / HTTP/1.1" 200 1358 0.0288
2013-12-03T20:52:36.66-0800 [RTR]     OUT env-test.cfapps.io - [04/12/2013:04:52:36 +0000] "GET / HTTP/1.1" 200 1358 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.57 Safari/537.36" 10.10.66.252:48779 response_time:0.067069415 app_id:c66ecb53-5aff-4f7d-b7a4-b0143c4b6ade
...
</pre>

終了するにはCtrl-C (^C)を押してください。

### アプリケーションの最近の部分をダンプ

アプリケーションがクラッシュした場合、最近のログは有用です。
そのためには、`gcf logsl`に`--recent`オプションを付けてください。

<pre class="terminal">
gcf logs APP_NAME --recent
</pre>

### アプリケーションのログの出力

CFロギング・システムはsyslogフォーマットとオクテット・エンコーディングをサポートしています。syslogフォーマットについては[RFC 5424](http://tools.ietf.org/html/rfc5424)、オクテット・エンコーディングについては[RFC 6587](http://tools.ietf.org/html/rfc6587)に記述されています。
オプションとして、syslog TLS([RFC 5425](http://tools.ietf.org/html/rfc5425))を使用することもできます。

サード・パーティのsyslog出力システムへアプリケーションのログを渡すには、syslog出力用URLをアプリケーションへバインドする必要があります。

そのためには、サード・パーティのログ・サービスをアプリケーションへ追加するか、コマンド・ラインを使ってユーザ定義のサービスとしてアプリケーションへバインドします。

ユーザ定義のsyslogサービスを追加するには:

<pre class="terminal">
gcf create-user-provided-service SERVICE_NAME -l SYSLOG-DRAIN-URL
</pre>

SYSLOG-DRAIN-URLはscheme://host:port'(schemeは'syslog'または'syslog-tls')というフォーマットである必要があります。例:

    syslog scheme:     'syslog://my.unencrypted.syslogservice.example.com:1234'
    syslog tls scheme: 'syslog-tls://my.encrypted.syslogservice.example.com:5467

カスタム・サービスを作った後、アプリケーションへバインドする必要があります。
例:

<pre class="terminal">
gcf bind-service APP_NAME SERVICE_NAME
</pre>

syslogサービスへログを出力したいすべてのアプリケーションについてこの手順をくりかえすことができます。
新しいサービスを有効にするため、アプリケーションを再起動する必要があります。

### 注意

Cloud Foundryによるログの収集と保存はベスト・エフォートです。
クライアントが処理しきれないでログのバッファがあふれた場合、システムはログのメッセージの一部を破棄します。
アプリケーションのログは、CLIなどのクライアントが最新部を追いかけるかsyslog出力がついていけるなら、利用可能です。
ログの機能はアプリケーションのSTDOUT、STDERR、および関連のメッセージのみを表示
します。
アプリケーションがログを特定のファイルではなくSTDOUTやSTDERRに書くよう設定する必要があります。
STDOUTとSTDERR以外のログはサポートの範囲外です。

ログはタイム・スタンプ付で出力されます。
このタイム・スタンプはロギング・サービスがログを受けとる時点で付加されます。
ログがタイム・スタンプを含んでいた場合、こちらのタイム・スタンプはロギング・システムに処理されず、メッセージの一部として扱われます。

これらを避けるため、アプリケーションがSTDOUTやSTDERRをバッファリングしないことを確認してください。

sinatraについて、以下の行を設定ファイルに入れておいてください:

    $stderr.sync = true
    $stdout.sync = true

Log4J ConsoleAppenderを使っているなら、バッファリングは行なわれません。
 [immediateFlush](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/WriterAppender.html#immediateFlush)というオプションがあり、デフォルトはtrueになっています。
falseに設定すると、バッファリングがありえます。

Logbackの[ConsoleAppender](http://logback.qos.ch/manual/appenders.html#ConsoleAppender)はデフォルトでバッファリングを行ないます。(OutputStreamWriterを使います)
