---
title: Rubyのためのサービス接続設定
---

このページでは、Rubyアプリケーションからサービスへの接続の設定について述べています。

## <a id='options'></a>Rubyアプリとサービスの設定のオプション ##

Rubyアプリケーションをサービスに接続するのに、設定する方法はいくつかあります:

* 接続オブジェクトを作ってください --- そうするのに、`cfruntime` gem
  が使えます。これがお勧めの方法になりますが、接続をきめ細かく制御できるためです。詳しくは[Manually Configure Connection
  with cfruntime](#manual)をご覧ください。

* データベースを使うアプリケーションでは `database.yml`内で接続を定義できます ---
  環境変数`VCAP_SERVICES`内のバインドされたすべてのサービスの接続情報を調べて、それに応じて`database.yml`ファイルを更新することができます。詳しくは[Define
  Connection in Configuration File](#config-file)をご覧ください。

* オート・コンフィギュレーション -- `cf-autoconfig` gemを使って自動的に設定することができます。 以下の条件があります: (1)
  Rails フレームワークであること, (2) サービスがPostgreSQLかMySQLであること, (3)
  複数のサービスのインスタンスがバインドされていないこと詳しくは、[Auto-Configure Connection with
  cf-autoconfig](#autoconfig)をご覧ください。

     **注意:** データベースを使うRuby on Railsアプリケーション向けのオート・コンフィギュレーションは、`database.yml`ファイル内の接続情報を上書きします -- 問題があるようなら、オート・コンフィギュレーションを使わずに自分で設定してください  


## <a id='prereq'></a>前提 ##

適切なgemがGemfileに記述されていることを確認してください:

<table>
  <tr>
    <th>Service type</th><th>Alias</th>
  </tr>
  <tr><td>MySQL</td><td><a href="https://github.com/brianmario/mysql2">mysql2</a></td></tr>
  <tr><td>MongoDB</td><td><a href="http://mongomapper.com/">mongo_mapper</a> (ORM), <a href="https://github.com/mongodb/mongo-ruby-driver">mongo</a></td></tr>
  <tr><td>RabbitMQ</td><td><a href="https://github.com/ruby-amqp/amqp">ampq</a></td></tr>
  <tr><td>Postgres</td><td><a href="https://rubygems.org/gems/pg">pg</a></td></tr>
  <tr><td>Redis</td><td><a href="https://github.com/redis/redis-rb">redis</a></td></tr>
</table>

`bundle install`を実行して`Gemfile.lock`を更新してください。


## <a id='manual'></a>cfruntimeを使って手動で設定する</a> ##

このやり方では、`cf-runtime` gemを使って接続情報を得て、アプリケーションを更新する必要があります。The `cf-runtime` gemは、バインドされたサービスへの接続の設定を容易にするヘルパー機能を提供します。


### <a id='connecting-to-a-named-service'></a>名前を指定したサービスへの接続 ###

名前で指定したサービスへの接続を作るには、クライアントのクラスの`create_from_svc`メソッドを使います。以下のテーブルは、`cf-runtime`ライブラリの接続のクラスの一覧を示しています;
`create_from_svc`をどのクライアンのクラスからでも呼ぶことができます。

<table>
  <tr>
    <th>Service type</th><th>CF-Runtime class</th>
  </tr>
  <tr><td>MySQL</td><td>CFRuntime::Mysql2Client</td></tr>
  <tr><td>MongoDB</td><td>CFRuntime::MongoClient</td></tr>
  <tr><td>RabbitMQ</td><td>CFRuntime::AMQPClient or CFRuntime::CarrotClient</td></tr>
  <tr><td>Postgres</td><td>CFRuntime::PGClient</td></tr>
  <tr><td>Redis</td><td>CFRuntime::RedisClient</td></tr>
  <tr><td>Blob</td><td>CFRuntime::AWSS3Client</td></tr>
</table>

**例1**

"mysql-test"という名前のMySQLインスタンスへの接続を作るには:

~~~ruby

require 'cfruntime' client = CFRuntime::Mysql2Client.create_from_svc
'mysql-test'

~~~

**例2**

"mongo-test"という名前のMongoDB インスタンスへの接続を作るには:

~~~ruby

require 'cfruntime' client = CFRuntime::MongoClient.create_from_svc
'mysql-test'

~~~

### <a id='connecting-to-one-instance'></a>Connect to Only Instance of a
Service ###

あるサービスに対してただ一つのインスタンスだけを使う場合、名前を指定する必要はありません。例として:

~~~ruby

require 'cfruntime' connection = CFRuntime::MongoClient.create db =
connection.db

~~~

上の例では、アプリケーションにバインドされたMongoDBインスタンスを見つけ、クライアントを取って来ます。もしあるタイプのサービスで複数のインスタンスがある場合、ライブラリはArgumentErrorというエラーを上げます。上に挙げたクラスは、`create`に関して同様に反応します。

### <a id='obtaining-connection-properties'></a>Get Connection Data with
cfruntime ###

`cfruntime`は、接続のプロパティを得るメソッドを提供します。`CFRuntime::CloudApp`クラスを使って、`service_props`メソッドはサービスの名前かインスタンスの名前とともに呼び出せます。たとえば、MySQLサービスから接続の詳細を取り出すには:

~~~ruby

require 'cfruntime' service_props = CFRuntime::CloudApp.service_props
'mysql'

~~~

下の表は、各サービスの別名の一覧です。

<table>
  <tr>
    <th>Service type</th><th>Alias</th>
  </tr>
  <tr><td>MySQL</td><td>mysql</td></tr>
  <tr><td>MongoDB</td><td>mongodb</td></tr>
  <tr><td>RabbitMQ</td><td>rabbitmq</td></tr>
  <tr><td>Postgres</td><td>postgresql</td></tr>
  <tr><td>Redis</td><td>redis</td></tr>
  <tr><td>Blob</td><td>blob</td></tr>
</table>

`service_props`へサービスの名前を渡してあるインスタンスの情報を取り出すこともできます。

### <a id='other-hand-methods'></a>Useful cfruntime Methods ###

下の表は`CFRuntime::Cloudapp`クラスの有用なメソッドの一覧です。


<table>
  <tr>
    <th>Method</th><th>Purpose</th>
  </tr>
  <tr><td>CFRuntime::CloudApp.host</td><td>Returns the host name of the VM running the application</td></tr>
  <tr><td>CFRuntime::CloudApp.port</td><td>Returns the port number assigned to the application</td></tr>
  <tr><td>CFRuntime::CloudApp.service_names</td><td>Returns a list of the service names bound to the application</td></tr>
  <tr><td>CFRuntime::CloudApp.service_names_of_type</td><td>Returns a list of the service names of a particular type that are bound to the application</td></tr>
  <tr><td>CFRuntime::CloudApp.running_in_cloud?</td><td>Returns a boolean value, indicating if the application is running on a Cloud Foundry instance</td></tr>
</table>

## <a id='config-file'></a>Define Connection in Configuration File ##

データベースを使うRubyアプリケーションでは、データベースへの接続は`database.yml`ファイルで設定できます。環境変数`VCAP_SERVICES`から接続情報の詳細を得ることができ、それにはバインドされているすべてのサービスの情報と資格情報がJSON形式で含まれています。`cf
bind-service`コマンドでサービスをアプリケーションへバインドする際、cfは接続情報を環境変数`VCAP_SERVICES`へ書き込みます。

Rubyでは、`VCAP_SERVICES`の内容に`ENV`でアクセスできます。

例:

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

The general format of this string is *provider-version*.  In this case the
provider is "elephantsql" and the version is "n/a" so the hash key is
"elephantsql-n/a".

Given this example, you can pull the credentials for your service with these
ruby statements:

~~~ruby
  db = JSON.parse(ENV['VCAP_SERVICES'])["elephantsql-n/a"]
  credentials = db.first["credentials"]
  host = credentials["host"]
  username = credentials["username"]
  password = credentials["password"]
  database = credentials["database"]
  port = credentials["port"]
~~~

Railsアプリケーションを設定するために、`database.yml`を変更することになります。VCAP_SERVICESからの資格情報を使い、erb形式で接続情報を記述してください。

ElephantSQL Postgresを使っている場合、`database.yml`ファイルは下のようになります:

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

Active Recordまたは他のORMを使っていて、インスタンス化を直接記述しない場合、コードは以下のようになります:

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

環境変数VCAP_SERVICESの中のキーによりコードは変化します。


## <a id='autoconfig'></a>Auto-Configure Connection with cf-autoconfig ##


現時点ではRailsアプリケーションのみサポートされていますが、オート・コンフィギュレーションを使うためには、以下の行をGemfileへ追加してください:

~~~ruby gem 'cf-autoconfig' ~~~

Gemfileの更新後、`bundle install`を実行して`Gemfile.lock`を更新してください。



## <a id='database-migration'></a>Create and Populate Database Tables ##

データベースを使いはじめる前に、migrationスクリプトの実行が必要です。

Railsを使っている場合、データベースのスキーマを初期化するために、以下のようなコマンドを実行します:

<pre class="terminal">
  $ bundle exec rake db:create db:migrate
</pre>

同様のプロセスがCloud Foundry上のアプリケーションに対しても必要です。アプリケーションをプッシュする際、'cf push
--command`オプションでマイグレーションのスクリプトを指定します。例:

<pre class="terminal">
  $ cf push --command "bundle exec rake db:create db:migrate" myapp
</pre>

cfでmanifest.ymlファイルを使っている場合、マニフェスト内の設定を上書きするために`--reset`オプションをつけるのを忘れないでください。

データベースのマイグレーションの後、起動コマンドを元にもどすためにもう一度アプリケーションをプッシュしてください。

起動コマンドをデフォルトの状態にもどすために、以下のコマンドを実行してください:

<pre class="terminal">
  $ cf push --command "" myapp
</pre>

独自の起動コマンドを使いたい場合は、環境変数PORTを含めてください。例:

<pre class="terminal">
  $ cf push --command 'bundle exec rackup -p$PORT' myapp
</pre>

## <a id='troubleshooting'></a>Troubleshooting ##

サービスとの接続に問題がある場合、 `cf logs`コマンドでログ・メッセージとアプリケーションの環境変数を調べてください。`cf
logs`コマンドの出力には環境変数VCAP_SERVICESの内容も含まれます。以下に例を示します:

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


