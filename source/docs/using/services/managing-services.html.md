---
title: サービスの管理
---

## <a id='viewing-services'></a> 利用可能なサービスを見る ##

ターゲットの指定とログインの後、[cf](/docs/using/managing-apps/cf/index.html)コマンドでCloud Foundryを指定し、ログインした後、利用可能なサービスを表示することができます:

<pre class="terminal">
$ cf services --marketplace
</pre>

このコマンドはアプリケーションから接続できるサービスの一覧を表示します。以下はCloud Foundryプライベートβでの例です。

<pre class="terminal">
$ cf services --marketplace
Getting services... OK

service      version   provider        plans                        description                     
mongodb      n/a       mongolab-dev    free, large, medium, small   Cloud hosted and managed MongoDB
mongodb      2.2       core            200                          MongoDB NoSQL database          
mysql        5.5       core            100, 200, cfinternal         MySQL database                  
postgresql   9.2       core            200                          PostgreSQL database (vFabric)   
rabbitmq     3.0       core            200                          RabbitMQ message queue          
redis        2.6       core            200                          Redis key-value store            
</pre>

<i>Note: これは一例です。 現在のCloud Foundryでは使えないサービスも含まれています。</i>

## <a id='create'></a>サービス・インスタンスを作成する ##

サービス・インスタンスを作成するには、以下のコマンドを使ってください。

<pre class="terminal">
$ cf create-service
1: mongodb n/a, via mongolab
2: mongodb 2.2
3: mysql 5.5
4: postgresql 9.2
5: rabbitmq 3.0
6: redis 2.6
7: redis n/a, via redistogo
8: smtp n/a, via sendgrid
What kind?> 3

Name?> mysql-a0a77

1: 100: Shared service instance, 10MB storage, 2 connections 2: 200:
Dedicated server, shared VM, 256MB memory, 2.5GB storage, 30 connections
Which plan?> 2

Creating service mysql-a0a77... OK
</pre>

## <a id='user-provided'></a>ユーザによるサービスのインスタンスを作成する ##

ユーザによるサービスのインスタンスとは、Cloud Foundryの外側で動作するサービスのインスタンスです。たとえば、DBSはCloud Foundryに管理されていないOracleデータベースの利用者情報を提供することがあります。こういったインスタンスの利用者情報をソースに直接書くのではなく、Cloud Foundryのモック・サービスを作って外部のサービスへ接続することができます。そのためには慣れ親しんだ`create-service`コマンドが使え、利用者情報もいつものように扱えます。

* [User Provided Service Instances](user-provided.html)

## <a id='bind'></a>サービスのインスタンスへバインドする ##

サービスをアプリケーションへバインドすると、環境変数VCAP_SERVICESへ資格情報が追加されます。通常、この資格情報はユニークです;
別のアプリが同じサービスへ接続しても、異なる資格情報を受け取ります。変更を反映するため、アプリケーションの再起動が必要な場合があります。

アプリが環境変数の内容にどのように関わるかは、使っているフレームワークに依存します。詳しくは、[Deploying Apps](/docs/using/deploying-apps/index.html) セクションをご覧ください。

既存のサービスをアプリケーションへバインドするには、以下のようにします:

<pre class="terminal">
$ cf bind-service
1: my-app
Which application?> 1

1: mysql-a0a77
Which service?> 1

Binding mysql-a0a77 to my-app... OK
</pre>

## <a id='unbind'></a>サービスのインスタンスをアンバインドする ##

サービスを切り離すと、環境変数[VCAP_SERVICES](../deploying-apps/environment-variable.html)から該当する資格情報が削除されます。変更を反映するため、アプリケーションの再起動が必要な場合があります。

<pre class="terminal">
$ cf unbind-service
1: my-app
Which application?> 1

1: mysql-a0a77
Which service?> 1

Unbinding mysql-a0a77 from my-app... OK
</pre>

## <a id='delete'></a>サービスのインスタンスの削除 ##

サービスを削除すると、サービスのインスタンスと*すべてのデータ* が削除されます。

<pre class="terminal">
$ cf delete-service
1: mysql-a0a77
Which service?> 1

Really delete mysql-a0a77?> y

Deleting mysql-a0a77... OK
</pre>
