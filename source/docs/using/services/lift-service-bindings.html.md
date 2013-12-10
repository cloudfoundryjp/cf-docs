---
title: Lift - サービスのバインド
---

## <a id='intro'></a>紹介 ##

本ガイドは、liftアプリケーションをCloud Foundryのサービスへ接続する方法を説明します。

## <a id='auto'></a>自動設定 ##

デフォルトでは、Cloud FoundryはLiftアプリケーションの中のサービスへの接続を検出し、Cloud Foundry環境が提供する資格情報を使って設定します。自動設定は、各タイプごとに一つのサービスだけが使われている場合にのみ適用されます
- リレーショナル・データベース(MySQL or Postgres), MongoDB, Redis, RabbitMQ各タイプごとで二つ以上のサービスを使っているか、よりきめ細かい制御をしたいならば、自分で設定することもできます。やり方については後述します。

## <a id='manual'></a>手動設定 ##

Scalaでデータベース接続の手動設定をしたい場合、プロジェクトへもう少し追加する必要があります。まず、`spring-core`と`cloudfoundry-runtime`をMavenの`pom.xml` へ追加します。**Cloud Foundry v2をサポートするには、`cloudfoundry-runtime`のヴァージョンは`0.8.4`以上にしてください**:

~~~xml
  <dependency>
    <groupId>spring</groupId>
    <artifactId>spring-core</artifactId>
    <version>1.0.2</version>
  </dependency>

  <dependency>
    <groupId>org.cloudfoundry</groupId>
    <artifactId>cloudfoundry-runtime</artifactId>
    <version>0.8.4</version>
  </dependency>
~~~

Spring milestonesリポジトリも追加が必要になるでしょう:

~~~xml
  <repository>
    <id>org.springframework.maven.milestone</id>
    <name>Spring Framework Maven Milestone Repository</name>
    <url>http://maven.springframework.org/milestone</url>
  </repository>
~~~

`./src/main/scala/bootstrap/liftweb/Boot.scala`の中のConnection Managerを設定しましょう。以下のnamespacesをインポートします:

~~~scala
  import org.cloudfoundry.runtime.env._
  import org.cloudfoundry.runtime.service.relational._
  import scala.collection.JavaConversions._
~~~

次にブート・メソッドのはじめの部分のコードを以下のように置き換えます。この例では、`cloudfoundry-runtime`ライブラリを使ってコードの先頭部にある名前に対応するサービスを見つけ、コネクション・マネージャーを作成し、セットします。

~~~scala
  val serviceName = "lift-db"
  val service = new CloudEnvironment().getServiceDataByName("name")
  val creds = service.get("credentials").asInstanceOf[java.util.LinkedHashMap[String, Object]]

  val password = Box[String](creds.get("password").toString)
  val username = Box[String](creds.get("username").toString)
  val url = "jdbc:mysql://" + creds.get("host") + ":" + creds.get("port") + "/" + creds.get("name")
  val driver = "com.mysql.jdbc.Driver"

  val vendor = new StandardDBVendor(driver, url, username, password)

  LiftRules.unloadHooks.append(vendor.closeAllConnections_! _)
  DB.defineConnectionManager(DefaultConnectionIdentifier, vendor)
~~~

