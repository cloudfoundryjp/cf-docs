---
title: Cloud Foundry Glindex (用語集 + 索引)
---
このページではCloud
Foundryの文書で使われる用語について簡単に説明しています。いくつかの用語については関連項目へのクロス・リファレンスがついています。

## <a id='application-manifest'></a>アプリケーションのマニフェスト ##

> マニフェストはデプロイの設定を定義します。アプリケーションの名前やインスタンスの数や使用できるメモリ量の上限や使用するサービスなどです。マニフェストの名前のデフォルトはmanifest.ymlです。マニフェストを使えばデプロイを自動化できます。コマンド・ラインでいろいろ設定する必要はありません。<br>
<br>
> 詳しくは[アプリケーションのマニフェスト](../../using/deploying-apps/manifest.html)をご覧ください。

## <a id='autoconfig'></a>自動設定 ##

> Cloud Foundryにおいて、オート・コンフィギュレーションとはバインドされたサービスとの接続を自動的に設定するプロセスのことです。フレームワークに応じてCloud Foundryはサーバーとサービスの接続パラメータを検出し、適切な設定ファイルをそれに応じて更新します。アプリケーションからCloud Foundryのサービスへ接続する際、開発者が設定しなくても、オート・コンフィギュレーションが接続できるようにしてくれます。

> Cloud Foundryにおいて、自動設定とはバインドされたサービスとの接続を自動的に設定するプロセスのことです。フレームワークに応じてCloud Foundryはサーバーとサービスの接続パラメータを検出し、適切な設定ファイルをそれに応じて更新します。アプリケーションからCloud Foundryのサービスへ接続する際、開発者が設定しなくても、自動設定が接続できるようにしてくれます。

