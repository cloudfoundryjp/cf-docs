---
title: Sinatra, Getting Started
---

## <a id='intro'></a>紹介 ##

Cloud FoundryはSinatraアプリケーションをサポートしています。本ガイドに従ってアプリケーションのサンプルを作り、Cloud
Foundryへデプロイしてみてください。

## <a id='prerequisites'></a>前提 ##

以下の前提を満たす必要があります;

* Cloud Foundryのアカウント。
  右のページでサインアップできます。[サインアップ](https://my.cloudfoundry.com/signup
* [Ruby](http://www.ruby-lang.org/en/)
* [Bundler](http://gembundler.com/)
* The [CF](../../managing-apps/) コマンド・ライン・ツール

## <a id='sample-project'></a>サンプル・プロジェクトの作成 ##

フォルダを作り、Rackアプリケーションの基本構造を用意します。

<pre class="terminal"> $ mkdir sinatra_hello_world $ cd sinatra_hello_world
$ touch hello_world.rb config.ru Gemfile </pre>

二つのファイルを以下のように初期化します;

hello_world.rb

~~~ruby require 'sinatra/base'

class HelloWorld < Sinatra::Base
  get "/" do
    "Hello, World!"
  end
end
~~~

config.ru

~~~ruby require './hello_world' run HelloWorld.new ~~~

Gemfile

~~~ruby source 'https://rubygems.org' gem 'sinatra' ~~~

Install the required Sinatra gem using Bundler;

<pre class="terminal"> $ bundle install </pre>

Rackupを使い、ローカルでアプリケーションを動かせる必要があります;

<pre class="terminal"> $ rackup >> Thin web server (v1.4.1 codename Chromeo)
>> Maximum connections set to 1024 >> Listening on 0.0.0.0:9292, CTRL+C to
stop </pre>

あなたのアプリケーションを[http://localhost:9292](http://localhost:9292)で見てみます。

## <a id='deploying'></a>Deploying Your Application ##

CFコマンドでアプリケーションをプッシュします;

<pre class="terminal"> $ cf push

Name> sinatra-hello-world

Instances> 1

1: 64M 2: 128M 3: 256M 4: 512M 5: 1G 6: 2G 7: 4G 8: 8G 9: 16G Memory Limit>
128M

Creating rack-test... OK

1: sinatra-hello-world.cloudfoundry.com 2: none URL>
sinatra-hello-world.cloudfoundry.com

Updating rack-test... OK

Create services for application?> n

Bind other services to application?> n

Save configuration?> n

Uploading sinatra-hello-world... OK Starting sinatra-hello-world... OK
Checking sinatra-hello-world... OK </pre>

アプリケーションをデプロイすると、プッシュした時に指定したURLでアクセスできます。

## <a id='next-steps'></a>Next steps - Binding a service ##

Rails 3とサービスの接続と利用については右のページを参照してください。 [here](./ruby-service-bindings.html)
