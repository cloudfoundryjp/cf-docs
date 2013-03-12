<!-- ---
title: Tunnelling with Services
---
-->
---
title: サービストンネル
---

<!--
## <a id='what-is-tunnelling'></a>What is tunnelling? ##
-->
## <a id='what-is-tunnelling'></a>トンネリングとは何か？ ##

<!--
Any provisioned service on Cloud Foundry is not directly accessible to the outside world by default. An application that is bound to the service has access, but only because it sits on the same network, behind the Cloud Foundry firewall.
-->
Cloud Foundry上で提供されるいくつかのサービスは、デフォルトでは外の世界から直接アクセスすることは出来ません。サービスをバインドしているアプリケーションはアクセス出来ますが、それはCloud Foundryの背後の同じネットワーク存在するからです。

<!--
To gain access to a service from outside of the Cloud Foundry ecosystem, a technique called tunneling is used. This means deploying a special application, called Caldecott, to a Cloud Foundry account. The application then binds and connects to the desired service and proxies a connection over HTTP to the service. Once deployed, Caldecott remains available for the creation of tunnels.
-->
Cloud Foundryエコシステムの外側からサービスにアクセスする技術をトンネリングと呼んでいます。このスペシャルアプリケーションはCaldecottと呼ばれます。Caldecottは希望するサービスにHTTP経由でプロキシ接続を行います。一度でデプロイを行えば、Caldecottはトンネル作成のために利用可能のままになります。

<!--
Once established, the tunnel can be used by a client, most likely vmc. The client makes a port on the loopback adapter (127.0.0.1) available to use with a native client of the bound service.
-->
一度確率されれば、トンネルはクライアント（vmc)によって利用可能になります。クライアントは、バインドされたネイティブクライアントで利用可能なループバックアダプタ(127.0.0.1)上のポートを作成します。

<!--
## <a id='creating-a-tunnel'></a>Creating a tunnel ##
-->
## <a id='creating-a-tunnel'></a>トンネルの作成 ##

<!--
The following example illustrates creating a tunnel to a MySQL database and then using mysqldump to create a back up of the database, even though it will be empty!
-->
以下のサンプルは、MySQLデータベースへのトンネルを作成し、mysqldumpを利用してデータベースのバックアップを説明します。ただし、中身は空です。

<!--
Create a service instance with vmc;
-->
vmcコマンドを利用してサービスインスタンスを作成します

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

<!--
Tunnel to the service with vmc, select mysqldump for the client and give a file path (mydb.sql) to dump to;
-->
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

<!--
The dump is succesfully writen to mydb.sql. At this point the tunnel has closed, however if option one - none is selected then the tunnel will be held open indefinitely supplying the connection details;
-->
ダンプは、mydb.sqlが書かれれば成功です。この時トンネルはクローズされます。しかしながら、ノードが選択された時、トンネルは無期限に開いたままなります。

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
<!--
This allows the use of a native client to connect to the service. Note that in this instance, for MySQL, the connection is available on port 10000 not 3306.
-->
サービス用のネイティブクライアントが使えます。このインスタンの場合、MySQLの接続ポートが3306ではなく、10000で利用可能です。
