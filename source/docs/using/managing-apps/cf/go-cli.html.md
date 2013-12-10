---
title: cf v6の紹介
---

## <a id='beta'></a>ベータのお知らせ ##

cf v6 βはCloud Foundryのコマンド・ライン・インタフェースを一から書き直したものです。v6はGoで記述され、以前のRubyヴァージョンより高速になっています。

cf v6はβテスト中です。以下の制限があります:

* アプリケーションのマニフェストはまだサポートされていません。デプロイのオプションはコマンド・ラインで指定する必要があります。これらは`manifest.yml`に保存されません。
* `gcf push`を使ったステージングとビルドパックの出力は現時点ではLoggregator向けに最適化されています。ログ・メッセージは不完全な場合があります。当面の対処は、`gcf logs --recent`を使うことです。
* いくつかのコマンドについて、最初の50回の結果だけが表示されます。

βの期間中はcfではなくgcfという名前になります。cf v6のコマンドは"gcf"ではじまります。すぐにgcfへ全面的に移行する場合は、エイリアスを使ってください。

cf v6はv5を置き換える予定です。v6のβ期間中は、1台のマシンでv5とv6は共存できます。"gcf"ではじめるコマンドを呼び出せばv6が使えます。v6が一般的になれば、gcfはcfへリネームされます。cf v6はその時点で"gcf"ではなく"cf"ではじまるように更新されます。

cf v6は https://github.com/cloudfoundry/cli/releases/tag/v6.0.0-betaからダウンロードできます。インストールの手順については https://github.com/cloudfoundry/cli/blob/master/INSTALL.mdをご覧ください。



## <a id='new'></a>新しい仕様 ##

## <a id='login'></a>login ##

cf v6の`login`コマンドは機能面で拡張されています。ユーザ名とパスワードに加えて、ターゲットAPIのエンドポイント、オーナガイゼーション、スペースを指定することもできます。オプションとして指定しなければ、cfは値を入力するよううながします。

Usage:

<pre class="terminal">
gcf login [-a API_URL] [-u USERNAME] [-p PASSWORD] [-o ORG] [-s SPACE]
</pre>

オーガナイゼーションとスペースが一つしかなければ、ログインすると自動的にそれらが使われます。指定する必要はありません。

ログインに成功すると、cf v6はあなたのAPIエンドポイント、オーガナイゼーション、スペースを`~/.cf`ディレクトリの`config.json`ファイルに保存します。これらを変更した場合、`config.json`も変更されます。

cf v6はcf loginコマンドで指定された値を使おうとします。ログインとターゲットの指定を含むスクリプトでは、非対話型の`gcf api`, `gcf auth`, `gcf target`を使ってください。

## <a id='push'></a>push ##

`push`コマンドのオプションは変更されました。オプションの多くは短かいもしくは一文字へ変更されました。二つの新しいオプションが追加されました。以下のリストに記載してあります。

Usage:

<pre class="terminal">
gcf push APP [-b URL] [-c COMMAND] [-d DOMAIN] [-i NUM_INSTANCES] [-m MEMORY] [-n HOST] [-p PATH] [-s STACK] [--no-hostname] [--no-route] [--no-start]`
</pre>

`APP`だけは指定する必要があります。オプショナルなものは以下の通りです:

* `-b` --- カスタム・ビルドパックのURL 例: https://github.com/heroku/heroku-buildpack-play.git
* `-c` --- アプリケーションの起動コマンド
* `-d` --- ドメイン 例: example.com
* `-i` --- 起動したいインスタンスの数
* `-m` --- メモリの上限 例: 256, 1G, 1024M, and so on.
* `-n` --- ホスト名 例: `my-subdomain`.
* `-p` --- アプリケーションが存在するディレクトリのパス
* `-s` --- スタック
* `--no-hostname` --- このアプリケーションへ割当てるルート・ドメイン (新機能)
* `--no-route` --- このアプリケーションへルートを割当てない (新機能)
* `--no-start` --- プッシュした後、アプリケーションを起動しない


## <a id='user-provided'></a> ユーザが提供するサービス ##

ユーザが提供するサービスの作成や更新を行うcf v6の新しいコマンド。説明は以下の節を参照。

ユーザが提供するサービスの作成や更新を行う時、`-l SYSLOG_DRAIN_URL`オプションを使ってRFC 5424の形式の出力をRFC 6587に従ったログ管理ソフトへ送れることに注意してください。

ユーザが提供するサービスが作成されれば、`gcf bind-service`を使ってアプリケーションへバインドすることができます。また、`gcf unbind-service`コマンドでアンバインド、 `gcf rename-service`でリネーム、`gcf delete-service`で削除できます。

### <a id='user-cups'></a>gcf create-user-provided-service, gcf cups ###

対話的な機能:

<pre class="terminal">
gcf create-user-provided-service SERVICE_INSTANCE -p "HOST, PORT, DATABASE, USERNAME, PASSWORD" -l SYSLOG-DRAIN-URL
</pre>

非対話的な機能:

<pre class="terminal">
gcf create-user-provided-service SERVICE_INSTANCE -p '{"username":"USERNAME","password":"PASSWORD"}' -l SYSLOG-DRAIN-URL
</pre>

### <a id='user-uups'></a>gcf update-user-provided-service`, gcf uups ###

