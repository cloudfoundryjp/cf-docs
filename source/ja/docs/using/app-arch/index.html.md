---
title: アプリケーションアーキテクチャーの考慮
---

<!--
Applications written using the runtimes and frameworks supported by Cloud Foundry will often run unmodified on Cloud Foundry, provided the design and architecture of an application follows a few simple guidelines. Following these guidelines will make an application cloud-friendly, and ease the deployment of an application to Cloud Foundry and other cloud platforms.
-->

Cloud Foundryがサポートするランタイムおよびフレームワークを使うように記述されたアプリケーションは、アプリケーションのデザインとアーキテクチャーについて、いくつかのガイドラインに従うことで、変更なしで CloudFoundry上で動くようになります。以下のガイドラインはアプリケーションをクラウドフレンドリーにし、Cloud Foundryや、他のクラウドプラットフォームにデプロイしやすくするためのものです。

<!--
* [Local File System Storage](#filesystem)
* [HTTP Sessions](#sessions)
* [Ports](#ports)
* [SMTP](#smtp)
-->

* [ローカルファイルシステム](#filesystem)
* [HTTPセッション](#sessions)
* [ポート](#ports)
* [SMTP](#smtp)

## <a id="filesystem"></a>ローカルファイルシステム ##

<!--
Applications running on Cloud Foundry should avoid writing files to the local file system. There are a few reasons for this.
-->

Cloud Foundry 上で動作するアプリケーションはローカルファイルシステムにファイルを書き出すことを避けるべきです。これにはいくつかの理由があります。

<!--
First, local file system storage is ephemeral - it is not guaranteed to persist for the entire life of the application. When an application instance crashes or is stopped, the resources assigned to that instance are reclaimed by the platform. When the instance is re-started, the application could be running on a different virtual machine or physical machine. This means that, although your application is able to write local files while it is running, the files will disappear after the application restarts.
-->

まず、ローカルファイルシステムは一時的なものです。アプリケーションの一生に渡って永続化されることを保証しません。アプリケーションがクラッシュしたり停止したりすると、そのインスタンスに割り当てられたリソースはプラットフォームによって回収されます。インスタンスがリスタートされた場合は異なる仮想マシンもしくは物理マシンで実行されるでしょう。これは、アプリケーションはローカルファイルに書き込みを行うことはできるけれども、アプリケーションがリスタートされたらそれらのファイルがなくなることを意味します。

<!--
Second, instances of the same application do not share a local file system. The application instances could be running in different DEA nodes, in different virtual machines, and on different physical machines. Because of this, a file written by one instance of an application is not visible to other instances of the same application.
-->

次に、同じアプリケーションのインスタンス同士はファイルシステムを共有していません。アプリケーションのインスタンスはそれぞれ異なるDEAノード(異なる仮想マシンや異なる物理マシン)上で動作する場合があります。このため、あるアプリケーションのあるインスタンスで書き込まれたファイルは、同じアプリケーションの別のインスタンスからは見えません。

<!--
If the files your application is writing are temporary, then this should not be a problem. However, if your application needs the data in the files to persist across application restarts, or the data needs to be shared across all running instances of the application, then the local file system should not be used.
-->

もし、アプリケーションが書き込むファイルが一時的なものであれば、これは問題にならないでしょう。しかし、もしアプリケーションが、再起動をしても失われない永続化されたファイルによるデータや、インスタンス間で共有されるデータを必要とするならば、ローカルのファイルシステムを使うべきではありません。

<!--
There are a few alternatives to using the local file system. One option is to use a Cloud Foundry services such as the MongoDB document database or a supported relational database (MySQL or vFabric Postgres). Another option is to use cloud storage providers such as [Amazon S3](http://aws.amazon.com/s3/), [Google Cloud Storage](https://cloud.google.com/products/cloud-storage), [Dropbox](https://www.dropbox.com/developers), or [Box](http://developers.box.com/). If your application needs to communicate across different instances of itself (for example to share state etc), consider the use of a cache like Redis or make use a messaging-based architecture with RabbitMQ.
-->

ローカルファイルシステムの代替手段がいくつかあります。一つのオプションは、MongoDBというドキュメントデータベース、あるいは、サポートされるリレーショナルデータベース(MySQLやvFabric Posgres)などのCloud Foundryサービスを使うことです。もう一つのオプションは、 [Amazon S3](http://aws.amazon.com/s3/) や [Google Cloud Storage](https://cloud.google.com/products/cloud-storage), [Dropbox](https://www.dropbox.com/developers), あるいは [Box](http://developers.box.com/) などのクラウドストレージを使うことです。アプリケーションが異なるインスタンス間で通信する必要があるならば、Redis のようなキャッシュや、RabbitMQを使ったメッセージングベースのアーキテクチャを採用することを検討してください。

## <a id="sessions"></a>HTTP セッション ##

<!--
Cloud Foundry supports session affinity or sticky sessions for incoming HTTP requests to applications. If multiple instances of an application are running on Cloud Foundry, all requests from a given client will be routed to the same application instance. This allows application containers and frameworks to store session data specific to each user session.
-->

Cloud Foundry はアプリケーションに渡されるHTTPのリクエストについて、セッションアフィニティあるいはスティッキーセッションをサポートします。複数のインスタンスがCloud Foundry上で動いている場合、一度アクセスしたクライアントからのすべてのリクエストは、同じアプリケーションインスタンスに転送されます。これによってアプリケーションコンテナやフレームワークはセッションデータをそれぞれのユーザーセッションに保存することができます。

<!--
Cloud Foundry does not persist or replicate HTTP session data. If an instance of an application crashes or is stopped, any data stored for HTTP sessions that were sticky to that instance will be lost. When a user session that was sticky to a crashed or stopped instance makes another HTTP request, the request will be routed to another instance of the application.
-->

Cloud FoundryはHTTPのセッションデータを複製したり永続化したりしません。アプリケーションのインスタンスがクラッシュもしくは停止すれば、そのインスタンスにひもづけられたHTTPセッションのデータはロスとします。クラッシュもしくは停止したインスタンスにひもづけられたユーザーセッションが別のHTTPリクエストを発行した場合、そのリクエストは別のインスタンスにルーティングされるでしょう。

<!--
Any session data that needs to be available even after an application crashes or is stopped, or needs to be shared by all instances of an application, should be stored in a Cloud Foundry service.
-->

アプリケーションがクラッシュもしくは停止した後も利用できる必要があるセッションデータや、すべてのインスタンスで共有される必要があるセッションデータは、Cloud Foundryサービスの中に保存されるべきです。

## <a id="ports"></a>ポート ##

<!--
Applications running on Cloud Foundry can only receive requests using the URLs configured for the application, and only on ports 80 (the standard HTTP port) and 443 (the standard HTTPS port).
-->

Cloud Foundry上で動作するアプリケーションは、アプリケーションに設定されたURLを用いて80番（スタンダードHTTPポート)または 443番(スタンダードHTTPSポート)のポートのリクエストのみ受け取ることが出来ます。

<!--
Service instances (MySQL, vFabric Postgres, MongoDB, Redis, RabbitMQ) running on Cloud Foundry can only be accessed by applications running on Cloud Foundry. Service instances cannot be accessed by programs running outside of Cloud Foundry, since only ports 80 and 443 are available. [Service Tunneling](/docs/using/working-with-services/tunneling/index.html) can be used to create a connection from one machine to a service running on Cloud Foundry.
-->

Cloud Foundry上で動作するサービス（MySQL, vFabric Postgres, MongoDB, Redis, RabbitMQ）のインスタンスはCloud Foundry上で動くアプリケーションからのみアクセスできます。80あるいは443ポートのみが利用可能なため、サービスインスタンスはCloud Foundry外部のプログラムからアクセスすることはできません。[サービストンネル](/docs/using/working-with-services/tunneling/index.html)を利用することで、あるマシンからCloud Foundry上のサービスにコネクションを張ることが出来ます。

<!--
If an application running outside of Cloud Foundry needs access to data stored in a Cloud Foundry service, you should create a web service application to expose the data and run the web service application on Cloud Foundry.
-->

Cloud Foundryの外で動くアプリケーションがCloud Foundry内のサービスのデータにアクセスする必要がある場合、 データを公開するWebサービスアプリケーションを作成して、Cloud Foundry上で動かすべきです。

## <a id="smtp"></a>SMTP ##

<!--
In order to prevent spam and other abuse, the standard SMTP port (port 25) is blocked on Cloud Foundry. Applications can not send or receive SMTP messages on this port. A good option for applications needing to send and receive email is to use a cloud-based email solution such as [SendGrid](http://sendgrid.com/developers.html).
-->

スパムや不正利用を防ぐために、標準のSMTPポート(25番)はCloud Foundry上でブロックされています。アプリケーションは、このポートを使ってSMTPメッセージを送受信することはできません。電子メールを送受信する必要があるアプリケーションに対しては、[SendGrid](http://sendgrid.com/developers.html) などのクラウドベースの電子メールソリューションを使うのがよいでしょう。

