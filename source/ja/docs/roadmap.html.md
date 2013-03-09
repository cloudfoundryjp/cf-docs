---
title: Cloud Foundry ロードマップ
---

<!--
Updated in early Feb, 2013.
-->

2013年2月初旬 更新

<!--
Post questions about our roadmap [here](https://groups.google.com/a/cloudfoundry.org/forum/?fromgroups#!forum/vcap-dev).
-->

ロードマップに関する質問は[こちら](https://groups.google.com/a/cloudfoundry.org/forum/?fromgroups#!forum/vcap-dev)

<!--
## February 2013
-->

## 2013年2月

<!--
* Preview of Team Edition with [Micro Cloud Foundry](http://cloudfoundry.github.com/docs/running/micro_cloud_foundry/).
* Move to [GitHub Pull Requests](https://groups.google.com/a/cloudfoundry.org/d/msg/vcap-dev/61ziGuPATDs/iD_dz96lwIcJ) for all repositories, stop using Gerrit
* Preview of new consolidated documentation (You're looking at it!)
-->

* [Micro Cloud Foundry](http://cloudfoundry.github.com/docs/running/micro_cloud_foundry/)によるTeam Editionのプレビュー
* Gerritをやめ、すべてのリポジトリを[GitHub Pull Requests](https://groups.google.com/a/cloudfoundry.org/d/msg/vcap-dev/61ziGuPATDs/iD_dz96lwIcJ)に移動
* 新しく整理されたドキュメント(今見ているドキュメントです!)

<!--
## April 2013
-->

## 2013年4月

<!--
### Team Edition production release
-->

### チームエディションの正式版リリース

<!--
#### Updated [Router](https://github.com/cloudfoundry/gorouter)
-->
#### [Router](https://github.com/cloudfoundry/gorouter) のアップデート

<!--
* Implemented in Go, no longer using nginx
* Load tested with 4x better performance
* WebSocket support
-->

* Nginxの廃止とGoによる実装
* 4倍のパフォーマンス向上
* WebSocketのサポート

<!--
#### Updated [Cloud Controller](https://github.com/cloudfoundry/cloud_controller_ng)
-->

#### [Cloud Controller](https://github.com/cloudfoundry/cloud_controller_ng) のアップデート

<!--
supporting the Cloud Foundry API 2.0
* Self-organizing teams with Organizations and Spaces
* Team collaboration support for apps and services
* Custom domain management
* User management and permissions
* Add support for MySQL for the Cloud Controller database
* Blob storage now supports local directories or s3
* Quota enforcement - operators enforce limits on resource consumption
  * number of apps
  * amount of memory
  * number and type of services
* Usage events - useful for auditing and billing use cases
-->

Cloud Foundry API 2.0 をサポートします。
* 組織とスペースによるセルフ管理のチーム機能を提供します
* アプリケーションとサービスのチームコラボレーションサポートします
* カスタムドメイン管理ができます
* ユーザー管理と許可設定ができます
* Cloud Controller データベースでの MySQL サポートが追加されます
* Blob ストレージがローカルディレクトリとS3をサポートします
* クオータ管理 - 管理者がリソースの制限と消費量の管理できます
  * アプリケーション数
  * 総メモリ量
  * サービスの数と種類
* 利用イベントが追加されます - 監査と課金のユースケースに利用可能です

<!--
#### Updated [DEA](https://github.com/cloudfoundry/dea_ng)
-->
#### [DEA](https://github.com/cloudfoundry/dea_ng) のアップデート

<!--
* New support for Heroku-inspired buildpacks
  * Easier to bring languages and containers to the platform
  * Self-contained buildpacks do not require code changes in multiple Cloud Foundry components
  * Builds on the MIT licensed Heroku buildpack ecosystem
  * Use the curated buildpacks, fork and make changes, or build your own
* Improved application isolation with Linux containers
* Staging happens on DEA’s instead of the separate Stager component
  * Environment variable changes do not require restaging
-->

* Herokuをヒントにしたビルドパックをサポートします
  * プラットフォームに言語とコンテナを持ち込むことがより簡単になりました
  * 自己内包するビルドパックは複数のCloud Foundryコンポーネントのコードの変更が不要になりました
  * MITライセンスのHerokuビルドパックエコシステムの上に構築します
  * 管理されたビルドパックとフォーク、変更を加えた、独自のビルドパックを利用可能
* Linuxコンテナによるアプリケーションの独立性の向上しました
* ステージングが独立したStagerコンポーネントではなくDEAで発生するようになります
  * 環境変数の変更によるステージングの再実行が不要になります

<!--
#### Updated [Services](https://github.com/cloudfoundry/vcap-services/tree/master/ng)
-->
#### [Services](https://github.com/cloudfoundry/vcap-services/tree/master/ng) のアップデート

<!--
* Updated versions
  * MongoDB 2.2
  * MySQL 5.5
  * Postgres 9.1
  * RabbitMQ 2.8
  * Redis 2.6
* New large capacity services
* New service instance isolation options
  * Run service instances in Linux containers on the same instance for high-density and security
  * Run instances in separate VMs for maximum isolation
* Snapshots - user managed snapshots, restore, download,
-->
* バージョンがアップデートされます
  * MongoDB 2.2
  * MySQL 5.5
  * Postgres 9.1
  * RabbitMQ 2.8
  * Redis 2.6
* 大容量サービスが追加されます
* サービスの独立性オプションが追加されます
  * 同一インスタンス上の複数のLinuxコンテナでサービスインスタンスを動かすことで、密宗度をあげ、セキュリティを確保します
  * 別々の仮想マシンでインスタンスを動かすことが最大の隔離方法です

<!--
#### Updated [Health Manager](https://github.com/cloudfoundry/health_manager)
-->

#### [Health Manager](https://github.com/cloudfoundry/health_manager) のアップデート

<!--
*  Significantly improved performance
-->

* パフォーマンスが大きく向上します

<!--
#### Team Edition Web Portal (Enterprise Offering)
-->

#### チームエディションウェブポータル (エンタープライズオファリング)

<!--
* Web-based user interface for Cloud Foundry
* Manage users, applications, services, domains
* Review and manage usage
-->

* Cloud Foundry向けウェブベースのユーザーインターフェース
* ユーザー、アプリケーション、サービス、ドメインの管理
* 利用状況のレビューと管理

<!--
#### Updated [User Account and Authentication](https://github.com/cloudfoundry/uaa) Service (UAA)
-->

#### [User Account and Authentication](https://github.com/cloudfoundry/uaa) サービス (UAA)

<!--
* Simplified bootstrapping
* User managed approvals and revocations
-->

* 起動処理が簡略化されます
* ユーザーによって承認と取り消しが管理されます

<!--
#### Amazon Web Services ease-of-use
-->

#### Amazon Web Service の簡単な利用

* デプロイが簡素化されます
  * いくつかの質問に答え、AWSの資格情報を与えることで複数ノードのデプロイが可能です
* BOSH が DHCP をサポートします
* BOSH が AWS VPC ネットワークをサポートします

<!--
* Simplified deployment
  * Answer a few questions, supply your AWS credentials, get a full multi-node deployment
* BOSH support for DHCP
* BOSH support for AWS VPC networks
-->