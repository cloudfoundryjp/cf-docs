---
title: Rails 3, Getting Started
---

## <a id='intro'></a>紹介 ##

Cloud FoundryはRails 3をサポートしています。古いヴァージョンも含みます。本ガイドに従ってアプリケーションのサンプルを作り、Cloud
Foundryへデプロイしてみてください。

## <a id='prerequisites'></a>前提 ##

以下の前提を満たす必要があります;

* Cloud Foundryのアカウント。 右のページでサインアップできます。[サインアップ](https://my.cloudfoundry.com/signup
* [Ruby](http://www.ruby-lang.org/en/)
* [Rails](http://rubyonrails.org/)
* [Bundler](http://gembundler.com/)
* [CF](../../managing-apps/index.html) コマンド・ライン・ツール
* Railsの作り方と実行方法の基本的な知識

## <a id='sample-project'></a>サンプル・プロジェクトの作成 ##

サンプルのrailsアプリケーションのサンプルを作成し、作成した時のフォルダで実行します。

<pre class="terminal">
$ rails new sample_rails
$ cd sample_rails
</pre>

例のモデルのscaffoldを作ります。次の二つのどちらかを使ってみましょう。Rails scaffold [tutorial](http://guides.rubyonrails.org/getting_started.html#getting-up-and-running-quickly-with-scaffolding) migrationを実行します。

<pre class="terminal">
$ rails generate scaffold Post name:string title:string content:text
$ rake db:migrate
</pre>

publicフォルダからindex.htmlを削除します。

<pre class="terminal">
$ rm public/index.html
$ vi config/routes.rb
</pre>

アプリケーションのrootが新しいcontrollerを差すよう変更します。以下の通りにしてください。

~~~ruby
SampleRails::Application.routes.draw do
  resources :posts
  root :to => 'posts#index'
end
~~~

例を実行し、記事の追加や削除ができることを確認しましょう。

<pre class="terminal">
$ rails s
</pre>

## <a id='assets'></a>Assets, Precompile or Not? ##

Cloud FoundryはRails asset pipelineをサポートしています。Cloud Foundryへプッシュする前にプリコンパイルされたアセットを用意しなければ、ステージされる時に用意されるということです。アセットをプリコンパイルするために、以下のコマンドを実行してください;

<pre class="terminal">
rake assets:precompile
</pre>

これにより、ステージされるたびにプリコンパイルが実行されなくなり、速度が向上することになります。

アプリケーションの初期化の際、潜在的な危険が一つあります。プリコンパイルされたrakeタスクはRailsアプリケーションの再初期化を実行します。これは、サービスへの接続や環境のチェックなどステージングの段階では使えないものを必要とすることがあります。application.rb内のオプションで再初期化されないようにできます:

~~~ruby
config.assets.initialize_on_precompile = false
~~~

assets:precompileが失敗すると、Cloud Foundryはライブ・コンピレーションを有効にします。これはプレコンパイルの代替手段になります。このモードでは、最初にロードされる時にコンパイルされます。application.rb内で設定を追加してライブ・コンパイレーションを有効にできます。

~~~ruby
Rails.application.config.assets.compile = true
~~~

## <a id='deploying'></a>Deploying Your Application ##

インストール済みのCFコマンドで、適切なCloud Foundryをターゲットとして指定し、ログインしてください。

<pre class="terminal">
$ cf target api.cloudfoundry.com
Setting target to https://api.cloudfoundry.com... OK

$ cf login
</pre>

"push"コマンドでアプリケーションをデプロイします;

<pre class="terminal">
$ cf push rails-3-test
Instances> 1

1: 64M
2: 128M
3: 256M
4: 512M
5: 1G
6: 2G
7: 4G
8: 8G
9: 16G
Memory Limit> 256M

Creating rails-3-test... OK

1: rails-3-test.cloudfoundry.com
2: none
URL> rails-3-test.cloudfoundry.com

Updating rails-3-test... OK

Create services for application?> n

Bind other services to application?> n

Save configuration?> n

Uploading rails-3-test... OK
Starting rails-3-test... OK
Checking rails-3-test... OK
</pre>

アプリケーションをデプロイすると、プッシュした時に指定したURLでアクセスできます。

## <a id='next-steps'></a>Next steps - Binding a service ##

Rails 3とサービスの接続と利用については右のページを参照してください。 [here](./ruby-service-bindings.html)
