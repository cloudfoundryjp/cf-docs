---
title: CFoundry Ruby Gem
---

## <a id='intro'></a>紹介 ##

本ガイドはCFoundry Ruby GemでCloud Foundryインスタンスのアカウントを管理するやり方を説明します。

## <a id='connecting'></a>Cloud Foundryへの接続 ##

まず、`cfoundry` gemがアプリケーションに含まれていることを確認してください。
Bundlerを使っている場合、`Gemfile`へアプリケーションを追加してください;

~~~ruby

source 'https://rubygems.org'
gem 'cfoundry'

~~~

cfoundryの最初のステップは、クライアントのインスタンスを作りログインすることです:

~~~ruby
require 'cfoundry'

endpoint = 'http://api.cloudfoundry.com'
username = 'my_cf_user'
password = 'my_cf_password'

client = CFoundry::Client.new endpoint
client.login username, password
~~~

試しに、利用可能なサービスへの接続を表示してみます:

~~~ruby

client.services.collect { |x| x.description }

~~~

## <a id='persist-authentication'></a>認証の要求 (cfトークンを使う) ##

あなたの資格情報を公開することなくcfoundryクライアントのオブジェクトを作る安全な方法は、cfでログインして生成されたauthトークンを使うことです。
authトークンはcfによって~/.cf/tokens.ymlに保存されます。
以下のrubyコードの断片は、このファイルを開き、適切なauthトークンを選択し、それを使ってCloud Foundryへログインします。

~~~ruby

require 'cfoundry'
require 'yaml'

home = ENV['HOME']
endpoint = 'https://api.cloudfoundry.com'

config = YAML.load File.read("#{home}/.cf/tokens.yml") 
token = CFoundry::AuthToken.from_hash config[endpoint]

client = CFoundry::Client.new endpoint, token

~~~

クライアントのオブジェクトができれば、以前と同様のやり方で扱えます。

## <a id='organizations'></a>オーガナイゼーション ##

アカウントは一つ以上のオーガナイゼーションに属している必要があります。
clientクラスを使って、オーガナイゼーションを確認します;

~~~ruby

