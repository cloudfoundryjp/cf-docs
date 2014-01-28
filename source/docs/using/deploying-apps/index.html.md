---
title: アプリケーションのデプロイについての重要事項
---


このページにはアプリケーションのデプロイに関するCloud Foundryの振舞いと仕様についてまとめてあります。

## <a id='push-process'></a>Cloud Foundryのプッシュのプロセス ##

Cloud Foundry CLI `cf push`でアプリケーションのデプロイを実行できます。Cloud Foundryのドキュメントでは、デプロイのプロセスを「アプリケーションのプッシュ」と呼ぶことがよくあります。

アプリケーションをプッシュする時、Cloud Foundryはさまざまなタスクを実行します。アプリケーションを実行するためのコンテナを見つけ、適切なソフトウェアとリソースを備えたコンテナを用意し、一つまたはそれ以上のアプリケーションのインスタンスを起動し、アプリケーションの状態をCloud Foundryデータベースに保存する、などです。

この流れは[アプリケーションはどのように公開されるか](/docs/running/architecture/how-applications-are-staged.html)に図示されています。

## <a id='exclude'></a>プッシュの時に不要なファイルを除外する ##

デフォルトでは、アプリケーションをプッシュする時、アプリケーションのプロジェクトのディレクトリにあるすべてのファイルがCloud Foundryインスタンスへアップロードされます。ただし、ヴァージョン管理に用いられる`.svn`, `.git`, `.darcs`という拡張子を持つファイルは除きます。アプリケーションのディレクトリに実行やビルドに不要なファイルがある場合、`.cfignore` ファイルを使って除外するのがベスト・プラクティスです。(`.cfignoreは、gitで管理されないようにする`.gitignore`に似ています)特に大きなアプリケーションでは、不要なファイルのアップロードはデプロイを遅くします。

アプリケーションのディレクトリのルートにある`.cfignore`というテキスト・ファイルでファイルやファイルのタイプを指定してください。たとえば、以下の行は`tmp`と`log`ディレクトリを除外します。

<pre class="terminal">
tmp/
log/
</pre>

使用するアプリケーションのタイプにより、除外するファイルのタイプは異なります。一般的なフレームワークについての例は https://github.com/github/gitignore にあります。これらはよい出発点になるでしょう。

##<a id='instances'></a>可用性のために複数のインスタンスを実行する ##

Cloud Foundryがアップグレードされている間にアプリケーションが使えないというリスクを避けるために、二つ以上のインスタンスを実行すべきです。DEAがアップグレードされる際、アプリケーションは_退避_させられます: 正常に停止させられ、別のDEA上で再起動されます。PivotalのCFでは、BOSHはDEAのアップグレードを一つずつ実行する設定になっています。したがって、起動にかかる時間が2分未満なら、アプリケーションのインスタンスは二つあれば十分です。Cloud Foundryは、起動に2分以上かかるアプリケーションについて、二つ以上のインスタンスを二つ以上のインスタンスの実行を推奨しています。


##<a id='buildpacks'></a>Buildpack Support for Runtimes and Frameworks ##

Cloud Foundryはフレームワークとランタイムごとのビルドパックを使ってアプリケーションを公開します。Herokuがビルドパックを使ったアプローチを開発し、OSSコミュニティがそれを使えるように公開しました。Cloud Foundryはさまざまなフレームワークやランタイムのためのビルドパックを提供しています。ランタイムに応じたデプロイの方法については、以下のリンクをご覧ください。

| Runtime        | Framework                                                                             |
| :------------- | :-------------                                                                        |
| Javascript     | [Node.js](/docs/using/deploying-apps/javascript/index.html)                           |
| Java / JVM     | [Java Spring, Grails, Scala Lift, and Play](/docs/using/deploying-apps/jvm/index.html)|
| Ruby           | [Rack, Rails, or Sinatra](/docs/using/deploying-apps/ruby/index.html)                 |


Cloud Foundryはカスタム・ビルドパックもサポートしています。 [カスタム・ビルドパック](/docs/using/deploying-apps/buildpacks.html)に説明があります。<a href="https://devcenter.heroku.com/articles/third-party-buildpacks">Herokuサード・パーティ・ビルドパック</a>にあるうちのいくつかもCloud Foundryで動作するでしょうが、結果は様々です。コミュニティが開発したビルドパックについては、https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks をご覧ください。Cloud Foundryに組込まれていないビルドパックを使うには、アプリケーションをプッシュする時に`--buildpack`オプションを使ってビルドパックのURLを指定します。

`cf push`でビルドパックを指定しなければ、Cloud Foundryは各ビルドパックの`bin/detect`スクリプトを使ってどのビルドパックを使うか決定します。

##<a id='deploy-options'></a>アプリケーションのデプロイのオプション ##

アプリケーションがどのようにデプロイされるかは、必須あるいはオプションのデプロイの属性によって制御されます。たとえば、アプリケーションの名前、実行するインスタンスの数、割当てるメモリの量などを指定します。`cf push`コマンドを実行する際にオプションで指定するか、アプリケーションのマニフェスト・ファイルで指定できます。

* `push`コマンドとオプションの詳細については、 [push](/docs/using/managing-apps/cf/index.html#push)セクションをご覧ください。
* アプリケーションのマニフェスト・ファイルでのオプションの指定については [cf Push and the Manifest](/docs/using/deploying-apps/manifest.html#push-and-manifest) セクションをご覧ください。


Pivotal CFへアプリケーションをプッシュする際のその他の情報については [Getting Started](/docs/dotcom/getting-started.html)をご覧ください。

##<a id='start-command'></a>アプリケーションの起動コマンド ##

アプリケーションを起動するコマンドをCloud Foundryが知る方法は三つあります。以下に優先順に記載します。

1. `--command`オプション(またはアプリケーションの`manifest.yml`ファイル)たとえば`cf push --command '.java/bin/java myapp'`
1. 存在するなら、アプリケーションのprocfile内の`web`キー。procfileは`Procfile`という名前のテキスト・ファイルで、アプリケーションのルート・ディレクトリに置かれます。この中にプロセスのタイプと起動コマンドが記載されます。たとえば `web: YourStartCommand`
1. ビルドパックの`bin/release` スクリプトの出力の`default_process_types`セクションの中のweb”プロセス・タイプの起動コマンド(指定されていれば)

##<a id='run-utility'></a>デプロイの時にユーティリティを実行する ##
アプリケーションをプッシュした時に、関連するユーティリティを実行することができます。
たとえば、データベースの初期化とマイグレーションが挙げられます。Cloud Foundryでデータベースを使うアプリケーションを実行するなら、データベースを作成しデータを格納する必要があります。同様に、データベースのスキーマが変更された時、データベースを更新するかマイグレートする必要があります。

###<a id='custom-start'></a>カスタム起動コマンドを指定する ###


Cloud Foundry上でスクリプトまたはユーティリティを呼び出すメカニズムは、アプリケーションを起動するコマンドをカスタマイズすることで実現されます。二つのやり方があります。

* `cf push`の実行時に`--command`オプションで指定する。

* アプリケーションのデプロイのマニフェスト`manifest.yml`で指定する。

###<a id='revert-start'></a>通常の起動コマンドへもどす ###

アプリケーションをプッシュする度にカスタム起動コマンドを指定したいのでないかぎり、デフォルトまたは以前に指定した起動コマンドへもどす必要があります。このためには、ユーティリティが実行されてアプリケーションが起動した後に:

* コマンド・ラインから指定したのなら、`--reset`オプションを指定してプッシュし直します。これによりCloud Foundryにマニフェストでの指定を使うよう指示することになります。(さもなければ、次にプッシュする際にも以前プッシュした時に指定したオプションを使うことになります)

* マニフェストで指定した場合は、マニフェストからカスタム・コマンドを削除し、プッシュし直します。

###<a id='single-app'></a>一つのインスタンスで起動コマンドを実行する ###

カスタム起動コマンドを使う時にもう一つ考慮することがあります: アプリケーションのプッシュ時に複数のインスタンスを起動する場合、カスタム・コマンドは各インスタンス毎に実行されます---これはデータベースの作成やマイグレーションにとってふさわしくありません。一つのインスタンスのみでデータベースをマイグレートする例として[Migrate a Database on Cloud Foundry](/docs/using/deploying-apps/migrate-db.html)をご覧ください。

## <a id='logging'></a>Viewing Application Logs ##

アプリケーションを`cf logs`で監視できます。詳しくは[こちら](./logging.html)をご覧ください。

## <a id='services'></a>サービスを利用する ##

Cloud Foundry上で実行されるアプリケーションのインスタンスはサービスを利用できます。こういったサービスはCloud Foundryのインスタンスによりさまざまです。

Pivotal CFのインスタンスは多くのサービスを用意しています。この中には種々のデータベース、メール、Redisなどが含まれています。Pivotal CFで利用できるサービスについては[Services Marketplace](/docs/dotcom/marketplace/services/index.html)をご覧ください。

用意されているサービスを利用するには、サービスのインスタンスを作る必要があります。Pivotal CFでの手順について、 [Getting Started with Services](/docs/dotcom/adding-a-service.html)をご覧ください。サービスの種類によっては、それに接続するアプリケーションで設定が必要になることがあります。

フレームワーク特有のサービスについては以下をご覧ください:

* [Service Bindings for Spring Applications](/docs/using/services/spring-service-bindings.html)
* [Service Bindings for Grails Applications]((/docs/using/services/grails-service-bindings.html)
* [Service Bindings for Lift Applications](/docs/using/services/lift-service-bindings.html)
* [Service Bindings for Rack, Rails, or Sinatra Applications](/docs/using/services/ruby-service-bindings.html)
* [Service Bindings for Node.js Applications](/docs/using/services/node-service-bindings.html)

Cloud Foundryから利用可能な外部のサービスについては[Services Architecture](/docs/running/architecture/services/index.html)をご覧ください。

