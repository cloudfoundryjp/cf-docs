---
title: Rails 3とサービスのバインド
---

## <a id='intro'></a>紹介 ##

本ガイドは、次に述べるようなアプリケーションの開発者向けです。ここでのアプリケーションはORMタイプのデータ・リソースと結合し、Cloud Foundry上へデプロイされ、実行されます。他の種類のデータ・リソースとRailsやRubyベースのアプリケーションとのバインドについては、Ruby [Service Bindings](./ruby-service-bindings.html)ページをご覧ください。

## <a id='prerequisites'></a>前提 ##

以下の前提を満たす必要があります;

* Cloud Foundryのアカウント。 右のページでサインアップできます。[サインアップ](https://my.cloudfoundry.com/signup
* [Ruby](http://www.ruby-lang.org/en/)
* [Rails](http://rubyonrails.org/)
* [Bundler](http://gembundler.com/)
* The [CF](../../managing-apps/index.html) コマンド・ライン・ツール
* サンプル・アプリケーションは [this](./rails-getting-started.html)にあります。

## <a id='gemfile'></a>Gemfileの設定 ##

どのサービスを使うかにより、適切なgemが含まれていてバンドルが更新されていることを確認する必要があります。'Gemfile'を編集し、以下のgemがproduction environmentで使えることを確認してください;

<pre>
Service Type      Gem

MySQL             mysql2
PostgreSQL        pg
MongoDB           mongo_mapper

</pre>

以下のgemを追加し、bundleを更新してください。

<pre class="terminal">
$ bundle update
</pre>

## <a id='modifying'></a>サンプル・アプリケーションの変更 ##

MySQLとPostgresのどちらとでも動作するActiveRecordの場合 (ただし、production environment向けのconfig/database.ymlで適切なアダプタが設定され、適切なサービスがバインドされている必要があります)

MySQL向け;

~~~yaml
production:
  adapter: mysql2
  encoding: utf8
~~~

Postgres向け;

~~~yaml
production:
  adapter: postgresql
  encoding: unicode
~~~

MongoDB向けに、いくつかのgemが存在します。ActiveRecordのようなORMを探しているのなら、[Mongo Mapper](http://mongomapper.com/)が最適でしょう。アプリケーションの変更が必要で、詳しくはMongo DBのWWWサイトの "[Rails 3 - Getting Started]"ページをご覧ください。

両方向け

## <a id='creating-and-binding'></a>Creating and binding the service ##

サービスを作るには、以下のようにcfコマンドを実行し、対話的に質問に答えます;

<pre class="terminal">
$ cf create-service
</pre>

サービスとアプリケーションをバインドするためには、以下のcfコマンドを実行してください;

<pre class="terminal">
$ cf bind-service --app [application name] --service [service name]
</pre>

