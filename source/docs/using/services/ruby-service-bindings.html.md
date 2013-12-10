---
title: Rubyのためのサービス接続設定
---

サービスのインスタンスを作ってバインドした後、アプリケーションをサービスへ接続するよう設定する必要があります。

## <a id='cf-app-utils'></a>cf-app-utilsを使ったVCAP_SERVICESへのアクセス ##

アプリケーションからVCAP_SERVICES内の利用者情報を名前やタグやラベルで検索するためのgemが提供されています。

## <a id='config-file'></a>設定ファイルでコネクションを定義する ##

データベースを使うRubyアプリケーションでは、データベースへの接続は`database.yml`ファイルで設定します。サービスをアプリケーションへバインドする際、接続情報を環境変数`VCAP_SERVICES`へ書き込まれます。`VCAP_SERVICES`にはJSONフォーマットでバインドされているサービスの一覧と利用者情報が書きこまれています。

Rubyでは、`VCAP_SERVICES`の内容に`ENV`でアクセスできます。 例:

~~~ruby

my_services = JSON.parse(ENV['VCAP_SERVICES'])

~~~

`VCAP_SERVICES` JSONから資格情報を得るには、ハッシュのキーを必要とします。
`cf services`でサービスの一覧とキーを見ることができます。

例:

<pre class="terminal">
  cf services
  Getting services in production... OK

  name           service           provider      version   plan     bound apps
  myservice      elephantsql       elephantsql   n/a       turtle   myapp

</pre>

ハッシュ・キーはサービス・プロヴァイダーとヴァージョンから作られます --- 上で挙げた例では、プロヴァイダーが"elephantsql"でヴァージョンが"n/a"で、結果としてハッシュ・キーは"elephantsql-n/a"になります。

資格情報を得るには以下のようにします:

~~~ruby
  db = JSON.parse(ENV['VCAP_SERVICES'])["elephantsql-n/a"]
  credentials = db.first["credentials"]
  host = credentials["host"]
  username = credentials["username"]
  password = credentials["password"]
  database = credentials["database"]
  port = credentials["port"]
~~~

Railsアプリケーションを設定するために、`database.yml`を変更することになります。VCAP_SERVICESからの資格情報を使って接続の設定をerbで記述すると以下のようにやります:

~~~

<%
  mydb = JSON.parse(ENV['VCAP_SERVICES'])["elephantsql-n/a"]
  credentials = mydb.first["credentials"]
%>

production:
  adapter: pg
  encoding: utf8
  reconnect: false
  pool: 5
  host: <%= credentials["host"] %>
  username: <%= credentials["username"] %>
  password: <%= credentials["password"] %>
  database: <%= credentials["database"] %>
  port: <%= credentials["port"] %>

~~~

Active Recordまたは他のORMを使わず、アダプタを直接記述する場合、コードは以下のようになります:

~~~ruby

mysqldb = JSON.parse(ENV['VCAP_SERVICES'])["cleardb-n/a"]
credentials = {
  :host => credentials["host"],
  :username => credentials["username"],
  :password => credentials["password"],
  :database => credentials["name"],
  :port => credentials["port"]
}

client = Mysql2::Client.new credentials

~~~

サービスのハッシュ・キーとデータベース・アダプタのシンタックスに応じて、コードは上の例から変わり得ます。


## <a id='migrate'></a>データベースのシードまたはマイグレート ##

データベースを使いはじめる前に、クリエイト、ポピュレート、マイグレートのどれかが必要です。詳しくは [Migrate a Database on Cloud Foundry](/docs/using/deploying-apps/migrate-db.html)をご覧ください。

## <a id='troubleshooting'></a>トラブルシューティング ##

サービスとの接続に問題がある場合、 `cf logs`コマンドでログ・メッセージとアプリケーションの環境変数を調べてください。`cf logs`コマンドの出力には環境変数VCAP_SERVICESの内容も含まれます。以下に例を示します:

<pre class="terminal">
  $ cf logs myapp
  Reading logs/console.log... OK
  Starting console on port 64868...

  Reading logs/env.log... OK
  VCAP_SERVICES={
    "elephantsql-n/a":[{
      "name":"elephant-postgres",
      "label":"elephantsql-n/a",
      "plan":"turtle",
      "credentials":{"uri":"postgres://username:password@server.example.com:5432/uniqid"}}]
  }
  ...more environment variables and values will show here...

  Reading logs/staging_task.log... OK
  ...output from the staging process displayed here...

  Reading logs/console.log
  ...output from running the application displayed here...
  --
</pre>

"a fatal error has occurred. Please see the Bundler troubleshooting
documentation"が出力される場合、bundlerをヴァージョン・アップして`bundle install`を実行してください。

<pre class="terminal">
  $ gem update bundler
  $ gem update --system
  $ bundle install
</pre>


