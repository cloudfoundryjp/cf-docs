---
title: CFoundry Ruby Gemでアプリケーションを管理する (旧版のCloud Foundry)
---

## <a id='intro'></a>紹介 ##

本ガイドはCFoundry Ruby GemでCloud Foundryインスタンスのアカウントを管理するやり方を説明します。

## <a id='connecting'></a>Cloud Foundryへの接続 ##

まず、cfoundry gemがアプリケーションに含まれていることを確認してください。bundlerを使っている場合は、Gemfileに記述します;

~~~ruby

source 'https://rubygems.org' gem 'cfoundry'

~~~

cfoundryの最初のステップは、クライアントのインスタンスを作りログインすることです。;

~~~ruby require 'cfoundry'

endpoint = 'http://api.cloudfoundry.com' username = 'my_cf_user' password =
'my_cf_password'

client = CFoundry::Client.new endpoint client.login username, password ~~~

Test the connection by listing the available services;

~~~ruby

client.services.collect { |x| x.description }

~~~

## <a id='persist-authentication'></a>Persisting Authentication (Using cf
tokens) ##

あなたの資格情報をばらすことなくcfoundryクライアントのオブジェクトを作る安全な方法は、cfでログインして生成されたauthトークンを使うことです。authトークンはcfによって~/.cf/tokens.ymlに保存されます。以下のrubyコードの断片は、このファイルを開き、適切なauthトークンを選択し、それを使ってCloud
Foundryへログインします。

~~~ruby

require 'cfoundry' require 'yaml'

home = ENV['HOME'] endpoint = 'https://api.cloudfoundry.com'

config = YAML.load File.read("#{home}/.cf/tokens.yml")  token =
CFoundry::AuthToken.from_hash config[endpoint]

client = CFoundry::Client.new endpoint, token

~~~

クライアントのオブジェクトができれば、以前と同様のやり方で扱えます。

## <a id='services'></a>Services ##

クライアントのクラスは四つのサービスを含んでいます; services, service_instances,
service_instance_by_name, service_instanceの四つです。

一番目のメソッド'services'は、使用可能なすべてのサービスのハッシュを返します。;

~~~ruby
pp client.services
[#<CFoundry::V1::Service:0x007fb844068ca0
  @description="MySQL database",
  @label="mysql",
  @provider="core",
  @state=:current,
  @type="database",
  @version="5.1">,
 #<CFoundry::V1::Service:0x007fb844068ac0
  @description="PostgreSQL database (vFabric)",
  @label="postgresql",
  @provider="core",
  @state=:current,
  @type="database",
  @version="9.0">,
 ...
 #<CFoundry::V1::Service:0x007fb8440678f0
  @description="MongoDB NoSQL database",
  @label="mongodb",
  @provider="core",
  @state=:current,
  @type="document",
  @version="2.0">]
~~~

'service_instances'は実際に割り当てられているサービスのインスタンスを返します;

~~~ruby
pp client.service_instances
[#<CFoundry::V1::ServiceInstance 'mysql-7327e'>,
 #<CFoundry::V1::ServiceInstance 'redis-6b10d'>,
 #<CFoundry::V1::ServiceInstance 'mongodb-3894a'>,
 #<CFoundry::V1::ServiceInstance 'postgresql-e7870'>,
 #<CFoundry::V1::ServiceInstance 'mysql-adeb7'>,
 ...
 #<CFoundry::V1::ServiceInstance 'mysql-13e8f'>,
 #<CFoundry::V1::ServiceInstance 'mongodb-220c4'>]
~~~

The 'service\_instance\_by_name' method returns a named service instance;

~~~ruby client.service_instance_by_name 'mysql-7327e' =>
#<CFoundry::V1::ServiceInstance 'mysql-7327e'> ~~~

サービスのインスタンスを作るには、service_instanceメソッドを使用します;

~~~ruby service = client.service_instance 'my_new_service' service.vendor =
'redis' # <- this is the label property of the service service.version =
'2.6' # <- if there are multiple versions of the same service, specify the
one required service.tier = 'free' service.create!

# if the create was succesful, it should return true.  ~~~ ## <a
id='runtimes-and-frameworks'></a>Runtimes and Frameworks ##

ランタイムとフレームワークは、その両方を参照するのに使えるコレクションを持っています;

~~~ruby

client.frameworks.collect { |x| x.name } => ["rails3", "rack", "java_web",
"play", "spring", "node", "standalone", "lift", "sinatra", "grails"]

client.runtimes.collect { |x| x.name } => ["java", "java7", "node",
"node06", "node08", "ruby18", "ruby19"]

~~~

## <a id='applications'></a>アプリケーション ##

Clientクラスは三つのアプリケーション・メソッドを持っています; apps, app, app_by_nameの三つです

'apps'メソッドはデプロイしたアプリケーションの一覧を返します;

~~~ruby

pp client.apps
[#<CFoundry::V1::App 'sidekiq'>,
 #<CFoundry::V1::App 'sidekiq-worker'>,
 #<CFoundry::V1::App 'caldecott'>,
 #<CFoundry::V1::App 'db-sample'>,
 #<CFoundry::V1::App 'go-test'>]

~~~

'app\_by\_name'メソッドは、名前を指定してアプリケーションを検索します;

~~~ruby client.app_by_name 'sidekiq' => #<CFoundry::V1::App 'sidekiq'> ~~~

アプリケーションのユーザを作るには、app methodメソッドを使います;

~~~ ruby

app = client.app 'my_new_app' app.instances = 1 # <- set the number of
instances you want app.memory = 256 # <- set the allocated amount of memory
app.services = [service] # <- set the services bound to the appliation as an
array of references to service_instance objects (optional)
app.framework_name = 'standalone' # <- the name of the framework app.command
= 'bundle exec ./app.rb' # <- an optional command to start the application
(if standalone)  app.runtime_name = 'ruby19' # <- the name of the runtime
app.create!

~~~

'stub'というアプリケーションが作られるだけで、実際のコードがCloud
Foundryへアップロードされるわでも起動されるわけでもありません。アプリケーションへ実際に実行するするものを提供するために、ソース、バイト・コード、バイナリなどの適切なもの(ランタイムやフレームワークに依存します)をzipファイルへアーカイブしてください。uploadコマンドを使ってzipファイルをアップロードしてください;

~~~ruby

require 'zip/zip'

# create an archive of the current folder

Zip::ZipFile.open('app.zip', Zip::ZipFile::CREATE) do |zipfile|
  Dir.glob('**/*').each do |filename|
    zipfile.add(filename, File.join(folder, filename))
  end
end

app.upload 'app.zip'

~~~

アプリケーションはCloud Foundryへアップロードされ、残るは起動のみです。