## <a id='autoreconfig'></a>自動再設定 ##

 [自動再設定](#auto-config)をご覧ください。

## <a id='bosh-agent'></a>BOSHエージェント ##

> BOSHエージェントはBOSHに管理されているVMで動作しているプロセスで、[BOSHダイレクター](bosh-director)からの指示を待っています。ダイレクターが[job](#job)をエージェントへアサインすると、エージェントは適切なパッケージをblobstoreからダウンロードし、実行し、設定します。エージェントは`monit`を使ってジョブの起動や停止を実行します。

## <a id='bosh-blobstore'></a> BOSH Blobstore ##

> BOSH Blobstoreはオブジェクトに基づいたcloud storage platformで、リリースごとのコンテントを格納します。(ジョブ、パッケージ・ソース、コンパイルされたパッケージなど) BOSHダイレクターは、blobstoreを読み書きするコンポーネントです。

## <a id='bosh-cli'></a>BOSH CLI ##

> [BOSHダイレクター](#bosh-director)と相互作用するコマンドBOSH CLIはreleaseとartifactsの作成と管理を行なうコマンドの集りです。
<br><br>
>詳しくは[BOSHコマンド・ライン・インターフェース](../bosh/reference/bosh-cli.html)をご覧ください。

## <a id='bosh-director'></a>BOSHダイレクター ##

> BOSHダイレクターは、VMの作成、パッケージのコンパイル、Cloud Foundryインスタンスのデプロイ、パッケージの保存、ドロップレットのブロブストアへの保存などを統合的に実行します。


## <a id='bosh-health-monitor'></a>BOSH Health Monitor ##

> ヘルス・ステータスをBOSH Agentから受け取り、メールなどのプラグインを通じて警告を送ります。

## <a id='bosh'></a>BOSH ##

> BOSHは、大規模分散サービスのリリース・エンジニアリングやデプロイやライフサイクル・マネージメントのためのオープン・ソース・ツールです。BOSHはIaaS上でのCloud Foundryや他の分散サービスのデプロイに使えます。IaaSとしてはvSphere, vCloud Director, Amazon Web Services EC2, OpenStackなどが挙げられます。

## <a id='bosh-manifest'></a> BOSH manifest ##

> BOSHのマニフェストはYAMLファイルで、デプロイの設定を定義します。 Cloud Foundry向けには以下を含みます:
<br> <br>

> - 作成するVM

> - VMが使う永続性のディスク

> - VMに割り当てるネットワークとIPアドレス

> - VMに適用するBOSHリリース

> - [ジョブ・テンプレート](#job-template)ごとの、設定ファイルやスクリプトへ適用される独自の設定

## <a id='buildpack'></a>ビルドパック ##

> ビルドパックとは、[ドロップレット](#droplet)を作成するためにアプリケーション[パッケージ](#package)に対して実行されるスクリプトの集りです。アプリケーションの実行に必要なすべてを含みます(このプロセスを[ステージング](#staging)と呼びます)。ビルドパックはフレームワークやランタイムそれぞれに特有です。Cloud FoundryはRuby, Java, Node.js向けのビルドパックを含んでいます; アプリケーションをアップロードすると、Cloud Foundryはアプリケーションを調べて適用するビルドパックを決めます。Cloud Foundryはリモートのビルドパックも使えます; `cf push` コマンドを実行する際、使いたいビルドパックのURLを指定します。
<br><br>
> 詳しくは、[独自のビルドパックの紹介](../../using/deploying-apps/buildpacks.html)をご覧ください。

## <a id='cc'></a>CC ##
> [Cloud Controller](#cloud-controller)をご覧ください。

## <a id='ccng'></a>CCNG ##
> 次世代あるいはv2のCloud Controller[Cloud Controller](#cloud-controller)をご覧ください。

## <a id='cf'></a> cf ##
> cfはCloud Controller用のコマンドです。ユーザがデプロイやアプリケーションの管理ができるように、Cloud Controller REST APIが使われます; 管理とは、プロヴィジョン、サービスとそのバインド、ユーザやオーガナイゼーションやスペースの管理、などです。
<br>
> 詳しくは、[cfコマンド・ライン・インターフェース](../../using/managing-apps/cf/index.html)をご覧ください。

## <a id='cf-release'></a>CF-Release ##
> [CF-Release](https://github.com/cloudfoundry/cf-release)がCloud Foundry用のBOSHリリース・リポジトリですCloud Foundryをデプロイする際、カスタマイズしたマニフェストとCF-Releaseを使うことができます。
<br><br>

> 詳しくは、[最新のCF-Releaseを使う](../../running/deploying-cf/common/cf-release.html)をご覧ください。

## <a id='cloud-controller'></a> Cloud Controller##

> Cloud Controller (CC)はバックエンドの機能を統合するコンポーネントです。それらの機能とは、アプリケーションのステージング、ライフサイクル・マネージメント、プロヴィジョニング、バインドなどです。Cloud Controllerは以下のような機能を持ちます:

<br> <br>

> - データベースのメンテナンス。対象は以下のものに関する情報です。アプリケーション、サービス、オーガナイゼーションやスペースやユーザやロールなどです。

> - 保存されたアプリケーション・パッケージとブロブストア内のドロップレット

> - NATSを通じて、他のコンポーネントとの相互作用。他のコンポーネントとは、DEA、サービス・ゲートウェイ、ヘルス・マネージャーを含みます。

> - ユーザがバックエンドの機能にアクセスするためのREST API

## <a id='cpi'></a> Cloud Provider Interface ##

> Cloud Provider Interface (CPI)はAPIで、BOSHがIaaSと相互に作用してVMやテンプレートを管理するのに使われます。以下のもの向けのCPIがあります。vSphere, OpenStack, Amazon Web ServicesCPIは仮想環境を抽象化し、アプリケーションのデプロイと実行を複数のクラウドにまたがって実行する基盤となります。

## <a id='cloud-provider-interface'></a>CPI ##

> [Cloud Provider Interface](#cpi)をご覧ください。

## <a id='droplet-execution-agent'></a>DEA ##

> [Droplet Execution Agent](#dea)をご覧ください。

## <a id='dea'></a> Droplet Execution Agent ##

> Droplet Execution Agent (DEA)はVM上でアプリケーションを実行します。Cloud Controllerはドロップレットを実行する際にメッセージを発行し、DEAがサブスクライブします。DEAの環境がドロップレットのランタイムと必要なメモリ量に合っている場合、DEAはCloud Controllerのリクエストへ応答し、ドロップレットを受け取り、起動します。同様に、Cloud Controllerからリクエストがあれば、DEAはアプリケーションを停止します。DEAは自分が起動したインスタンスを監視し、状態を定期的に[NATS](#nats)を通じてブロードキャストします。
<br><br>

> 詳しくは、[Droplet Execution Agent](../architecture/execution-agent.html)をご覧ください。

## <a id='droplet'></a> droplet ##

> ドロップレットはアプリケーションの[staging](#staging)プロセスの結果で、ビルドパックが適用されるべきアップロードされたアプリケーションです。元のアプリケーションにラッパーをかぶせたもので、HTTPリクエストを待つべきポートを入力として受け取ります。また二つのメソッドstartとstopを持ちます。

## <a id=' '></a>flapping ##

> 繰り返しクラッシュするか、あるいあは起動しないアプリケーションについて、Cloud FoundryはFlappingというヘルス・ステータスを報告します。

## <a id='health-manager'></a>Health Manager ##

> Health Managerはデーモンの一つで、定期的にCloud Controllerデータベースをスキャンし、デプロイされたアプリケーションの状態とその上で実行されるべきVMを調べます。Health Managerはあるべき状態と実際の状態を比較し、問題を見つけるとCloud Controllerへメッサージを送ります。
<br> <br>
>詳しくは[Health Manager](health-manager.html)をご覧ください。

## <a id='health-monitor'></a>Health Monitor##

> [BOSH Health Monitor](#bosh-health-monitor)をご覧ください。

## <a id='job'></a>job ##

> BOSHにおいて、ジョブとはパッケージの起動と実行のためのデプロイと実行のルールと資源の集合です。ジョブはマニフェストの中で定義されます。 (BOSH用とCloud Foundry 自体用の両方で) ジョブは、明示的あるいは参照 [resourcepool](#resource-pool)によって定義されます:

<br> <br>

> - ネットワークの設定

> - ジョブのテンプレート

> - デプロイするインスタンスの数

> - 資源の割当て(メモリ、ディスク、CPU)

> ジョブはロールからも参照されます。


## <a id='job-spec'></a>job specification ##

> ジョブの定義はYAMLファイルで、[ジョブ](#job)のテンプレート・ファイルやパッケージの依存関係やプロパティを記述しています。

## <a id='job-template'></a>ジョブのテンプレート ##

> [job](#job)のための、汎用的な設定ファイルとスクリプトの集りテンプレートが適用される際、ジョブはRuby ERBのテンプレートを使って最終的な設定ファイルとクリプトを生成します。BOSH CLIを使ってジョブのテンプレートを生成できます。

<br><br>

>設定ファイルがテンプレートへ変えられる際、インスタンス特有の情報は抽象化され、VM上でジョブが起動される時に与えられます。たとえば、WWWサーバが使うポートや、データベースのユーザ名とパスワードなどです。


## <a id='manifest'></a>manifest ##
> [application manifest](#application-manifest)と[BOSH manifest](#bosh-manifest)をご覧ください。

## <a id='micro-bosh'></a>Micro BOSH ##
> Micro BOSHはBOSHのコンポーネントすべてを含むVMです。MicroBOSHはBOSHをインストールするために使われます。
<br><br>

> 詳しくは、[Deploying BOSH with Micro BOSH](../../running/deploying-cf/vsphere/deploying_bosh_with_micro_bosh.html)をご覧ください。


## <a id='nats'></a>NATS ##
> NATSは分散メッセージングのシステムです。Cloud Foundryのコンポーネント間の通信にはNATSが使われています。


## <a id='org'></a>オーガナイゼーション ##
> Cloud
Foundryでは、オーガナイゼーションはユーザの集りで、関連するアプリケーションとサービスのために使われます。ユーザはオーガナイゼーションに応じて、資源に対してさまざまな権限を持ちます。オーガナイゼーションは[スペース](#space)を含みます。<br><br>

>
詳しくは[オーガナイゼーションとスペース](../../using/managing-apps/orgs-and-spaces.html)をご覧ください。


## <a id='package'></a>パッケージ ##
> BOSHでは、パッケージはコンパイルとインストールのためのソース・コードとスクリプトの集りです。必要に応じて、パッケージはデプロイの際にコンパイルされます。ダイレクターはテンプレート向けにデプロイされるパッケージのコンパイル済のヴァージョンがあるかチェックします。存在しなければ、同じテンプレートを使ってコンパイル用VMを起動します。これにより、パッケージのソースがブロブストアから取り出され、コンパイルされ、パッケージのバイナリが作られてブロブストアに格納されます。ソース・コードをバイナリへ変換するには、コンパイルを担当するパッケージング・スクリプトが各パッケージに存在し、コンパイルVM上で実行されます。

## <a id='package-spec'></a>package spec ##

> このファイルは、パッケージの名前、依存するパッケージ、含まれるファイルを定義しています。


## <a id='release'></a> release ##

> BOSHにおいて、リリースはテンプレートから作られたVMへインストールされるソフトウェアと設定ファイルのひな形の集りです。


## <a id='resource pool'></a>リソース・プール ##

> BOSHのマニフェストでは、リソース・プールが作成されるVMの特性を定義します。これらのVMへは、一つまたは複数の[jobs](#job)をアサインすることができます。アトリビュートはリソース・プールを定義します。これには、作成されるVMの数、VMのテンプレート、CPUの数、メモリの量、各VMのディスク容量、などが含まれます。
<br><br>

> リソース・プールの内容、マニフェスト内のジョブがどのように割当てられるかなどについては、[Cloud Foundryのマニフェストの例](../../running/deploying-cf/vsphere/cloud-foundry-example-manifest.html)をご覧ください。

## <a id='router'></a>Router ##

> ルータはCloud Foundryへのトラフィックを適切なコンポーネントへ届くようにします。 -- たいていは Cloud ControllerかDEA上のアプリケーションが行き先です。ルータはGo言語で記述されています。ルータはDEAからのアプリケーションがオンラインになったまたはオフラインになったというメッセージを受信し、メモリ上のルーティング・テーブルを更新します。外部からのリクエストは、ルータのプール内で負荷分散されます。
<br><br>

> 詳しくは、[Router](router.html)をご覧ください。


## <a id='space'></a> Space ##

> Cloud Foundryにおいて、スペースは[オーガナイゼーション](#org)内のアプリケーションとサービスのグループです。例として、いろいろなOSのホーム・ディレクトリに相当する個人用スペースや "Development", "Staging", "Production"のような共有スペースを含みえます。あるオーガナイゼーションにアクセスするには、ユーザは特定の権限を必要とします。
<br><br>
> 詳しくは、[オーガナイゼーションとスペース](../../using/managing-apps/orgs-and-spaces.html)をご覧ください。

## <a id='staging'></a>ステージング ##

> ステージングとは、アップロードされたアプリケーションとCloud Foundryかユーザが指定したビルドパックを使ってDEAが実行するプロセスを意味します。ステージングの結果が[ドロップレット](#droplet)です。


## <a id='stemcell'></a>stemcell##

> stemcellはLinuxやBOSHエージェント向けのVMのテンプレートです。BOSHははstemcellをCloud Foundry [リリース](#release)がデプロイされるVM群をクローンするために使います。

## <a id='steno'></a>Steno ##

> Cloud Foundryをサポートするために作られた、軽量のロギング・ライブラリです。


## <a id='uaa'></a> UAA ##

> [User Account and Authentication Service](#uaa)をご覧ください。


## <a id='uaa'></a>User Account and Authentication Service (UAA)  ##

> Cloud Foundryにおいて、User Account and Authentication Service (UAA)はアプリケーションとCloud Foundryの資源に関するシングル・サインオンの機能を提供します。UAAはOAuth 2.0認証サーバとして動作します。クライアント・アプリケーションに対して、Cloud Controllerを含むリソース・サーバへのアクセス・トークンを与えます。
<br><br>

> 詳しくは[User Account and Authentication Service](../architecture/uaa.html)をご覧ください。

## <a id='vcap-services'></a>VCAP_SERVICES##

> あるアプリケーションにバインドされているサービスの接続情報が格納されている環境変数
<br><br>
> 詳しくは[VCAP_SERVICES Environment Variable](../../using/services/environment-variable.html)をご覧ください。


## <a id='vmc'></a>VMC ##
> VMCはCloud Foundry v1用のコマンドです。[cf](#cf)はCloud Foundry v2用のコマンドです。


## <a id='warden'></a>Warden ##

> Wardenは、Cloud Foundryで用いられている、Unix上で隔離された環境の作成や管理のフレームワークです。WardenはVM内にコンテナを作成し管理するためのAPIとコマンドを提供します。Wardenはコンテナの使うCPUやメモリ量やディスク容量を制限することができます。


## <a id='yaml'></a> YAML ##

> YAMLはアプリケーションのマニフェストやBOSHのマニフェストで使われているフォーマットです。YAMLの文法については、[www.yaml.org](www.yaml.org)をご覧ください。


## <a id='yml'></a>yml ##

> YAMLファイルに使われる拡張子。[YAML](#yaml)をご覧ください。




