---
title: サービスへのトンネル
---

**サービスへのトンネリングはCloud Foundry v2では未サポートです**  

実行中のサービスへネイティブ・コネクターを使ってアクセスできます。たとえば、MySQLデータベースへ次のようなコマンドでアクセスできます:

`mysql -D [DATABASE_NAME] -h [HOST_NAME] -P [PORT] -u [USER] -p`

環境変数`VCAP_SERVICES`から接続に関する具体的な情報を得ることができます。環境変数`VCAP_SERVICES`はアプリケーションの`env.log`ファイルの中にあります。

`VCAP_SERVICES`の詳細と`env.log`ファイルの見方については、[VCAP_SERVICES Environment Variable](../deploying-apps/environment-variable.html)をご覧ください。

<!---
## <a id='what-is-tunnelling'></a>What Is Tunneling? ##

Cloud Foundry上のサービスには通常は直接アクセスできません。サービスにバインドされたアプリケーションはアクセスできますが、それらはCloud
Foundryファイアーウォールの向こう側にある同じネットワークに接続されています。

Cloud
Foundryの外部からサービスにアクセスするには、トンネリングと呼ばれる技術を使います。Caldecottという名前の特別なアプリケーションをデプロイします。このアプリケーションが目的のサービスとバインドし、HTTP上のプロキシとして動作します。デプロイされると、Caldecottはリクエストを待ち、トンネルを作成します。

接続が確立すると、cfなどのクライアントはトンネルを使うことができます。クライアントはループバック・アダプタ(127.0.0.1)を作り、該当サービスのクライアントがこれを利用できます。

## <a id='creating-a-tunnel'></a>Create a tunnel ##

以下の例では、MySQLデータベースへのトンネルを作り、次にmysqldumpを使ってデータベースのバックアップを作成します。(データベースは空ですが)

Create a service instance with cf;

<pre class="terminal">
$ cf create-service
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

cfでトンネルを作り、クライアントとしてmysqldumpを選択し、バックアップ先となるファイル名(mydb.sql)を入力します;

<pre class="terminal">
$ cf tunnel mysql-a7cc7
1: none
2: mysql
3: mysqldump
Which client would you like to start?> 3

Opening tunnel on port 10000... OK
Waiting for local tunnel to become available... OK
Output file> mydb.sql
</pre>

ダンプが正常にmydb.sqlへ書き込まれました。この時点でトンネルがクローズされます。しかし、option 1 -
noneが選択された場合、トンネルは開いたままになります:

<pre class="terminal">
$ cf tunnel mysql-a7cc7
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

これによりネイティブなクライアントがサービスに接続します。 ここで、MySQLに接続するポートは10000であり、3306ではないことに注意してください。

-->
