---
title: サービスの管理
---

## <a id='viewing-services'></a> View Available Services ##

After targeting and logging into
[cf](/docs/using/managing-apps/cf/index.html)コマンドでCloud
Foundryを指定し、ログインした後、利用可能なサービスを知ることができます:

<pre class="terminal"> $ cf info --services </pre>

このコマンドはアプリケーションから接続できるサービスの一覧を表示します。以下はCloud Foundryプライベートβでの例です。

<pre class="terminal"> $ cf info --services Getting services... OK

service      version   provider        plans                        description                     
mongodb      n/a       mongolab-dev    free, large, medium, small   Cloud hosted and managed MongoDB
mongodb      2.2       core            200                          MongoDB NoSQL database          
mysql        5.5       core            100, 200, cfinternal         MySQL database                  
postgresql   9.2       core            200                          PostgreSQL database (vFabric)   
rabbitmq     3.0       core            200                          RabbitMQ message queue          
redis        2.6       core            200                          Redis key-value store            
</pre>

<i>Note: This is an example. 現在のCloud Foundryでは使えないサービスも含まれています。</i>

## <a id='create'></a>Create a Service ##

サービスを作成し、バインドするには、以下のコマンドを使ってください。

<pre class="terminal"> $ cf create-service 1: mongodb n/a, via mongolab-dev
2: mongodb 2.2 3: mysql 5.5 4: postgresql 9.2 5: rabbitmq 3.0 6: redis 2.6
7: redis n/a, via redistogo-dev 8: smtp n/a, via sendgrid-dev What kind?> 3

Name?> mysql-a0a77

1: 100: Shared service instance, 10MB storage, 2 connections 2: 200:
Dedicated server, shared VM, 256MB memory, 2.5GB storage, 30 connections
Which plan?> 2

Creating service mysql-a0a77... OK </pre>

## <a id='bind'></a>Bind a Service ##

サービスをアプリケーションへバインドすると、環境変数VCAP_SERVICESへ資格情報が追加されます。通常、この資格情報はユニークです;
別のアプリが同じサービスへ接続しても、異なる資格情報を受け取ります。変更を反映するため、アプリケーションの再起動が必要な場合があります。

アプリが環境変数の内容にどのように関わるかは、使っているフレームワークに依存します。詳しくは、[Deploying
Apps](/docs/using/deploying-apps/index.html) セクションをご覧ください。

既存のサービスをアプリケーションへバインドするには、以下のようにします:

<pre class="terminal"> $ cf bind-service 1: my-app Which application?> 1

1: mysql-a0a77 Which service?> 1

Binding mysql-a0a77 to my-app... OK </pre>

## <a id='unbind'></a>Unbind a Service ##

サービスを切り離すと、環境変数VCAP_SERVICESから該当する資格情報が削除されます。変更を反映するため、アプリケーションの再起動が必要な場合があります。

<pre class="terminal"> $ cf unbind-service 1: my-app Which application?> 1

1: mysql-a0a77 Which service?> 1

Unbinding mysql-a0a77 from my-app... OK </pre>

## <a id='delete'></a>Delete a Service ##

サービスを削除すると、サービスのインスタンスと*すべてのデータ* が削除されます。

<pre class="terminal"> $ cf delete-service 1: mysql-a0a77 Which service?> 1

Really delete mysql-a0a77?> y

Deleting mysql-a0a77... OK </pre>
