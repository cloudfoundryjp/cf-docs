---
title: Cloud Foundry上でのデータベースのマイグレート
---

このページでは、cf v5の使用が前提になっています。

マイグレーションはデータベースのスキーマを変更するための構造化・組織化された手段です。このページではデータベースのマイグレーションの一般的なガイドラインとRailsアプリケーション向けの詳細な推奨について説明しています。

## <a id='general_guidelines'></a>一般的なデータベースのマイグレーションのガイドライン

Cloud Foundryにおいてデータベースをマイグレートする方法は、アプリケーションのフレームワークに依存します。Cloud Foundryはデプロイしたアプレイケーションに対してシェルのコマンドやRakeタスクを直接実行する仕組みを提供しませんが、以下のようなアプローチが使用可能です。

- 適切なSQLを実行するか、Postgresのためのpsqlのような対話型のシェルでスクリプトを実行してデータベースをマイグレートします。`grep`を使ってアプリケーションのenv.logから`DATABASE_URL` (データベースへの接続情報)を検索し、スキーマへ接続しマイグレートし、その後`cf-push`でアプリケーションを更新できます。

- デプロイしたアプリケーションについて、Cloud Foundryの`manifest.yml`ファイルで起動コマンドを指定してシェルのコマンドやRakeのタスクを実行してデータベースをマイグレートします。単純に`manifest.yml`にコマンドを追加すると、アプリケーションのすべてのインスタンスについてそれが実行されてしまうことに注意してください。最初のインスタンスについてのみ実行するには、以下を使うことができます:
    - インスタンス一つだけを起動し、その後スケールする。または、
    - 環境変数`VCAP_APPLICATION`を使ってコードを実行するインスタンスの数を制限する。

より一般的な場合として、Railsの`rake db:migrate task`を使う一例として以下の方法があります。

## <a id='migrate_rails'></a>Railsアプリケーションのデータベースのマイグレート ##

Railsについて、通常はRakeタスク`rake db:migrate`を使ってデータベースをマイグレートします。同じタスクをアプリケーションのマニフェスト`manifest.yml`ファイルへ起動コマンドを以下のように追加して実行することもできます。

~~~
---
applications:
- name: my-application
  command: "bundle exec rake db:migrate && bundle exec rails -p $PORT"
  ... the rest of your settings ...
~~~

しかし、この方法はすべてのインスタンス毎に`db:migrate`を実行してしまうことに注意してください。インスタンス一つだけを起動してマイグレートを実行してからスケールするか、カスタムRakeタスクを作ることにより一度だけマイグレートを実行することができます。

### <a id='single_and_scale'></a>一つのインスタンスでマイグレートし、その後スケールする ###

1. アプリケーションの`manifest.yml`を編集し、instancesを1に設定し、起動コマンドとして`command: "bundle exec rake db:migrate && bundle exec rails -p $PORT"`を設定します。

~~~
---
applications:
- name: my-application
  instances: 1
  command: "bundle exec rake db:migrate && bundle exec rails -p $PORT"
  ... the rest of your settings ...
~~~

2. `cf push`を実行してアプリケーションをプッシュします。
3. `manifest.yml`を編集し、instancesの値を望む数に変更し、カスタム起動コマンドを削除します。
3. `cf push --reset`を実行してアプリケーションを変更後の`manifest.yml`を使ってデプロイしなおします。

### <a id='create_custom_task'></a>カスタムRakeタスクを作って実行する ###

Cloud Foundryはデプロイされたアプリケーションの各インスタンスのメタデータを環境変数`VCAP_APPLICATION`として提供しています。この環境変数は、インスタンスごとにユニークな数を`instance_index`として含みます。最初のインスタンスの`instance_index`の値は0になります。

1. 上の情報にもとづき、環境変数`VCAP_APPLICATION`から`instance_index`の値を取り出し、0の時のみ実行するRakeタスクを作ります。0でなければタスクを終了します。

~~~
namespace :cf do
  desc "Only run on the first application instance"
  task :on_first_instance do
    instance_index = JSON.parse(ENV["VCAP_APPLICATION"])["instance_index"] rescue nil
    exit(0) unless instance_index == 0
  end
end
~~~

2. `manifest.yml`ファイルを編集し、このタスクをカスタム起動コマンドとして定義します:

~~~
---
applications:
- name: my-rails-app
  instances: 5
  command: bundle exec rake cf:on_first_instance db:migrate && bundle exec rails -p $PORT"
  ... the rest of your settings ...
~~~

3. `cf push`を実行し、アプリケーションをデプロイします。


