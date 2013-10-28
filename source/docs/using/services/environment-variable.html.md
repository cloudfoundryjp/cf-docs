---
title: VCAP_SERVICES環境変数
---

サービスをアプリケーションへバインドすることにより、アプリケーションのプロセスの環境変数VCAP\_SERVICESへ資格情報が追加されることがあります。VCAP\_SERVICESの内容を参照するにはいくつかの方法があります。

## <a id='cli'></a>VCAP_SERVICESをコマンドで参照する ##

このコマンドはVCAP_SERVICESを含むすべての環境変数を見ることができます。

<pre class="terminal"> $ cf files APP_NAME_HERE logs/env.log </pre>

## <a id='app'></a>アプリケーションからVCAP_SERVICESを参照する ##

以下のようなコードで環境変数を扱えます。

### Java

``` System.getenv("VCAP_SERVICES"); ```

### Ruby

``` ENV['VCAP_SERVICES'] ```

### Node.js

``` process.env.VCAP_SERVICES ```

## <a id='example'></a>Example Contents ##

この例では、VCAP\_SERVICESの内容を読み出します。ここでは、四つのサービスのインスタンスを作成し、バインドしています。一つ目はClearDB,
二つ目はCloudAMQP, もう一つはRedis Cloudです。

~~~
VCAP_SERVICES=
{
  cleardb-n/a: [
    {
      name: "cleardb-1",
      label: "cleardb-n/a",
      plan: "spark",
      credentials: {
        name: "ad_c6f4446532610ab",
        hostname: "us-cdbr-east-03.cleardb.com",
        port: "3306",
        username: "b5d435f40dd2b2",
        password: "ebfc00ac",
        uri: "mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab",
        jdbcUrl: "jdbc:mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab"
      }
    }
  ],
  cloudamqp-n/a: [
    {
      name: "cloudamqp-6",
      label: "cloudamqp-n/a",
      plan: "lemur",
      credentials: {
        uri: "amqp://ksvyjmiv:IwN6dCdZmeQD4O0ZPKpu1YOaLx1he8wo@lemur.cloudamqp.com/ksvyjmiv"
      }
    }
    {
      name: "cloudamqp-dev-9dbc6",
      label: "cloudamqp-dev-n/a",
      plan: "lemur",
      credentials: {
        uri: "amqp://vhuklnxa:9lNFxpTuJsAdTts98vQIdKHW3MojyMyV@lemur.cloudamqp.com/vhuklnxa"
      }
    }
  ],
  rediscloud-n/a: [
    {
      name: "rediscloud-1",
      label: "rediscloud-n/a",
      plan: "20mb",
      credentials: {
        port: "17546",
        host: "pub-redis-17546.MatanCluster.ec2.garantiadata.com",
        password: "1M5zd3QfWi9nUyya"
      }
    },
  ],
}
~~~

## <a id='database_url'></a>DATABASE_URL ##

もしアプリケーションが接続用文字列を設定されたDATABASE_URLに依存しているのなら、自分で設定してもかまいません。

<pre class="terminal"> $ cf set-env myapp DATABASE_URL
mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab
</pre>
