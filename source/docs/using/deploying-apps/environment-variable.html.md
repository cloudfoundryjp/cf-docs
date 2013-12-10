---
title: Cloud Foundryの環境変数
---
Cloud Foundryは環境変数を使ってデプロイされたアプリケーションと環境についての情報のやりとりします。このページではDEAとビルドパックがアプリケーションのために設定する環境変数について説明します。

アプリケーション固有の環境変数の設定の方法については[アプリケーションのマニフェスト](/docs/using/deploying-apps/manifest.html)ページの[マニフェストでの環境変数の設定](/docs/using/deploying-apps/manifest.html#var)をご覧ください。

## <a id='view'></a>環境変数を参照する ##
この節ではCloud Foundryの環境変数を参照する方法について説明します。

### <a id='cli'></a>CLIで環境変数を参照する ###

cfコマンドは環境変数の値を返す機能を二通り提供します。詳しくは[cfコマンド・ライン・インターフェース](/docs/using/managing-apps/cf/index.html)ページの [ログ](/docs/using/managing-apps/cf/index.html#logs)と[ファイル](/docs/using/managing-apps/cf/index.html#files)をご覧ください。

<pre class="terminal">
$ cf files APP_NAME_HERE logs/env.log
</pre>

### <a id='app'></a>プログラムから環境変数へアクセスする ###

ここではプログラムから環境変数へアクセスするを説明します。以下の例では`VCAP_SERVICES`環境変数を使います。

### Java

```
System.getenv("VCAP_SERVICES");
```

### Ruby

```
ENV['VCAP_SERVICES']
```

### Node.js

```
process.env.VCAP_SERVICES
```

## <a id='dea-set'></a>DEAが設定する変数 ##

ここでは、アプリケーションを公開する時にDEAが設定する環境変数について説明します。

### <a id='HOME'></a>HOME ###
デプロイされたアプリケーションにとってのrootフォルダです。

`HOME=/home/vcap/app`

### <a id='HOME'></a>MEMORY_LIMIT ###
アプリケーションの各インスタンスが使用できるメモリの量の最大値です。この値はアプリケーションのマニフェストあるいはプッシュする時のオプションの指定で決まります。

インスタンスがこの制限を越えると再起動されます。再起動がくりかえされると、停止されます。

`MEMORY_LIMIT=512m`

### <a id='PORT'></a>PORT ###
DEAがアプリケーションと通信するのに使うポートです。DEAはアプリケーションを公開する時にこのポートを割当てます。このため、ポートを使うコードは`PORT`を使って参照する必要があります。

`PORT=61857`

### <a id='PWD'></a>PWD ###
アプリケーションを処理するビルドパックが実行されているワーキング・ディレクトリを示す。

`PWD=/home/vcap`

### <a id='TMPDIR'></a>TMPDIR###
一時的なファイルや公開するファイルを置くディレクトリ。

`TMPDIR=/home/vcap/tmp`

### <a id='USER'></a>USER###
DEAを実行しているユーザ・アカウント。

`USER=vcap`

### <a id='VCAP\_APP\_HOST'></a>VCAP\_APP\_HOST

DEAは実行されているホストのIPアドレス。

`VCAP_APP_HOST=0.0.0.0`

### <a id='VCAP_APPLICATION'></a>VCAP_APPLICATION ###

この変数はデプロイされたアプリケーションに関する有用な情報を保持しています。結果はJSON形式で返されます。以下の表は返される属性の一覧です。


|属性|説明 |
| --------- | --------- | 
|application_users, users | |
|instance_id  |アプリケーションを識別するGUID|
|instance_index |インスタンスを特定する番号(DEAごとに割り振られる) | 
|application_version, version |アプリケーションのヴァージョンを示すGUID。アプリケーションがプッシュされる度に値が更新される。 | 
|application_name, name |プッシュされた時に割り振られるアプリケーションの名前 | 
|application_uris |The URI(s) assigned to the application.   | 
|started_at, start |アプリケーションが最後に起動された時刻 | 
|started\_at\_timestamp |アプリケーションが最後に起動されたタイム・スタンプ | 
|host |アプリケーションのインスタンスのIPアドレス | 
|port |アプリケーションのインスタンスのポート | 
|limits  |インスタンスが使用できるメモリ、ディスク、ファイル数の上限。 メモリとディスクの上限はアプリケーションがデプロイされた時コマンド・ラインまたはマニフェストによって決まる。使用できるファイル数は管理者が設定する。 | 
|state_timestamp |アプリケーションが現在の状態になった時点を表すタイム・スタンプ| 

~~~

VCAP_APPLICATION={"instance_id":"451f045fd16427bb99c895a2649b7b2a","instance_index":0,"host":"0.0.0.0","port":61857,"started_at":"2013-08-12 00:05:29 +0000","started_at_timestamp":1376265929,"start":"2013-08-12 00:05:29 +0000","state_timestamp":1376265929,"limits":{"mem":512,"disk":1024,"fds":16384},"application_version":"c1063c1c-40b9-434e-a797-db240b587d32","application_name":"styx-james","application_uris":["styx-james.a1-app.cf-app.com"],"version":"c1063c1c-40b9-434e-a797-db240b587d32","name":"styx-james","uris":["styx-james.a1-app.cf-app.com"],"users":null}`

~~~

### <a id='VCAP_APP_PORT'></a>VCAP\_APP\_PORT ###

上で説明した [ポート](#PORT)と同様です。

### <a id='VCAP_CONSOLE_IP'></a>VCAP\_CONSOLE\_IP ###

V2では未実装です。

RailsコンソールにアクセスできるIPアドレス。

`VCAP_CONSOLE_IP=0.0.0.0`

### <a id='VCAP\_CONSOLE\_PORT'></a>VCAP\_CONSOLE\_PORT ###
V2では未実装です。

Railsコンソールにアクセスできるポート(IPアドレスは`VCAP_CONSOLE_IP`で示されます)。

`VCAP_CONSOLE_PORT=61858` 

### <a id='VCAP_SERVICES'></a>VCAP\_SERVICES ###

ほとんどのサービス・タイプについて、Cloud Foundryは接続情報をサービスがアプリケーションへバインドされた時に環境変数`VCAP_SERVICES`へ追加します。

結果はJSON形式で返され、一つ以上のインスタンスがアプリケーションへバインドされているサービス・タイプのオブジェクトが格納されています。サービス・タイプのオブジェクトはアプリケーションへバインドされているサービスのインスタンスのための子オブジェクトを含みます。サービスのインスタンスの属性はタイプにより異ります。バインドされたサービスの属性を以下の表に示します。すべての属性がすべてのタイプに適用されるわけではないことにご注意ください。


|属性|説明 |
| --------- | --------- | 
|service name-version|サービス・タイプを表すサービスの名前とヴァージョン。(ヴァージョンが指定されていない場合、"n/a") ダッシュで区切られる。例: "cleardb-n/a" |
|name|サービスのインスタンスが作られた時に指定された名前 |
|label|service name-versionと同じ値を取る |
|plan|サービスが作られた時に選択されたプロヴァイダーのプラン |
|name|サービス・プロヴァイダーのサーバーで動作しているデータベースの名前。cleardbのインスタンスのための"credentials"オブジェクトに含まれる  |
|host |サービスへ接続するためのホスト; cleardb, rediscloud, or sendgridのインスタンスのための"credentials"オブジェクトに含まれる  |
|port |サービスへ接続するためのポート; cleardb, rediscloudのインスタンスのための"credentials"オブジェクトに含まれる |
|username |サービスへ接続するためのユーザ名; cleardb, sendgridのインスタンスのための"credentials"オブジェクトに含まれる |
|password |サービスへ接続するためのパスワード; cleardb, cloudamp, sendgridのインスタンスのための"credentials"オブジェクトに含まれる |
|uri  |サービスへ接続するためのURI; cleardb, rediscloud, elephantsql, mongolabのインスタンスのための"credentials"オブジェクトに含まれる |
|jdbcUrl|データベース接続のためのJDBC; cleardbのインスタンスのための"credentials"オブジェクトに含まれる |


以下の例では、Cloud Foundry Marketplaceで利用可能な各サービス・タイプのインスタンスにバインドされたVCAP_SERVICE変数を解析した内容を示します。

~~~
VCAP_SERVICES=
{
  cleardb-n/a: [
    {
      name: "cleardb-1",
      label: "cleardb-n/a",
      plan: "spark",
      credentials: {
        name: "ad_c6f4446532610ab",
        hostname: "us-cdbr-east-03.cleardb.com",
        port: "3306",
        username: "b5d435f40dd2b2",
        password: "ebfc00ac",
        uri: "mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab",
        jdbcUrl: "jdbc:mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab"
      }
    }
  ],
  cloudamqp-n/a: [
    {
      name: "cloudamqp-6",
      label: "cloudamqp-n/a",
      plan: "lemur",
      credentials: {
        uri: "amqp://ksvyjmiv:IwN6dCdZmeQD4O0ZPKpu1YOaLx1he8wo@lemur.cloudamqp.com/ksvyjmiv"
      }
    }
  ],
  rediscloud-n/a: [
    {
      name: "rediscloud-1",
      label: "rediscloud-n/a",
      plan: "20mb",
      credentials: {
        port: "17546",
        hostname: "pub-redis-17546.MatanCluster.ec2.garantiadata.com",
        password: "1M5zd3QfWi9nUyya"
      }
    },
  ],
{
  elephantsql-dev-n/a: [
  {
    name: "elephantsql-dev-c6c60",
    label: "elephantsql-dev-n/a",
    plan: "turtle",
    credentials: {
      uri: "postgres://seilbmbd:PHxTPJSbkcDakfK4cYwXHiIX9Q8p5Bxn@babar.elephantsql.com:5432/seilbmbd"
    }
  }
  ]
}


 mongolab-dev-n/a: [
  {
    name: "mongolab-dev-2cea8",
    label: "mongolab-dev-n/a",
    plan: "sandbox",
    credentials: {
      uri: "mongodb://cloudfoundry-test_2p6otl8c_841b7q4b_tmtlqeaa:eb5d00ac-2a4f-4beb-80ad-9da11cff5a70@ds027908.mongolab.com:27908/cloudfoundry-test_2p6otl8c_841b7q4b"
    }
  }
  ]
}
{
  "newrelic-n/a":[
    {
      "name":"newrelic-14e9d",
      "label":"newrelic-n/a",
      "plan":"standard",
      "credentials":
        {
          "licenseKey":"2865f6f3nsig8f813af7989fccb24a699cb22a4beb"
        }
    }
  ]
}
{
  sendgrid-n/a: [
    {
      name: "mysendgrid",
      label: "sendgrid-n/a",
      plan: "free",
      credentials: {
        hostname: "smtp.sendgrid.net",
        username: "QvsXMbJ3rK",
        password: "HCHMOYluTv"
      }
    }
  ]
}
~~~

## <a id='java-buildpack'></a>Javaビルドパックで設定される変数 ##

ここではアプリケーションの公開時にJavaビルドパックで設定される環境変数について説明します。

### <a id='JAVA_HOME'></a>JAVA_HOME ###

アプリケーションが実行されているコンテナでのJAVAの位置。
The location of JAVA on the container running the application.

`JAVA_HOME=/home/vcap/app/.jdk`

### <a id='JAVA_OPTS'></a>JAVA_OPTS ###

アプリケーション実行時のJavaのオプション。すべての値が、JVMの起動時に変更されることなく使用されます。Javaビルドパックの`/config/javaopts.yml`ファイルで設定できます。

`JAVA_OPTS=-Xmx512m -Xms512m -Dhttp.port=61857`

### <a id='JAVA_TOOL_OPTIONS'></a>JAVA\_TOOL\_OPTIONS ###

Liftフレームワークを使用するJavaアプリケーションの自動設定を有効にするのに必要なJavaオプションを設定する環境変数です。
 
`JAVA_TOOL_OPTIONS=-Drun.mode=production`

## <a id='ruby-buildpack'></a>Rubyビルドパックで設定される変数 ##

ここではアプリケーションの公開時にRubyビルドパックで設定される環境変数について説明します。

### <a id='BUNDLE_BIN_PATH'></a>BUNDLE\_BIN\_PATH ###

Bundlerがバイナリをインストールした場所。

`BUNDLE_BIN_PATH:/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/bin/bundle`

### <a id='BUNDLE_GEMFILE'></a>BUNDLE_GEMFILE ###

アプリケーションのgemfileへのパス。
 
`BUNDLE_GEMFILE:/home/vcap/app/Gemfile`

### <a id='BUNDLE_WITHOUT'></a>BUNDLE_WITHOUT ###

`BUNDLE_WITHOUT`環境変数は、excluded groupsに含まれるgemのインストールをCloud Foundryにスキップさせます。`BUNDLE_WITHOUT`は特にRailsアプリケーションにとって有用です。なぜなら、productionモードで実行する時には不要なassets”や“development”モード用のgemが含まれているからです。

この変数の使い方については http://blog.cloudfoundry.com/2012/10/02/polishing-cloud-foundrys-ruby-gem-support をご覧ください。

`BUNDLE_WITHOUT=assets`

### <a id='DATABASE_URL'></a>DATABASE_URL ###

Rubyビルドパックはバインドされたサービスが既知のデータベースか確かめます。既知のリレーショナル・データベースがあれば、ビルドパックはリストの先頭にあるものをDATABASE_URL環境変数へ設定します。

あなたのアプリケーションがDATABASE_URLを必要とし、Cloud Foundryが設定しない場合、手動で設定することもできます。

`$ cf set-env myapp DATABASE_URL mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab`

### <a id='GEM_HOME'></a>GEM_HOME ###

gemがインストールされている場所。

`GEM_HOME:/home/vcap/app/vendor/bundle/ruby/1.9.1`

### <a id='GEM_PATH'></a>GEM_PATH ###

gemを見つけることができる場所。

`GEM_PATH=/home/vcap/app/vendor/bundle/ruby/1.9.1:`

### <a id='RACK_ENV'></a>RACK_ENV ###
Rackデプロイ環境で設定される変数。development, deployment, または noneになる。
 
`RACK_ENV=production`

### <a id='RAILS_ENV'></a>RAILS_ENV ###
Railsデプロイ環境で設定される変数。development, test, または productionになる。アプリケーションがどのように実行されるかを決める環境特有の設定ファイルを制御する。

`RAILS_ENV=production`

### <a id='RUBYOPT'></a>RUBYOPT ###
このRuby環境変数は、Rubyインタープリタに渡されるコマンド・ライン・オプションを保持する。

`RUBYOPT: -I/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/lib -rbundler/setup`

## <a id='node-buildpack'></a>Nodeビルドパックで設定される変数 ##

### <a id='BUILD_DIR'></a>BUILD_DIR ###
Node.jsアプリケーションが実行される度にNode.jsがコピーされるディレクトリ。

### <a id='CACHE_DIR'></a>CACHE_DIR ###

Node.jsがキャッシュとして使うディレクトリ。

### <a id='PATH'></a>PATH ###

Node.jsが使うシステムのパス。

`PATH=/home/vcap/app/bin:/home/vcap/app/node_modules/.bin:/bin:/usr/bin`



