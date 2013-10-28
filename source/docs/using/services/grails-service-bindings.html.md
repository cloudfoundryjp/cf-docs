---
title: Grails - サービスのバインド
---

Cloud FoundryはGrailsアプリケーションとMySQL, Postgres, MongoDB, Redis,
RabbitMQなどのサービスとの接続をサポートしている。多くの場合、Cloud
Foundry上のGrailsアプリケーションは自動的にサービスを検出し、接続することができる。それ以外の場合、接続パラメーターを自分で制御することもできます。

## <a id="auto"></a>Auto-Reconfiguration ##

GrailsはSQLアクセスのプラグインを提供している。
([Hibernate](http://grails.org/plugin/hibernate)を使って)や[MongoDB](http://www.grails.org/plugin/mongodb)や[Redis](http://grails.org/plugin/redis)など。これらのうちの一つをインストールし`Config.groovy`か`DataSource.groovy`で設定すれば、アプリが起動し接続情報が与えられた際にCloud
Foundry Grailsプラグインが自動的に再設定します。

サービス接続のオート・リコンフィギュレーションを有効にするには、
`cloudfoundry-runtime`ライブラリを`BuildConfig.groovy`ファイルの`dependencies`セクションへ追加し、`cloud-foundry`プラグインを
`plugins`セクションへ追加してください。また、Spring Framework
Milestoneリポジトリを`repositories`セクションへ追加してください。**Cloud Foundry
v2をサポートするには、`cloudfoundry-runtime`のヴァージョンは`0.8.4`以上にしてください**:

~~~groovy
  repositories {
    grailsHome()
    mavenCentral()
    grailsCentral()
    mavenRepo "http://maven.springframework.org/milestone/"
  }

  dependencies {
    compile "org.cloudfoundry:cloudfoundry-runtime:0.8.4"
  }

  plugins {
    compile ':cloud-foundry:1.2.3'
  }
~~~

If you were using all three types of services, your configuration might look
like this:

~~~groovy
environments {
  production {
    dataSource {
      url = 'jdbc:mysql://localhost/db?useUnicode=true&characterEncoding=utf8'
      dialect = org.hibernate.dialect.MySQLInnoDBDialect
      driverClassName = 'com.mysql.jdbc.Driver'
      username = 'user'
      password = "password"
    }
    grails {
      mongo {
        host = 'localhost'
        port = 27107
        databaseName = "foo"
        username = 'user'
        password = 'password'
      }
      redis {
        host = 'localhost'
        port = 6379
        password = 'password'
        timeout = 2000
      }
    }
  }
}
~~~

この設定では、`url`, `host`, `port`, `databaseName`, `username`, and `password`
はCloud Foundry
Grailsプラグインに上書きされます。ローカルな環境で自分のサービスに接続してアプリケーションを試験したい場合、これらのフィールドには実際の値をセットできます。Cloud
Foundryのサービスにのみ接続するアプリケーションなら、以下のような値をセットできます。ただし、フィールドは存在していなければなりません。

## <a id="manual"></a>Manual Configuration ##

Cloud Foundry Grailsプラグインを使いたくないなら、Cloud Foundry のサービスへ自分で設定することもできます。

自分で設定する場合、最善の方法は`cloudfoundry-runtime` ライブライリを使ってアプリケーションが動作するCloud
Foundry環境の情報を得ることです。このライブラリを使うには、`BuildConfig.groovy`ファイルの`dependencies`セクションにこのライブラリを追加してください。また、Spring
Framework Milestoneリポジトリを`repositories`セクションへ追加してください。**Cloud Foundry
v2サポートが必要なら、このライブラリのヴァージョンは`0.8.4`以上である必要があります**:

~~~groovy
  repositories {
    grailsHome()
    mavenCentral()
    grailsCentral()
    mavenRepo "http://maven.springframework.org/milestone/"
  }

  dependencies {
    compile "org.cloudfoundry:cloudfoundry-runtime:0.8.4"
  }
~~~

その後、`DataSources.groovy`ファイル内で`cloudfoundry-runtime` API
を使って接続のパラメーターをセットできます。三つのタイプのデータベースを使っていて、オート・リコンフィギュレーションの例に従い、サービスの名前が"myapp-mysql",
"myapp-mongodb", and "myapp-redis"だとすると、`DataSources.groovy`ファイルは以下のようになります。

~~~groovy import org.cloudfoundry.runtime.env.CloudEnvironment import
org.cloudfoundry.runtime.env.RdbmsServiceInfo import
org.cloudfoundry.runtime.env.RedisServiceInfo import
org.cloudfoundry.runtime.env.MongoServiceInfo

def cloudEnv = new CloudEnvironment()

environments {
  production {
    dataSource {
      pooled = true
      dbCreate = 'update'
      driverClassName = 'com.mysql.jdbc.Driver'

      if (cloudEnv.isCloudFoundry()) {
        def dbInfo = cloudEnv.getServiceInfo('myapp-mysql', RdbmsServiceInfo.class)
        url = dbInfo.url
        username = dbInfo.userName
        password = dbInfo.password
      } else {
        url = 'jdbc:mysql://localhost:5432/myapp'
        username = 'sa'
        password = ''
      }
    }
    
    grails {
      mongo {
        if (cloudEnv.isCloudFoundry()) {
          def mongoInfo = cloudEnv.getServiceInfo('myapp-mongodb', MongoServiceInfo.class)
          host = mongoInfo.host
          port = mongoInfo.port
          databaseName = mongoInfo.database
          username = mongoInfo.userName
          password = mongoInfo.password
        } else {
          host = 'localhost'
          port = 27107
          databaseName = 'foo'
          username = 'user'
          password = 'password'
        }
      }
      redis {
        if (cloudEnv.isCloudFoundry()) {
          def redisInfo = cloudEnv.getServiceInfo('myapp-redis', RedisServiceInfo.class)
          host = redisInfo.host
          port = redisInfo.port
          password = redisInfo.password
        } else {
          host = 'localhost'
          port = 6379
          password = 'password'
        }
      }
    }
  }
}
~~~