>> client.organizations
=> [#<CFoundry::V2::Organization '1e3ce9fb-50ac-4d2a-9506-f4d671c00f50'>, #<CFoundry::V2::Organization '77ab28ea-2b6e-4a7f-86c8-2c2df2714535'>]

~~~

## <a id='spaces'></a>スペース ##

Cloud Foundry上のオーガナイゼーションは、複数のスペースへ分割することができます。clientクラスで簡単に確認できます:

~~~ruby

>> client.spaces.collect { |x| x.name }
=> ["development", "staging", "production"]

~~~

スペースを作ったり削除したりする:

~~~ruby

# create a space
new_space = client.space
new_space.name = 'New Space'
new_space.organization = client.organizations.first
new_space.create!

# then, delete it
new_space.delete!

~~~

スペースを参照するメソッドがいろいろあります:

~~~ruby

# find space by name
client.space_by_name 'development'
=> #<CFoundry::V2::Space '07b7271f-bcfa-47df-aa9b-3911c60a0d65'>

client.methods.grep /space_by/
=> [:spaces_by_name, :spaces_by_organization_guid, :spaces_by_developer_guid, :spaces_by_app_guid]

~~~

## <a id='services'></a>サービス、インスタンス、サービスのプラン ##

Cloud Foundryではサービスはたくさんのサービス・プランを持っています。
この文書では、それぞれのサービスの一番目のサービス・プランを使うことにします。

クライアントのクラスは四つのサービスを含んでいます; services, service\_instances, service\_instance\_by\_name, service\_instanceの四つです。

一番目のメソッド'services'は、使用可能なすべてのサービスのハッシュを返します:

~~~ruby
client.services.collect { |x| x.description }
=> ["MySQL database", "MongoDB NoSQL database", "Redis key-value store", "RabbitMQ message queue", "PostgreSQL database (vFabric)"]
~~~

'service_instances'は実際に割り当てられているサービスのインスタンスを返します:

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

'service\_instance\_by_name' メソッドはサービスのインスタンスの一覧を返します:


~~~ruby
client.service_instance_by_name 'mysql-7327e'
=> #<CFoundry::V1::ServiceInstance 'mysql-7327e'>
~~~

サービスのインスタンスを作るには、service_instanceメソッドを使用します:

~~~ruby 
service = client.services.select{ |x| x.label == 'redis' }.first # <- find
the service we wish to deploy
service_plan =
client.service_plans_by_service_guid(service.guid).first # <- find the services first service plan

service_instance = client.service_instance # <- prepare a new instance
service_instance.name = 'my_new_redis_service' # <- give it a name

service_instance.service_plan = service_plan # <- assign the plan
service_instance.space = client.spaces.first # <- assign the space the service instance will belong to

service_instance.create! # <- send the request to create it

# if the create was successful, it should return true.

~~~
## <a id='runtimes-and-frameworks'></a>ランタイムとフレームワーク ##

ランタイムとフレームワークは、その両方を参照するのに使えるコレクションを持っています:

~~~ruby

client.frameworks.collect { |x| x.name }
=> ["play", "java_web", "buildpack", "lift", "rails3", "spring", "grails", "sinatra", "rack", "node", "standalone"]

client.runtimes.collect { |x| x.name }
=> ["java", "java7", "node", "node06", "node08", "ruby18", "ruby19"]


~~~


## <a id='applications'></a>アプリケーション ##

Clientクラスは三つのアプリケーション・メソッドを持っています; apps, app, app\_by\_nameの三つです

'apps'メソッドはデプロイしたアプリケーションの一覧を返します:

~~~ruby

client.apps
=> [#<CFoundry::V2::App '27edfb92-b0f4-47ad-acd6-cb911eb88096'>]

~~~

'app\_by\_name'メソッドは、名前を指定してアプリケーションを検索します:

~~~ruby
client.apps_by_name "node_app"
=> [#<CFoundry::V2::App '27edfb92-b0f4-47ad-acd6-cb911eb88096'>]
~~~

アプリケーションのユーザを作るには、app methodメソッドを使います:

~~~ ruby

app = client.app
app.name = 'my_new_app'
app.total_instances = 2 # <- set the number of instances you want
app.memory = 512 # <- set the allocated amount of memory
app.production = true # <- should the application run in production mode
app.framework = client.frameworks.select{ |x| x.name == 'rails3' }.first # <- set the framework
app.runtime = client.runtimes.select{ |x| x.name == 'ruby19' }.first # <- set the runtime
app.space = client.spaces.first # <- assign the application to a space

app.create!

# we may also want to bind a service
app.bind client.service_instance_by_name('my_new_redis_service')

~~~

httpでアプリケーションへアクセスできるよう、ドメイン上のルートが割り当てられている必要があります:

~~~ruby
route = client.route
route.domain = client.domains.first # <- pick the first / default route
route.space = space # <- assign the route to a space
route.host = 'my-rails-app' # <- this is the part of the url that is prepended to the domain
route.create!

app.add_route route # <- add the route to the domain

~~~

'stub'というアプリケーションが作られるだけで、実際のコードがCloud Foundryへアップロードされるわでも起動されるわけでもありません。
アプリケーションへ実際に実行するするものを提供するために、ソース、バイト・コード、バイナリなどの適切なもの(ランタイムやフレームワークに依存します)をzipファイルへアーカイブしてください。
uploadコマンドを使ってzipファイルをアップロードしてください;

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

アプリケーションはCloud Foundryへアップロードされ、残るは起動のみです:

~~~ruby
app.start!
~~~~

startの呼び出しは非同期で、ブロックが渡された場合はURLをともなってそのブロックが呼び出されます。
urlはログの置き場所で、HTTPでリクエストすることができます。
CFoundry::Clientにはstream_urlというメソッドがあり、サーバーからデータのかたまりを転送します:

~~~ruby

app.start!(true) do |url|
  begin
    offset = 0

    while true
      begin
        client.stream_url(url + "&tail&tail_offset=#{offset}") do |out|
          offset += out.size
          print out
        end
      rescue Timeout::Error
      end
    end

  rescue CFoundry::APIError
  end
end

~~~

## <a id='env-vars'></a>環境変数 ##

環境変数は以下のように簡単に変更できます:

~~~ruby

app = client.apps.first

app.env['my_env_var'] = 'env_value' # <- set the environment variable
app.update! # <- commit the change to the application => true

app.env['my_env_var'] # <- retrieve the variable => "env_value"

~~~

## <a id='domains'></a>ドメイン ##

ドメインはルートの元になっています。
Cloud Foundryのドメインのデフォルトは、Cloud Foundryインスタンスが実行されているドメインです。

新しいドメインを作るには、クライアントのクラスの'domain'メソッドを使います:

~~~ruby

domain = client.domain
domain.name = 'mydomain.com' # <- the name of the domain
domain.wildcard = true
domain.owning_organization = client.organizations.first # <- set the owning organization
domain.create! # <- commit

~~~

すでに存在しているドメインを取り出すには:

~~~ruby

client.domains # <- all domains => [#<CFoundry::V2::Domain '665b1cf3-e736-42cb-b7fe-bae8362fc30d'>]

client.domains_by_owning_organization_guid client.organizations.first # <- domains belonging to an organization
=> [#<CFoundry::V2::Domain '665b1cf3-e736-42cb-b7fe-bae8362fc30d'>]

client.domains_by_space_guid client.spaces.first # <- domains assigned to a
space
=> [#<CFoundry::V2::Domain '665b1cf3-e736-42cb-b7fe-bae8362fc30d'>]

~~~
