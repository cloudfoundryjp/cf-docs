---
title: Rubyアプリ(Rack, Rails, Sinatra)をデプロイする。
---

Rack, Rails, Sinatraアプリのデプロイの準備については、右のページをご欄ください。
[入門](../../../dotcom/getting-started.html).

## <a id='bundler'></a>アプリケーションのバンドル ##

GemfileとGemfile.lockを作るために<a
href="http://gembundler.com/">Bundler</a>コマンドを実行してください。Cloud
Foundryへプッシュする前にこの二つのファイルを用意してください。

## <a id='config'></a> Rackの設定ファイル ##

**Rack** か **Sinatra** には、`config.ru`が必要です。例を示します:

~~~ruby require './hello_world' run HelloWorld.new ~~~

## <a id='precompile'></a> Asset precompilation ##

Cloud FoundryはRails asset pipelineをサポートしています。Cloud
Foundryへプッシュする前にプリコンパイルされたアセットを用意しなければ、ステージされる時に用意されるということです。デプロイの前にプレコンパイルするには、以下のコマンドを使ってください:

<pre class="terminal"> rake assets:precompile </pre>

これにより、ステージされるたびにプリコンパイルが実行されなくなり、速度が向上することになります。

アプリケーションの初期化の際、潜在的な危険が一つあります。プリコンパイルされたrakeタスクはRailsアプリケーションの再初期化を実行します。これは、サービスへの接続や環境のチェックなどステージングの段階では使えないものを必要とすることがあります。application.rb内のオプションで再初期化されないようにできます:

~~~ruby config.assets.initialize_on_precompile = false ~~~

assets:precompileが失敗すると、Cloud
Foundryはライブ・コンピレーションを有効にします。これはプレコンパイルの代替手段になります。このモードでは、最初にロードされる時にコンパイルされます。application.rb内で設定を追加してライブ・コンパイレーションを有効にできます。

~~~ruby Rails.application.config.assets.compile = true ~~~

## <a id='workers'></a> Workers tasks ##

Cloud Foundryはワーカー・プロセスをサポートしています。動作の仕組みについては右のページ[Rails workers quick
start](rails-running-worker-tasks.html)をご参照ください。

## <a id='console'></a> Railsコンソール ##

Cloud Foundry v2ではRail Consoleは未サポートです。

## <a id='services'></a>サービスへのバインド #

[Rubyのサービスのバインドのやり方](../../services/ruby-service-bindings.html)をご覧ください

## <a id='rake'></a>Rakeタスクの実行 ##

あなたのアプリケーションが使っているデータベースなどのサービスがCloud
Foundryで未サポートで、直接接続可能なら、rakeタスクをローカルで(Cloud Foundry上ではなく)実行し database
migration
することができます。他のタスクも実行できます。サービスへの接続情報を保持している環境変数`VCAP_SERVICES`の内容へアクセスする方法については、以下をご覧ください。

あるいは、manifest.yml内の起動コマンドを使ってCloud Foundry上でrakeタスクを実行できます。

最初にデプロイする時、設定を保存するかきかれます。これにより、最初のプッシュの時に入力した設定がアプリケーション内の`manifest.yml`へ格納されます。`manifest.yml`を編集し、以下のように起動コマンドを記述してください:

~~~yaml
---
applications:
- name: my-rails-app
  command: rake db:migrate && rails s
... the rest of your settings  ...
~~~

**重要** 最初のマイグレーションは、一つのアプリケーションのインスタンスに対して一度だけ実行できます。マイグレーションの実行後、`cf scale`コマンドでアプリケーションをスケールできます。`VCAP_APPLICATION`環境変数(`instance_id`を含んでいる)内のユニークなインスタンス・ナンバー(JSON形式)を参照するスクリプトを使って、さらにマイグレーションを実行することができます。

`VCAP_APPLICATION`は`VCAP_SERVICES`と同様に働きます。`VCAP_SERVICES`については[here](../../services/environment-variable.html)をご覧ください。

Cloud Foundry上のRubyアプリで`VCAP_APPLICATION`の内容を見るには以下を使います:

`ENV['VCAP_APPLICATION']`

`cf log APPNAME`コマンドで`env.log`の内容を取り出すこともできます。

~~~ Reading logs/env.log... OK TMPDIR=/home/vcap/tmp VCAP_APP_PORT=61169
VCAP_CONSOLE_IP=0.0.0.0 USER=vcap
VCAP_APPLICATION={"application_users":[],"instance_id":"d05ee6e8198d8b8deb51b3a5dcd0f228","instance_index":0,"application_version":"0699019d-51f4-409e-b588-ef6669596c6f","application_name":"railsnew","application_uris":["railsnew.cfapps.io"],"started_at":"2013-06-14
01:46:34
+0000","started_at_timestamp":1371174394,"host":"0.0.0.0","port":61169,"limits":{"mem":256,"disk":1024,"fds":16384},"version":"0699019d-51f4-409e-b588-ef6669596c6f","name":"railsnew","uris":["railsnew.cfapps.io"],"users":[],"start":"2013-06-14
01:46:34 +0000","state_timestamp":1371174394} RACK_ENV=production
PATH=/home/vcap/app/bin:/home/vcap/app/vendor/bundle/ruby/1.9.1/bin:/bin:/usr/bin:/bin:/usr/bin
PWD=/home/vcap LANG=en_US.UTF-8 VCAP_SERVICES={} HOME=/home/vcap/app SHLVL=2
RAILS_ENV=production GEM_PATH=/home/vcap/app/vendor/bundle/ruby/1.9.1:
PORT=61169 VCAP_APP_HOST=0.0.0.0 MEMORY_LIMIT=256m DATABASE_URL=
VCAP_CONSOLE_PORT=61170 _=/usr/bin/env

~~~
