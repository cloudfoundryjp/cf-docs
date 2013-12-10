---
title: Cloud Foundry上でのデータベースのマイグレート
---

Cloud Foundry上でデータベースをマイグレートする方法は、フレームワークによって異なります。このページではRailsアプリケーションのためのデータベースのマイグレーションについて説明します。また、他の環境での一般的なガイドラインについても説明します。




## <a id='migrate-ruby-db'></a>Railsアプリケーションのためのデータベースのマイグレート ##

Railsアプリケーションのデータベースのマイグレートのために、Rakeタスク(`rake db:migrate`)を使うことができます。Cloud Foundry上でRakeタスクを実行するのに、起動コマンドのカスタマイズが利用できます。これにより、アプリケーションの起動の前にマイグレーションを実行できます。

以下にアプリケーションのマニフェストを編集してアプリケーションの実行の前にデータベースをマイグレートするやり方を記載しています。`manifest.yml`については[Application Manifests](/docs/using/deploying-apps/manifest.html)ページの[cf Push and the Manifest](/docs/using/deploying-apps/manifest.html#push-and-manifest)をご覧ください。

複数のインスタンスを実行する場合、各インスタンスごとではなくただ一度だけマイグレーションを実行したいでしょう。以下の節では、インスタンスは複数でもマイグレーションは1回だけ実行されるようにする二つの方法を説明しています。

### <a id='start-scale'></a>インスタンスを一つだけ起動し、後でスケールする ###

マイグレーションを実行する時はインスタンスを一つだけ起動し、その後好きな数のインスタンスを起動するようにアプリケーションをプッシュし直します。

  1. `manifest.yml`でinstance属性を"1"に設定し、`command`でrakeタスク(`rake db:migrate`)をアプリケーションの起動コマンドの前に設定します。

     ~~~
     ---
     applications:
     - name: my-rails-app
       instances: 1
       command: rake db:migrate && rails s
       ... the rest of your settings  ...
    ~~~

  2. アプリケーションをプッシュします。

  3. `manifest.yml`の`instances`属性を望む数に設定し、`command`属性からマイグレーションのタスクを削除し、アプリケーションをプッシュし直します。

### <a id='task'></a>マイグレーションを制限したRakeタスクを使用する ###

望むなら、最初のインスタンスに対してのみマイグレーションを実行するrakeタスクを作ることもできます。Cloud Foundry上で動作するアプリケーションのメタ・データの中に`instance_index`があり、最初に起動されたインスタンスについては"0"になっています。

1. 最初のインスタンスでのみ動くようなrakeタスクを書きましょう。下の例では`VCAP_APPLICATION`環境変数を使って実行を最初のインスタンスに制限しています。そのインスタンスでは`instance_index`の値は"0"です。

  ~~~
  namespace :cf do
     desc "Only run on the primary Cloud Foundry instance"
     task :on_primary_instance do
        instance_index = JSON.parse(ENV["VCAP_APPLICATION"])["instance_index"] rescue nil
       exit(0) unless instance_index == 0
     end
  end
~~~

2. `manifest.yml`内の`command`として最初のインスタンスでのみ`db:migrate`を実行するように設定します。

     ~~~
     ---
     applications:
     - name: my-rails-app
       instances: 5
       command: bundle exec rake cf:on_primary_instance db:migrate && rails s
       ... the rest of your settings  ...
     ~~~

アプリケーションのインスタンスが起動したか確認しましょう。前者のやり方ではマイグレーションが失敗するとアプリケーションのインスタンスは起動されないからです。


## <a id='migrate-node-db'></a>データベースのマイグレーションのガイドライン ##

Cloud Foundry上でのデータベースのマイグレートはフレームワークにより様々です。一般に、以下のうちの一つを実行することになるでしょう:

- 適切なSQL文またはスクリプトを対話型のSQLシェルで実行します。例として、Postgresの`psql`が挙げられます。

- アプリケーションのコードへのアクセスによるコマンドでスキーマをマイグレートします。Railsの`rake db:migrate`タスクはこのやり方の例になっています。あなたがデータベースのマイグレートをアプリケーションの内部から行なうコードを書いたなら、マイグレートしてからスケールするか実行するインスタンスを制限するか、[Use Rake Task to Limit Migration](#task)にあるように環境変数`VCAP_APPLICATION`を使ってマイグレーションを実行します。
 
