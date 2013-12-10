---
title: サービスのバインド
---

**注意:** このページで述べるオート・コンフィギュレーションは、Cloud Foundry v2では未だサポートされていません。

## <a id='intro'></a>紹介 ##

本ガイドは、Cloud Foundry上で実行されるNode.jsアプリケーションの開発者向けです。

## <a id='prerequisites'></a>前提 ##

* Cloud Foundryのアカウント。右のページでサインアップできます。[サインアップ](https://my.cloudfoundry.com/signup)
* The [CF](../../managing-apps/index.html) コマンド・ライン・ツール
* [Node.js](http://www.nodejs.org) Cloud Foundryインスタンスへ適切なヴァージョンのNODE.jsをインストールする。
* [NPM](http://npmjs.org/) - アプリの依存関係などを管理するNodeパッケージマネージャー
* サンプル・アプリケーションは[このチュートリアル](./index.html)にあります。

### <a id='creating'></a> サービスを作成する ##

サービスを作るには、以下のようにcfコマンドを実行し、対話的に質問に答えます;

~~~bash
$ cf create-service
~~~

## <a id='autoconfig'></a>自動設定 ##

以下のようなサービスがNode.jsアプリケーションにバインドされている時、Cloud Foundryが自動的に設定します。

* Rabbit MQ via the '[ampq](https://github.com/postwait/node-amqp)' module
* Mongo via the '[mongodb](http://mongodb.github.com/node-mongodb-native/)'
  and '[mongoose](http://mongoosejs.com/)' modules
* MySQL via the '[mysql](https://github.com/felixge/node-mysql)' module
* Postgres via the '[pg](https://github.com/brianc/node-postgres) module
* Redis via the '[redis](https://github.com/mranney/node_redis)' module

この時点でアプリケーションはポート3000で待ち受けます。アプリケーションに手を加えずにデプロイするために、プロジェクトへcf-autoconfig
node モジュールを追加します。以下の二箇所を変更し、cf-autoconfigをpackage.jsonへ追加します。

~~~json

{
  "name": "hello-node",
  "version": "0.0.1",
  "dependencies": {
    "express": "*",
    "cf-autoconfig": "*"
  },
  "engines": {
    "node": "0.8.x"
  }
}
~~~

アプリケーションの先頭でこのライブラリをrequireします;

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

app.get('/', function(req, res) {
    res.send('Hello from Cloud Foundry');
});

app.listen(3000);
~~~

通常通りアプリケーションをデプロイすると、ポートが自動的に割当てられ、接続が行なわれます。自動設定を使いたくなければ、環境変数から得たポートへ接続するようアプリを変更するだけです;

~~~javascript
var express = require("express");
var app = express();

app.get('/', function(req, res) {
    res.send('Hello from Cloud Foundry');
});

app.listen(process.env.VCAP_APP_PORT || 3000);
~~~

## <a id='Connecting'></a> サービスへ接続する ##

アプリケーションにrecord_visitという関数を追加してみましょう。これは訪問者のIPアドレスと日付を記録する、接続のデモンストレーションです。

### <a id='module-support'></a> 適切なモジュールのサポートを追加する ###

package.jsonを編集し、dependenciesセクションへ該当モジュールを追加します。通常、一つだけが必要ですが、念のためすべて追加することにします。

~~~json
{
  "name": "hello-node",
  "version": "0.0.1",
  "dependencies": {
    "express": "*",
    "cf-autoconfig": "*",
    "mongodb": "*",
    "mongoose": "*",
    "mysql": "*",
    "pg": "*",
    "redis": "*",
    "ampq": "*"
  },
  "engines": {
    "node": "0.8.x"
  }
}
~~~

次に、app.jsを編集し、接続の内容はブランクにしておきます。普段はホストのアドレスやポートやユーザ名とパスワードを書く部分です。

### <a id='mongodb'></a> Mongodb ##

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

var record_visit = function(req, res){
  require('mongodb').connect('', function(err, conn){
    conn.collection('ips', function(err, coll){
      object_to_insert = { 'ip': req.connection.remoteAddress, 'ts': new Date() };
      coll.insert( object_to_insert, {safe:true}, function(err){
        res.writeHead(200, {'Content-Type': 'text/plain'});
        res.write(JSON.stringify(object_to_insert));
        res.end('\n');
      });
    });
  });
}

app.get('/', function(req, res) {
  record_visit(req, res);
});

app.listen(3000); ~~~~

### <a id='redis'></a> Redis ##

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

var record_visit = function(req, res){
  var redis = require("redis"),
  client = redis.createClient();

  client.on("error", function (err) {
    console.log("Error " + err);
  });

  client.set("last_ip", req.connection.remoteAddress, redis.print);
}

app.get('/', function(req, res) {
  record_visit(req, res);
  res.send('Hello from Cloud Foundry');
});

app.listen(3000);
~~~~


### <a id='mysql'></a> MySQL ##

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

var record_visit = function(req, res){

  var mysql      = require('mysql');
  var connection = mysql.createConnection();

  connection.connect();

  connection.query('SELECT NOW() as time_now;', function(err, rows, fields) {
    if (err) throw err;

    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.write(rows[0].time_now);
    res.end('\n');

    console.log('The solution is: ', );
  });

  connection.end();
}

app.get('/', function(req, res) {
  record_visit(req, res);
});

app.listen(3000);
~~~~

### <a id='rabbitmq'></a> Rabbit MQ ##

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

var record_visit = function(req, res){
  var amqp = require('amqp');

  var connection = amqp.createConnection();

  // Wait for connection to become established.
  connection.on('ready', function () {
    // Use the default 'amq.topic' exchange
    connection.queue('my-queue', function(q){
        // Catch all messages
        q.bind('#');

        // Receive messages
        q.subscribe(function (message) {
          // Print messages to stdout
          console.log(message);
        });
    });
  });
}

app.get('/', function(req, res) {
  record_visit(req, res);
});

app.listen(3000);
~~~~

### <a id='binding'></a> Binding a service ##

サービスとアプリケーションをバインドするためには、以下のcfコマンドを実行してください;

~~~bash
$ cf bind-service --app [application name] --service [service name]
~~~

