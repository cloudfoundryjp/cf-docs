---
title: サービストンネル
---

## <a id='what-is-tunnelling'></a>トンネリングとは何か？ ##

Cloud Foundry上で提供されるいくつかのサービスは、デフォルトでは外の世界から直接アクセスすることは出来ません。サービスをバインドしているアプリケーションはアクセス出来ますが、それはCloud Foundryの背後の同じネットワーク存在するからです。

Cloud Foundryエコシステムの外側からサービスにアクセスする技術をトンネリングと呼んでいます。このスペシャルアプリケーションはCaldecottと呼ばれます。Caldecottは希望するサービスにHTTP経由でプロキシ接続を行います。一度でデプロイを行えば、Caldecottはトンネル作成のために利用可能のままになります。

一度確率されれば、トンネルはクライアント（vmc)によって利用可能になります。クライアントは、バインドされたネイティブクライアントで利用可能なループバックアダプタ(127.0.0.1)上のポートを作成します。

## <a id='creating-a-tunnel'></a>トンネルの作成 ##

以下のサンプルは、MySQLデータベースへのトンネルを作成し、mysqldumpを利用してデータベースのバックアップを説明します。ただし、中身は空です。

vmcを使ってサービスインスタンスを作成します
Create a service instance with vmc;

<pre class="terminal">
$ vmc create-service
1: blob 0.51
2: mongodb 2.0
3: mysql 5.1
4: postgresql 9.0
5: rabbitmq 2.4
6: redis 2.2
7: redis 2.4
8: redis 2.6
What kind?> 3

Name?> mysql-a7cc7

Creating service mysql-a7cc7... OK
</pre>

vmcを使ってサービスにトンネルし、mysqldumpクライアントとダンプするためのファイルパス(mydb.sql)選択します。

<pre class="terminal">
$ vmc tunnel mysql-a7cc7
1: none
2: mysql
3: mysqldump
Which client would you like to start?> 3

Opening tunnel on port 10000... OK
Waiting for local tunnel to become available... OK
Output file> mydb.sql
</pre>

ダンプは、mydb.sqlが書かれれば成功です。この時トンネルはクローズされます。しかしながら、ノードが選択された時、トンネルは無期限に開いたままなります。

At this point the tunnel has closed, however if option one - 
none is selected then the tunnel will be held open indefinitely supplying the connection details;

<pre class="terminal">
$ vmc tunnel mysql-a7cc7
1: none
2: mysql
3: mysqldump
Which client would you like to start?> 1

Opening tunnel on port 10000... OK

Service connection info:
  username : uFlLtV9lfB1xV
  password : pqS7RpFXG9Jhu
  name     : db1626ceeb99d42739244cb5c635519e6


Open another shell to run command-line clients or
use a UI tool to connect using the displayed information.
Press Ctrl-C to exit...
</pre>

サービス用のネイティブクライアントが使えます。このインスタンの場合、MySQLの接続ポートが3306ではなく、10000で利用可能です。
