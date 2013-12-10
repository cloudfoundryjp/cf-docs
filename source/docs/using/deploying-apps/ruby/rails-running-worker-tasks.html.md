---
title: Rails 3, ワーカー・タスクを実行する
---

## <a id='intro'></a>紹介 ##

Rails 3アプリケーションを開発する際、資源を節約してユーザからのリクエストに使えるようにいくつかのタスクを遅らせたいことがよくあります。

本ガイドはワーカー・ライブラリを用いたRailsアプリケーションの例の作り方とデプロイの方法を示します。ワーカー・ライブラリで遅延されたタスクは別のアプリケーションとして実行されます。また、ワーカー・アプリケーションのリソースのスケールのさせ方も示します。

## <a id='worker-libs'></a>ワーカー・タスク・ライブラリの選択 ##

最初の作業として、どのワーカー・ライブラリを使うか決めます。Ruby / Railsの主要なライブラリの概要を示します;

### [Delayed::Job](https://github.com/collectiveidea/delayed_job) ###

"[Shopify](http://www.shopify.com/)自身のライブラリで、ジョブ・テーブルで複数のコア・タスクを管理します。"

### [Resque](https://github.com/defunkt/resque) ###

"Redisベースのライブラリで、バックグラウンド・ジョブを作り、複数のキューへ格納し、後で実行します。"

### [Sidekiq](https://github.com/mperham/sidekiq) ###

「一つのプロセスで多くのメッセージを同時にスレッドを使います。Railsは必須ではありませんが、Rails
3と密に統合することによりバックグラウンドでのメッセージ処理をとても簡単にします」 このライブラリは
Redis-backedであり、おおむねResque messagingと互換性があります。

他にもたくさんありますので、 https://www.ruby-toolbox.com/categories/Background_Jobsをご参照ください!

## <a id='example-app'></a> サンプル・アプリケーションの作成 ##

アプリケーションの例としてSidekiqを使います。セットアップはわかりやすく、性能もすぐれています。

最初に、モデルthingsを持つRailsアプリケーションを作ります。;

<pre class="terminal">
$ rails create rails-sidekiq
$ cd rails-sidekiq
$ rails g model Thing title:string description:string </pre>

Gemfileにsidekiqとuuidtoolsを追加します。以下のようになります。;

~~~ruby
source 'https://rubygems.org'

gem 'rails', '3.2.9' gem 'mysql2'

group :assets do
  gem 'sass-rails',   '~> 3.2.3'
  gem 'coffee-rails', '~> 3.2.1'
  gem 'uglifier', '>= 1.0.3'
end

gem 'jquery-rails'
gem 'sidekiq'
gem 'uuidtools'
~~~

bundleをインストールします;

<pre class="terminal">
$ bundle install
</pre>

sidekiqのためのワーカーをapp/workers内に作ります;

<pre class="terminal">
$ touch app/workers/thing_worker.rb
</pre>

~~~ruby
class ThingWorker

  include Sidekiq::Worker

  def perform(count)

    count.times do

      thing_uuid = UUIDTools::UUID.random_create.to_s
      Thing.create :title => "New Thing (#{thing_uuid})", :description => "This is the description for thing #{thing_uuid}"
    end

  end

end
~~~

nをworkerに引き渡される数とすると、workerがn個のthingsが作成します。

'things'のコントローラを作成します;

<pre class="terminal">
$ rails g controller Thing
</pre>

~~~ruby
class ThingController < ApplicationController

  def new
    ThingWorker.perform_async(2)
    redirect_to '/thing'
  end

  def index
    @things = Thing.all
  end

end
~~~

thingsのためのviewを追加します;

<pre class="terminal">
$ mkdir app/views/things
$ touch app/views/things/index.html.erb
</pre>

~~~html
<%= @things.inspect %>
~~~

## <a id='deploy'></a>1回目のデプロイ、2回目のデプロイ…  ##

このアプリケーションは2回デプロイする必要があります。1回はRailsアプリケーションのため、もう1回はRubyアプリケーションのためです。もっとも簡単な方法は、別々のcfマニフェストとして扱うことです;

Web Manifest (web-manifest.ymlとして保存);

~~~yaml
---
applications:
- name: sidekiq
  memory: 256M
  instances: 1
  host: sidekiq
  domain: ${target-base}
  path: .
  services:
    sidekiq-mysql:
      vendor: mysql
      version: "5.1"
      tier: free
    sidekiq-redis:
      vendor: redis
      version: "2.6"
      tier: free
~~~

Worker Manifest (worker-manifest.ymlとして保存);

~~~yaml
---
applications:
- name: sidekiq-worker
  memory: 256M
  instances: 1
  path: .
  command: bundle exec sidekiq
  services:
    sidekiq-redis:
      vendor: redis
      version: "2.6"
      tier: free
    sidekiq-mysql:
      vendor: mysql
      version: "5.1"
      tier: free
~~~

'sidekiq.cloudfoundry.com'というURLが使われるでしょう。変更はweb-manifest.ymlで。両方のマニフェストについて、アプリケーションをプッシュします;

<pre class="terminal"> $ cf push -m web-manifest.yml $ cf push -m
worker-manifest.yml </pre>

CFがワーカーのためのURLをきいてくるはずですが、option 2 - "none"を選びます。

## <a id='test'></a>アプリケーションの試験 ##

該当URLのthings controllerのnewアクションを実行します。この例では、URLはhttp://sidekiq.cloudfoundry.com/thing/new になります。

新しいsidekiqジョブが作られ、Redisの待ち行列に入れられます。次に、ワーカー・アプリケーションに取り上げられ、ブラウザが/thingにリダイレクトされ、'things'コレクションが表示されます。ブラウザがリダイレクトされる前に、Sidekiqがタスクを終える可能性があります!

## <a id='test'></a>workersのスケール ##

このアプローチの良い点は、リソースを使わないでおき、Sidekiqワーカーのスケーリングがあまり問題にならなくなることです。

ワーカーの数を2に変更します;

<pre class="terminal">
$ cf scale sidekiq-worker --instances 2
</pre>