`gcf update-user-provided-service`コマンドを使って、ユーザが提供するサービスの設定を変更できます。指定しない設定は変更されません。

対話的な機能:

<pre class="terminal">
gcf update-user-provided-service SERVICE_INSTANCE -p "HOST, PORT, DATABASE, USERNAME, PASSWORD" -l SYSLOG-DRAIN-URL
</pre>

非対話的な機能:

<pre class="terminal">
gcf update-user-provided-service SERVICE_INSTANCE -p '{"username":"USERNAME","password":"PASSWORD"}' -l SYSLOG-DRAIN-URL
</pre>


## <a id='user-provided'></a> ドメインとルート ##

ドメインとルートを管理するcf v6コマンドは以下の通りです:

* `gcf create-domain` ---  オーガナイゼーションの中にドメインを作ります。
* `gcf share-domain` --- すべてのオーナガイゼーションに対してドメインを共有します。
* `gcf map-domain` --- ドメインをスペースへ割当てます。
* `gcf unmap-domain` --- ドメインのスペースへの割当を解除します。
* `gcf delete-domain` --- ドメインを削除します。
* `gcf create-route` --- スペースの中にURLルートを作ります。
* `gcf map-route` --- アプリケーションへURLルートを追加します。
* `gcf unmap-route` --- アプリケーションのURLルートを削除します。
* `gcf delete-route` --- ルートを削除します。


ルートの中でドメインを使うには:

1. `gcf create-domain`でオーナイゼーション内にドメインを作成します。さもなければ、ドメインが既に存在しているものとします。
1. `gcf map-domain`でドメインをスペースへ割当てます。
1. `gcf map-route`でドメインをアプリケーションへ割当てます。スペース内でルートがユニークである限り、同じドメインを別のアプリケーションへ割当てることもできます。`-n HOSTNAME`オプションでユニークなホスト名を指定すれば、ルートはユニークになります。

## <a id='domains-routes'></a> スペースとオーガナイゼーションのロールの管理 ##


cf v6はユーザとロールを管理する新しいコマンドを提供しています。

* `gcf org-users` --- ロールごとにオーガナイゼーションに属するユーザの一覧を表示します。
* `gcf set-org-role` --- オーガナイゼーションのロールをユーザへ割当てます。"OrgManager", "BillingManager", "OrgAuditor"が使用できるロールです。
* `gcf unset-org-role` --- ユーザからオーガナイゼーションのロールを取りさります。
* `gcf space-users` --- ロールごとにスペースに属するユーザの一覧を表示します。
* `gcf set-space-role` --- スペースのロールをユーザへ割当てます。"SpaceManager", "SpaceDeveloper", "SpaceAuditor"が使用できるロールです。
* `gcf unset-space-role` --- ユーザからスペースのロールを取りさります。


## <a id='aliases'></a> New Aliases ##

cf v6はよく使われるコマンドに対して一文字の別名を提供しています。たとえば、`gcf p`で`gcf push`を実行でき、`gcf t`で`gcf target`を実行できます。下に示すように、コマンドに別名があれば、コマンド・ラインのヘルプで見ることができます。

## <a id='help'></a> コマンド・ラインのヘルプ ##

`gcf help`でコマンドの一覧と簡単な説明を見ることができます。詳しい説明がほしければ、コマンドに `-h`をつけて実行してください。以下に例を示します:

<pre class="terminal">
gcf push -h
NAME:
   push - Push a new app or sync changes to an existing app

ALIAS:
   p

USAGE:
   gcf push APP [-b URL] [-c COMMAND] [-d DOMAIN] [-i NUM_INSTANCES]
               [-m MEMORY] [-n HOST] [-p PATH] [-s STACK]
               [--no-hostname] [--no-route] [--no-start]

OPTIONS:
   -b ''		Custom buildpack URL (for example: https://github.com/heroku/heroku-buildpack-play.git)
   -c ''		Startup command
   -d ''		Domain (for example: example.com)
   -i '1'		Number of instances
   -m '128'		Memory limit (for example: 256, 1G, 1024M)
   -n ''		Hostname (for example: my-subdomain)
   -p ''		Path of app directory or zip file
   -s ''		Stack to use
   --no-hostname	Map the root domain to this app
   --no-route		Do not map a route to this app
   --no-start		Do not start an app after pushing
</pre>

注意:

* `gcf push APP`のように、ユーザからの入力は大文字で示しています。
* `gcf create-route SPACE DOMAIN [-n HOSTNAME]`のように、省略可能なオプションは大括弧で示しています。

