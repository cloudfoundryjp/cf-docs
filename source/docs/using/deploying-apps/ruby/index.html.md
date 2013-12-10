---
title: Rack, Rails, Sinatraアプリケーションをデプロイする。
---

このページではRack, Rails, Sinatraアプリケーションのデプロイ特有の情報について説明しています。関連情報については、[入門](../../../dotcom/getting-started.html)をご欄ください。

## <a id='bundler'></a>アプリケーションのバンドル ##

GemfileとGemfile.lockを作るために<a href="http://gembundler.com/">Bundler</a>コマンドを実行してください。Cloud Foundryへプッシュする前にこの二つのファイルを用意してください。

## <a id='config'></a> Rackの設定ファイル ##

**Rack** か **Sinatra** には、`config.ru`が必要です。例を示します:

~~~ruby
require './hello_world'
run HelloWorld.new
~~~

## <a id='precompile'></a> アセットのプリコンパイル ##

Cloud FoundryはRails asset pipelineをサポートしています。Cloud Foundryへプッシュする前にプリコンパイルされたアセットを用意しなければ、ステージされる時に用意されるということです。デプロイの前にプレコンパイルすれば、ステージの時間を短縮できます。

アセットをプリコンパイルするには以下のコマンドを使ってください:

<pre class="terminal">
rake assets:precompile
</pre>

アプリケーションの初期化の際、潜在的な危険が一つあります。
Rakeプリコンパイル・タスクはRailsアプリケーションを再初期化することに注意してください。初期化がステージングの段階では利用できないサービス接続や環境のチェックを必要とする場合、問題になり得ます。プリコンパイルの際に再初期化されないようにするためには、`application.rb`に次の行を追加してください:

~~~ruby
config.assets.initialize_on_precompile = false
~~~

assets:precompileが失敗すると、Cloud Foundryはライブ・コンパイレーション・モード
を有効にします。このモードでは、最初にロードされる時にコンパイルされます。`application.rb`に以下の行を追加してライブ・コンパイレーションを有効にできます。

~~~ruby
Rails.application.config.assets.compile = true
~~~

## <a id='workers'></a> ワーカー・タスク ##

Cloud Foundryはワーカー・タスクをサポートしています。動作の仕組みについては右のページ[Rails workers quick start](rails-running-worker-tasks.html)をご参照ください。

## <a id='console'></a> Railsコンソール ##

Cloud Foundry v2ではRail Consoleは未サポートです。

## <a id='services'></a>サービスへのバインド ##

[Rubyのサービスのバインドのやり方](../../services/ruby-service-bindings.html)をご覧ください

## <a id='rake'></a>Rakeタスクの実行 ##

Cloud Foundryはrakeタスクをデプロイしたアプリケーション上で実行する仕組みを提供していません。
Cloud Foundry上でrakeタスクを実行する必要があれば(デプロイの前にローカルで実行するのではなく)、Cloud Foundryがアプリケーションを起動するのに使うコマンドとしてrakeタスクを実行することができます。

アプリケーションを起動するコマンドは、`manifest.yml`ファイルの `command`属性で設定します。

以前にアプリケーションをデプロイしたことがあれば、マニフェスト・ファイルは既に存在しています。マニフェストを作る方法は二通りあります。最初にデプロイする前に、手動でマニフェスト・ファイルを作り、アプリケーションのルート・ディレクトリに保存しておく方法があります。手動で作っておかなければ、最初のデプロイの際にcfが必要な情報の入力を求め、それを元にマニフェスト・ファイルを作って保存します。詳しくは[Application Manifests](/docs/using/deploying-apps/manifest.html)をご覧ください。

rakeのデータベースのマイグレーションを行なう例が[Migrate a Database for a Ruby App](/docs/using/deploying-apps/migrate-db.html#migrate-ruby-db)にあります。
