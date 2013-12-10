---
title: Spring - サービスのバインド
---

## <a id='intro'></a>紹介 ##

Cloud Foundryは、SpringアプリケーションからMySQL, Postgres, MongoDB, Redis,
RabbitMQなどのサービスへの接続をサポートしています。多くの場合、Cloud
Foundryはコードを変更することなく自動的にSpringアプリケーションを設定することができます。それ以外の場合、接続パラメーターを自分で制御することもできます。

## <a id='auto'></a>自動設定 ##

Springアプリケーションがサービス(データベースやメッセージング・システムなどの)を使っている場合、コードに手を加えることなくCloud Foundryへデプロイできます。こういったケースで、Cloud Foundryは自動的にサービスへの接続を再設定します。

Cloud Foundryは以下の条件が成り立つ場合に自動設定を行ないます:

* サービスごとに、ただ一つのインスタンスだけが使われている。この文脈では、MySQLとPostgresは一つのサービスの種類(リレーショナル・データベース)と見なされます。MySQLとPostgresサービスの両方がバインドされている場合、自動設定は行われません。
* Springアプリケーションのcontextの中で、サービスのタイプごとにただ一つのbeanが存在する。たとえば、`javax.sql.DataSource`のbeanが一つだけある。

自動設定の場合、Cloud Foundryがデータベースまたは接続のfactory beanを作成します。この時、Cloud Foundryが持っているホスト、ポート、ユーザ名などのプロパティを使用します。たとえば、`javax.sql.DataSource`のbeanが一つだけある場合、Cloud Foundryは自分のデータベース・サービスと接続し、元々指定されていたユーザ名、パスワード、URLは使用しません。Cloud Foundryは自分が持っている値を使います。これはアプリケーションからは透過的です。アプリケーションはデータを読み書きできれば十分で、プロパティなどは気にしないからです。また、独自の設定(たとえば、接続のプールの大きさなど)をしていてもCloud Foundryの自動設定はそれを無視します。

サービスのタイプごとの自動設定の詳細については、[Service-specific Details](#services)セクションをご覧ください。

### <a id='optout'></a>自動設定を避ける ###

Cloud Foundryの自動設定が望ましくない場合があります。自動設定をさせないためには、Spring application context file内の[`<cloud:>` namespace](#namespace)エレメントを使って、サービスを示すbeanを明示的に作成してください。これにより自動設定が使われなくなります。

## <a id='manual'></a>手動設定 ##

SpringアプリケーションがCloud Fouundryの自動設定を使えない、あるいはきめ細かく制御したいなどの場合、やるべきことは単純です。

手動設定の場合、`cloudfoundry-runtime`ライブラリを依存関係の中に含めます。ビルド・ファイル(e.g. Maven `pom.xml`ファイル、またはGradle `build.gradle` ファイル)を`org.cloudfoundry.cloudfoundry-runtime`が含まれるように更新します。例として、アプリケーションをビルドするのにMavenを使っているとして、以下の`pom.xml`の一部が**Cloud Foundry v2サポートが必要なら、このライブラリのヴァージョンは`0.8.4`以上である必要があります**:

~~~xml
<dependencies>
    <dependency>
        <groupId>org.cloudfoundry</groupId>
        <artifactId>cloudfoundry-runtime</artifactId>
        <version>0.8.4</version>
    </dependency>

    <!-- additional dependency declarations -->
</dependencies>
~~~ 

また、アプリケーションがSpring Framework
Milestoneリポジトリを含むよう更新する必要があります。以下の`pom.xml`の一部は、Mavenでのやり方を示しています:

~~~xml
<repositories>
    <repository>
          <id>org.springframework.maven.milestone</id>
           <name>Spring Maven Milestone Repository</name>
           <url>http://maven.springframework.org/milestone</url>
           <snapshots>
                   <enabled>false</enabled>
           </snapshots>
    </repository>

    <!-- additional repository declarations -->
</repositories>
~~~

### <a id='namespace'></a>XML Configuration ###

手動でサービスを設定するために、 Spring XML application context
configurationファイルで`<cloud:>`名前空間を使うことができます。このやり方で、Springアプリケーションはあるタイプで複数のサービスを使うことができます。`<cloud:>`名前空間の要素は、一般的な設定のデフォルトを提供します。

Cloud Foundryを使うSpringアプリケーションを更新する基本的なステップは以下のようになります:

* 上にあるように、`cloudfoundry-runtime`への依存関係を追加します。

* Springアプリケーションで、すべてのapplication context filesをCloud Foundry
  サービスの宣言(`<cloud:>`名前空間の宣言やCloud
  Foundryサービス・スキーマの場所を追加によるデータ・ソースなど)を含むように更新してください。設定の一部を以下に示します:

~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:cloud="http://schema.cloudfoundry.org/spring"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.2.xsd
        http://schema.cloudfoundry.org/spring
        http://schema.cloudfoundry.org/spring/cloudfoundry-spring.xsd">

    <!-- bean declarations -->

</beans>
~~~

Cloud FoundryサービスをSpring XML application context
fileで設定するには、`<cloud:>`名前空間とある要素の名前を使います。

Cloud Foundryはサポートするサービスの名前を提供します: データベース(MySQLやPostgres), Redis, MongoDB, RabbitMQなどです。各サービスの名前空間の詳細については、[Service-specific Details](#services)セクションをご覧ください。以下の名前空間の要素は、すべてのサービスにおいて有効です。

#### Cloud Properties ####

`<cloud:properties>`要素は、アプリケーションとバインドされたサービスの基本的な情報を表します。Spring property placeholder supportによって、アプリケーションはこれらのプロパティを使います。

Spring Framework 3.1 (またはそれ以降)を使っているなら、`<cloud:properties>`を含めることなく、自動的に使えるようになることに注意してください。

`<cloud:properties>`要素は一つのアトリビュート(`id`)だけを持ち、`Properties`
beanの名前を示します。このIDを使って`<context:property-placeholder>`を参照します。これによりCloud Foundryによるすべてのプロパティを使うことができます。次に、これらのプロパティを他のbean defintionsで使えます。

Spring application context fileでのこの要素の使い方の例を以下に示します:

~~~xml
<cloud:properties id="cloudProperties" />
~~~

Spring property placeholdersの使い方については、以下をご覧ください。

### <a id='javaconfig'></a>Javaの設定 ###

Java configurationはXMLでの`<cloud:>`名前空間の代わりに使えます。`cloudfoundry-runtime`ライブラリ内の`CloudEnvironment`クラスのメソッドは、サービスを名前かタイプで指定して情報を得るのに使えます。また、各サービスへの接続オブジェクトを作るのに使えます。Java configurationを使うために、`cloudfoundry-runtime`ライブラリをプロジェクトへ追加してください。詳しくは以下[above](#manual)をご覧ください。

各サービスの`CloudEnvironment`の使い方については、以下の[Service-specific Details](#services)をご覧ください。

### <a id='properties'></a>Property Placeholders ###

Cloud Foundryはデプロイされたアプリケーションに対して、アプリケーションやサービスのプロパティを公開します。Cloud
Foundryが公開するプロパティには、名前やクラウド・プロヴァイダーなどのアプリケーションの基本的な情報が含まれます。
バインドされたサービスの詳細は接続情報も含まれます。

サービスのプロパティは、一般的に以下の形式の一つを取ります:

~~~
    cloud.services.{service-name}.connection.{property}
    cloud.services.{service-name}.{property}
~~~

ここで、`{service-name}`はデプロイ時にサービスへ与えた名前を示し、`{property}`は環境変数`VCAP_SERVICES`内の資格情報のフィールドを示します。

例として、`my-postgres`という名前のPostgresサービスを作り、アプリケーションへバインドしたとします。また、`VCAP_SERVICES`内の複数の資格情報を別々のフィールドとして公開しているとします。Cloud
Foundryはこのサービスについて以下のプロパティを提供します:

~~~
    cloud.services.my-postgres.connection.hostname
    cloud.services.my-postgres.connection.name
    cloud.services.my-postgres.connection.password
    cloud.services.my-postgres.connection.port
    cloud.services.my-postgres.connection.username
    cloud.services.my-postgres.plan
    cloud.services.my-postgres.type
~~~

サービスが`uri`フィールドとして資格情報を提供しているなら、以下のプロパティが設定されます:

~~~
    cloud.services.my-postgres.connection.uri
    cloud.services.my-postgres.plan
    cloud.services.my-postgres.type
~~~

楽ができるように、あるタイプにおいてただ一つのサービスだけがある場合、Cloud
Foundryはサービスの名前ではなくタイプの名前を使って別名を定義します。たとえば、アプリケーションへバインドされたMySQLサービスが一つだけならば、プロパティは
`cloud.services.mysql.connection.{property}`という形式になります。このケースでは、Cloud
Foundryは以下の別名を使います:

* `mysql`
* `postgresql`
* `mongodb`
* `redis`
* `rabbitmq`

A Springアプリケーションは、property placeholder mechanismを使って、Cloud Foundryプロパティを活用できます。たとえば、`spring-mysql`という名前のMySQLサービスがあるとします。アプリケーションは、Cloud Foundryが提供する接続ではなくc3p0 connection poolを必要とするが、Cloud FoundryがMySQLサービスに提供するのと同じプロパティを使いたいものとします。ユーザ名、パスワード、JDBC URLなどです。

以下のSpring XML application contextの断片がやり方を示しています:

~~~xml
<beans profile="cloud">
  <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
    <property name="driverClass" value="com.mysql.jdbc.Driver" />
    <property name="jdbcUrl"
              value="jdbc:mysql://${cloud.services.spring-mysql.connection.host}:${cloud.services.spring-mysql.connection.port}/${cloud.services.spring-mysql.connection.name}" />
    <property name="user" value="${cloud.services.spring-mysql.connection.username}" />
    <property name="password" value="${cloud.services.spring-mysql.connection.password}" />
  </bean>
</beans>
~~~

下の表は、Cloud Foundryがデプロイされたアプリケーションへ提供するプロパティの一覧です。

| プロパティ               | 説明                                                               |
|------------------------|---------------------------------------------------------------------------|
| cloud.application.name | The name provided when the application was pushed to CloudFoundry.        |
| cloud.provider.url     | The URL of the cloud hosting the application, such as `cloudfoundry.com`. |

各サービスごとの提供されるプロパティは[Service-specific Details](#services)セクションにあります。

### <a id='profiles'></a>プロファイル ###

Spring Framework versions 3.1またはそれ以降は、条件付のアプリケーションの設定という方法で、ean definition profilesをサポートしています。ある条件が成り立つ時にのみ、一つのbean definitionだけが有効になります。これらのプロパティを設定すると、アプリケーションは環境によっていちいち設定しなおす必要がなくなります。たとえば、あなたのローカル環境とCloud Foundryでそのまま使えることになります。

Spring bean definition profilesについて、詳しくは[Spring Framework
documentation](http://static.springsource.org/spring/docs/current/spring-framework-reference/html/new-in-3.1.html#new-in-3.1-bean-definition-profiles)をご覧ください。

SpringアプリケーションをCloud Foundryへデプロイする際、Cloud Foundry は自動的に`cloud` profileを有効にします。

#### Profiles in Java Configration ####

どの設定が呼ばれるかの条件を決めるために、Springアプリケーションの`@Profile` annotationを`@Configuration`クラスに置くことができます`default`や`cloud`プロファイルは、Cloud Foundry上で実行されているかどうか知ることができ、Java configurationはローカルとcloud deploymentの両方をサポートできます。Java configuration classesは以下のようになります:

~~~java
@Configuration
@Profile("cloud")
public class CloudDataSourceConfig {
    @Bean
    public DataSource dataSource() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RdbmsServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-postgres", RdbmsServiceInfo.class);
        RdbmsServiceCreator serviceCreator = new RdbmsServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }
}
~~~

~~~java
@Configuration
@Profile("default")
public class LocalDataSourceConfig {
    @Bean
    public DataSource dataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setUrl("jdbc:postgresql://localhost/db");
        dataSource.setDriverClassName("org.postgresql.Driver");
        dataSource.setUsername("postgres");
        dataSource.setPassword("postgres");
        return dataSource;
    }
}
~~~


#### XMLの設定内でのプロファイル ####

適切なSpring application context
file内のネストした`<beans>`要素のプロファイル・アトリビュートを使って、XML設定ファイルの中で特定の環境向けの設定をグループ化できます。独自のプロファイルを作れますが、Cloud
Foundryにとってもっともだいじなものは`default`や`cloud`プロファイルです。

Cloud Foundry外の環境でアプリケーションが動作できるように、`cloud` profile block内の`<cloud:>`
名前空間の使い方をグループ化すべきです。次に、`default`プロファイル(または独自のプロファイル)を、 Cloud Foundry以外のための設定
that will be used if you deploy your application to a non-Cloud Foundry
environment.

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans  xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:cloud="http://schema.cloudfoundry.org/spring"
        xmlns:jdbc="http://www.springframework.org/schema/jdbc"
        xmlns:util="http://www.springframework.org/schema/util"
        xmlns:mongo="http://www.springframework.org/schema/data/mongo"
        xsi:schemaLocation="http://www.springframework.org/schema/data/mongo
          http://www.springframework.org/schema/data/mongo/spring-mongo-1.0.xsd
          http://www.springframework.org/schema/jdbc
          http://www.springframework.org/schema/jdbc/spring-jdbc-3.2.xsd
          http://schema.cloudfoundry.org/spring
          http://schema.cloudfoundry.org/spring/cloudfoundry-spring.xsd
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
          http://www.springframework.org/schema/util
          http://www.springframework.org/schema/util/spring-util-3.2.xsd">

  <bean id="mongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">
     <constructor-arg ref="mongoDbFactory" />
  </bean>
  
  <beans profile="default">
    <mongo:db-factory id="mongoDbFactory" dbname="pwdtest" host="127.0.0.1" port="27017" username="test_user" password="efgh" />
  </beans>

  <beans profile="cloud">
    <cloud:mongo-db-factory id="mongoDbFactory" />
  </beans>

</beans>
~~~

standard root `<beans>`要素内の`<beans profile="value">`要素はネストしていることに注意してください。cloud profile内ののMongoDB connection factoryは、`<cloud:>`名前空間を使います。default profile内のconnection factory configurationは、`<mongo:>`名前空間を使います。このアプリケーションは異なる二つの環境で動作し、片方からもう片方へ切り替える際、手動で変更する必要はありません。

## <a id='services'></a>サービス特有の詳細 ##

続くセクションではCloud Foundryがサポートするサービス向けのSpringの自動設定と手動設定について説明します。

### <a id='rdbms'></a>MySQLとPostgres ###

#### 自動設定 ####

自動設定、Cloud FoundryがSpring application context内の`javax.sql.DataSource`
beanを検出した際に実行されます。以下のSpring application context
fileの一部はこのタイプのbeanの定義の例を示しています。Cloud Foundryはこれを検知し、自動設定を実行します:

~~~xml
<bean class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close" id="dataSource">
    <property name="driverClassName" value="org.h2.Driver" />
    <property name="url" value="jdbc:h2:mem:" />
    <property name="username" value="sa" />
    <property name="password" value="" />
</bean>
~~~ 

Cloud Foundryが実際に使うリレーショナル・データベースは、デプロイ時にバインドしたサービス・インスタンスに依存します:
MySQLかPostgresです。Cloud Foundryは、classpath上で見つけたデータソースの実装に応じて、DBCPまたはTomcat datasourceを作ります。

Cloud Foundryは内部的に以下の値を生成します。: `driverClassName`, `url`, `username`, `password`, `validationQuery`.

#### Javaでの手動設定 ####

Java configuration内でデータベース・サービスを定義するには、`@Configuration`クラスを作り、`@Bean`メソッドが`javax.sql.DataSource` beanを返すようにします。`cloudfoundry-runtime`ライブラリ内でヘルパー・クラスでこのbeanを作ることができます。以下に例を示します:

~~~java
@Configuration
public class DataSourceConfig {
    @Bean
    public DataSource dataSource() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RdbmsServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-postgres", RdbmsServiceInfo.class);
        RdbmsServiceCreator serviceCreator = new RdbmsServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }
}
~~~

#### XMLでの手動設定 ####

`<cloud:data-source>`要素は、SpringアプリケーションでJDBCを設定する簡単な方法を提供します。この後、実際にアプリケーションをデプロイする際、MySQLかPostgresのインスタンスをバインドしてみます。

##### 基本的な手動設定 #####

以下のSpring XML application context fileの一部は、`org.springframework.jdbc.core.JdbcTemplate` beanへのJDBCデータソースを設定する方法を示しています:

~~~xml
<cloud:data-source id="dataSource" />

<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
  <property name="dataSource" ref="dataSource" />
</bean>
~~~

こちらの例では、データソースに関する個々の値を指定していないことに注意してください。JDBC
driverのクラス名、データベースにアクセスするための具体的なURL、ユーザ名などです。その代わりに、Cloud
Foundryは実行時にバインドしたデータベースのサービスのタイプから適切な情報を持ってきます。

下の表は、`<cloud:data-source>`要素のアトリビュートの一覧です:

| Attribute | Description | Type |
|-----------|-------------|------|
| id | The ID of this data source. データソースを参照する際、JdbcTemplate
beanはこのIDを使います。デフォルトの値はバインドされたサービスのインスタンスの名前です。| String |
| service-name | The name of the data source service. 複数のデータベースのサービスがバインドされていて、どのSpring beanをどのサービへバインドしたいかを指定したい時にのみこのアトリビュートを指定します。デフォルトの値はバインドされたサービスのインスタンスの名前です。| String |

この設定では、実行時にもっとも一般的な設定を使ってデータソースを作ります。次のセクションでは、デフォルトを上書きするやり方を説明します。

##### 高度な手動設定 #####

`<cloud:data-source>`: `<cloud:connection>`と`<cloud:pool>`の二つの要素を使ってJDBCデータソースを設定することができます。

`<cloud:connection>`は、新しい接続が確立した時にJDBCドライバへ送りたいプロパティを格納した一つのString attribute (`properties`)を取ります。文字列のフォーマットは、セミコロンで区切られた名前と値の組になります。(たとえば、"`property1=value;property2=value`")

`<cloud:pool>` child elementは以下の属性を持つ:

| Attribute | Description | Type | Default |
|-----------|-------------|------|---------|
| pool-size | コネクション・プールのサイズ。接続の最大数か、最小と最大数をハイフンでつないで範囲を指定してください。| int | デフォルトの最小値は`0`. デフォルトの最大値は`8`. これはApache Commons Poolのデフォルトと同様です。|
| max-wait-time | 利用可能な接続がない時の待ち時間の最大値(ミリセカンド単位)。これを越えると例外を発生させる。`-1`を指定するとタイムアウトしない。 | int | デフォルトは `-1` (永遠に待つ). |

以下はadvanced data source configuration optionsの使い方の例です:

~~~xml
<cloud:data-source id="mydatasource">
  <cloud:connection properties="charset=utf-8" />
  <cloud:pool pool-size="5-10" max-wait-time="2000" />
</cloud:data-source>
~~~

上の例では、JDBCドライバにはUTF-8文字セットを指定するプロパティが渡されています。接続数の最小と最大は、それぞれ5と10です。接続の空きがない場合の待ち時間は2000ミリセカンド(2秒)で、その後は例外を投げます。

### <a id='mongodb'></a>MongoDB ###

#### 自動設定 ####

自動設定を使うには、[Spring Data MongoDB](http://www.springsource.org/spring-data/mongodb) 1.0 M4またはそれ以降を使う必要があります。

Cloud FoundryがSpring application context内に`org.springframework.data.document.mongodb.MongoDbFactory` beanを発見すると、自動設定が実行されます。以下のSpring XML application contextファイルの一部は、Cloud Foundryが感知すると自動設定がされるはずのbeanの例になっています:

~~~xml
<mongo:db-factory
    id="mongoDbFactory"
    dbname="pwdtest"
    host="127.0.0.1"
    port="1234"
    username="test_user"
    password="test_pass"  />
~~~ 

Cloud Foundryは`SimpleMongoDbFactory`を作り、以下のプロパティの値をセットします: `host`, `port`, `username`, `password`, `dbname`.

#### Javaでの手動設定 ####

Javaの設定内でMongoDBを設定するには、`org.springframework.data.mongodb.MongoDbFactory` beanを返す`@Bean`メソッドを持つ`@Configuration`クラスを作ってください。`cloudfoundry-runtime`ライブラリ内でヘルパー・クラスでこのbeanを作ることができます。以下に例を示します:

~~~java
@Configuration
public class MongoConfig {
    @Bean
    public MongoDbFactory mongoDbFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        MongoServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-mongodb", MongoServiceInfo.class);
        MongoServiceCreator serviceCreator = new MongoServiceCreator();
        return serviceCreator.createService(serviceInfo.get);
    }

    @Bean
    public MongoTemplate mongoTemplate() {
        return new MongoTemplate(mongoDbFactory());
    }
}
~~~

#### XMLでの手動設定 ####

`<cloud:mongo-db-factory>`名前空間を使って、SpringアプリケーションのためのMongoDB connection factoryを簡単に設定することができます。

##### 基本的な手動設定 #####

以下のSpring XML appication context fileの一部は、`org.springframework.data.mongodb.core.MongoTemplate` beanで使われる`MongoDbFactory` 設定の例です:

~~~xml
<cloud:mongo-db-factory id="mongoDbFactory" />

<bean id="mongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">
    <constructor-arg ref="mongoDbFactory"/>
</bean>
~~~

The following table lists the attributes of the `<cloud:mongo-db-factory>`
element.

<table>
<tbody>
  <tr>
    <th>Attribute</th>
    <th>Description</th>
    <th>Type</th>
  </tr>
  <tr>
    <td>id</td>
    <td>The ID of this MongoDB connection factory.  接続を参照する際、MongoTemplate beanはこのIDを使います。<br>デフォルトの値はバインドされたサービスのインスタンスの名前です。</td>
    <td>String</td>
  </tr>
  <tr>
  <td>service-name</td>
    <td>The name of the MongoDB service. <br>複数のMongoDBサービスがバインドされていて、どのSpring beanをどのサービス・インスタンスへバインドしたいかを指定したい時にのみこのアトリビュートを指定します。デフォルトの値はバインドされたサービスのインスタンスの名前です。
    <td>String</td>
  </tr>
  <tr>
    <td>write-concern</td>
    <td>Controls the behavior of writes to the data store.  このアトリビュートの値は、`com.mongodb.WriteConcern`クラスの値に対応します。       <p>このアトリビュートを指定しない場合、`WriteConcern`は設定されず、すべての書き込みのデフォルトはNORMALになります。</p>
       <p>このアトリビュートが取り得る値は以下になります:</p>
       <ul>
         <li><b>NONE</b>: 例外があがりません。</li>
         <li><b>NORMAL</b>: ネットワークについては例外をあげますが、サーバーに関しては例外をあげません。</li>
         <li><b>SAFE</b>: MongoDBサービスは書き込みの前にサーバーを待ちます。ネットワークとサーバーの両方について例外をあげます。</li>
         <li><b>FSYNC_SAVE</b>: MongoDBサービスは書き込み操作の前に、サーバーがデータをディスクへ実際に出力するのを待ちます。ネットワークとサーバーの両方について例外をあげます。</li>
       </ul>
     </td>
     <td>String</td>
   </tr>
</tbody>
</table>

これは、デフォルトの値を使ってMongoDB connection
factoryを作ります。以下のセクションでは、デフォルトを上書きするやり方を説明します。

##### 高度な手動設定 #####

`<cloud:mong-db-factory>`名前空間によって作られたconnection
factoryは、`<cloud:mongo-options>`要素を指定することによりカスタマイズできます。以下にMongoDBの拡張設定の例を示します:

~~~xml
<cloud:mongo-db-factory id="mongoDbFactory" write-concern="FSYNC_SAFE">
  <cloud:mongo-options connections-per-host="12" max-wait-time="2000" />
</cloud:mongo-db-factory>
~~~

上の例では、接続の数の最大値は12で、スレッドの待ち時間は1秒です。WriteConcernはいちばん安全な(FSYNC_SAFE)になっています。

The `<cloud:mongo-options>` child element takes the following attributes:

| Attribute | Description | Type | Default |
|-----------|-------------|------|---------|
| connections-per-host | MongoDBインスタンスへのホストごとの接続数の最大値。 これらの接続は、アイドル時はプール内に保持されます。プールが使いつくされると、接続を必要とする操作は接続が使えるようになるまでブロックされます。| int | 10 |
| max-wait-time | コネクションが利用可能になるまでの待ち時間の最大値(ミリセカンド単位) | int | 120,000 (2 minutes) |

### <a id='redis'></a>Redis ###

#### 自動設定 ####

自動設定を使うには、 [Spring Data Redis](http://www.springsource.org/spring-data/redis) 1.0 M4またはそれ以降を使う必要があります。

自動設定は、Cloud FoundryがSpring application context内の`org.springframework.data.redis.connection.RedisConnectionFactory` beanを検出した際に実行されます。以下のSpring XML application contextファイルの一部は、Cloud Foundryが感知すると自動設定がされるはずのbeanの例になっています:

~~~xml
<bean id="redis"
      class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
      p:hostName="localhost" p:port="6379"  />
~~~

Cloud Foundryは`JedisConnectionFactory`を作り、その時に以下のプロパティの値を使います: `host`, `port`, `password`. このことは、アプリケーション内でJedis JARパッケージ化する必要があることを意味します。Cloud Foundryは現時点ではJRedisとRJCの実装をサポートしていません。

#### Javaでの手動設定 ####

Javaの設定内でRedisサービスを設定するには、`org.springframework.data.redis.connection.RedisConnectionFactory` beanを返す`@Bean`メソッドを持つ`@Configuration`クラスを作ってください。`cloudfoundry-runtime`ライブラリ内でヘルパー・クラスでこのbeanを作ることができます。以下に例を示します:

~~~java
@Configuration
public class RedisConfig {
    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RedisServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-redis", RedisServiceInfo.class);
        RedisServiceCreator serviceCreator = new RedisServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }

    @Bean
    public RedisTemplate redisTemplate() {
        return new StringRedisTemplate(redisConnectionFactory());
    }
}
~~~

#### XMLでの手動設定 ####

The `<cloud:redis-connection-factory>` provides a simple way for you to
configure a Redis connection factory for a Spring application.

##### 基本的な手動設定 #####

以下のSpring XML application context
fileの一部は、`org.springframework.data.redis.core.StringRedisTemplate`
beanでの`RedisConnectionFactory`設定の例です:

~~~xml
<cloud:redis-connection-factory id="redisConnectionFactory" />

<bean id="redisTemplate" class="org.springframework.data.redis.core.StringRedisTemplate">
  <property name="connection-factory" ref="redisConnectionFactory"/>
</bean>
~~~

下の表は、`<cloud:redis-connection-factory>`要素のアトリビュートの一覧です:

<table>
<tbody>
<tr>
  <th>Attribute</th>
  <th>Description</th>
  <th>Type</th>
</tr>
<tr>
  <td>id</td>
  <td>The ID of this Redis connection factory.  接続を参照する際、RedisTemplate beanはこのIDを使います。<br>デフォルトの値はバインドされたサービスのインスタンスの名前です。</td>
  <td>String</td>
</tr>
<tr>
  <td>service-name</td>
  <td>The name of the Redis service. <br>複数のRedisサービスがバインドされていて、どのSpring beanをどのサービス・インスタンスへバインドしたいかを指定したい時にのみこのアトリビュートを指定します。デフォルトの値はバインドされたサービスのインスタンスの名前です。
  <td>String</td>
</tr>
</tbody> 
</table>

この設定では、実行時にもっとも一般的な設定を使ってfactoryを作ります。以下のセクションでは、デフォルトを上書きするやり方を説明します。

##### 高度な手動設定 #####

`<cloud:redis-connection-factory>`名前空間によって作られたconnection
factoryは、`<cloud:pool>`要素を指定することによりカスタマイズできます。

以下はadvanced Redis configuration optionsの使い方の例です:

~~~xml
<cloud:redis-connection-factory id="myRedisConnectionFactory">
  <cloud:pool pool-size="5-10" max-wait-time="2000" />
</cloud:redis-connection-factory>
~~~

これ例では、接続数の最小と最大は、それぞれ5と10です。接続の空きがない場合の待ち時間は2000ミリセカンド(2秒)で、その後は例外を投げます。

The `<cloud:pool>` child element takes the following attributes:

<table>
<tbody>
<tr>
  <th>Attribute</th>
  <th>Description</th>
  <th>Type</th>
  <th>Default</th>
</tr>
<tr>
  <td>pool-size</td>
  <td>Specifies the size of the connection pool.  接続の最大数か、最小と最大数をハイフンでつないで範囲を指定してください。
  <td>int</td>
  <td>Default minimum is 0.  Default maximum is 8. これはApache Commons Poolのデフォルトと同様です。
  <td>max-wait-time</td>
  <td>使える接続がない場合、このアトリビュートの値(ミリセカンド単位)の時間接続が使えるようになるのを待ち、その後例外を投げます。Specify `-1` to indicate that the connection pool should wait forever. </td>
  <td>int</td>
  <td>Default value is `-1` (forever).</td>
 </tr>
</tbody> 
</table>

### <a id='rabbitmq'></a>RabbitMQ ###

#### 自動設定 ####

自動設定を使うには、[Spring AMQP](http://www.springsource.org/spring-amqp)
1.0またはそれ以降を使う必要があります。Spring AMQP provides publishing, multi-threaded consumer generation, and message conversion. It also facilitates management of AMQP resources while promoting dependency injection and declarative configuration.

自動設定は、Cloud FoundryがSpring application context内の`org.springframework.amqp.rabbit.connection.ConnectionFactory` beanを検出した際に実行されます。以下のSpring application context fileの一部はこのタイプのbeanの定義の例を示しています。Cloud Foundryはこれを検知し、自動設定を実行します:

~~~xml
<rabbit:connection-factory
    id="rabbitConnectionFactory"
    host="localhost"
    password="testpwd"
    port="1238"
    username="testuser"
    virtual-host="virthost" />
~~~

Cloud Foundryは`org.springframework.amqp.rabbit.connection.CachingConnectionFactory`を作り、以下のプロパティの値を使います:
`host`, `port`, `username`, `password`, `dbname`.: `host`, `virtual-host`, `port`, `username`, `password`.

#### Javaでの手動設定 ####

To configure a RabbitMQ service in Java configuration, simply create a `@Configuration` class with a `@Bean` method to return a `org.springframework.amqp.rabbit.connection.ConnectionFactory` bean (from the Spring AMQP library). `cloudfoundry-runtime`ライブラリ内でヘルパー・クラスでこのbeanを作ることができます。以下に例を示します:

~~~java
@Configuration
public class RabbitConfig {
    @Bean
    public ConnectionFactory rabbitConnectionFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RabbitServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-rabbit", RabbitServiceInfo.class);
        RabbitServiceCreator serviceCreator = new RabbitServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }

    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        return new RabbitTemplate(connectionFactory);
    }
}
~~~

#### XMLでの手動設定 ####

`<cloud:rabbit-connection-factory>`は、SpringアプリケーションでRabbitMQ connection factoryを設定する簡単な方法を提供します。

##### 基本的な手動設定 #####

以下のSpring XML appication context fileの一部は、`rabbitTemplate` beanで使われるRabbitConnectionFactory configuration 設定の例です:この例は、 `<rabbit:>`名前空間を使ってRabbitMQ-specific configurationを行います。例の後に説明があります:

~~~xml
<!-- Obtain a connection to the RabbitMQ via cloudfoundry-runtime:
--> <cloud:rabbit-connection-factory id="rabbitConnectionFactory" />

<!-- Set up the AmqpTemplate/RabbitTemplate: -->
<rabbit:template id="rabbitTemplate"
    connection-factory="rabbitConnectionFactory" />

<!-- Request that queues, exchanges and bindings be automatically declared
on the broker: --> <rabbit:admin
connection-factory="rabbitConnectionFactory"/>

<!-- Declare the "messages" queue: --> <rabbit:queue name="messages"
durable="true"/>
~~~

The following table lists the attributes of the
`<cloud:rabbit-connection-factory>` element:

<table>
<tbody>
<tr>
  <th>Attribute</th>
  <th>Description</th>
  <th>Type</th>
</tr>
<tr>
  <td>id</td>
  <td>The ID of this RabbitMQ connection factory. 接続を参照する際、RabbitTemplate beanはこのIDを使います。<br>デフォルトの値はバインドされたサービスのインスタンスの名前です。</td>
  <td>String</td>
</tr>
<tr>
  <td>service-name</td>
  <td>The name of the RabbitMQ service. <br>複数のRabbitMQサービスがバインドされていて、どのSpring beanをどのサービス・インスタンスへバインドしたいかを指定したい時にのみこのアトリビュートを指定します。デフォルトの値はバインドされたサービスのインスタンスの名前です。
  <td>String</td>
</tr>
</tbody>
</table>

これは、デフォルトの値を使ってRabbitMQ connection
factoryを作ります。以下のセクションでは、デフォルトを上書きするやり方を説明します。

##### 高度な手動設定 #####

`<cloud:rabbit-connection-factory>`名前空間によって作られたconnection
factoryは、`<cloud:rabbit-options>`要素を指定することによりカスタマイズできます。

以下はadvanced RabbitMQ configuration optionsの使い方の例です:

~~~xml
<cloud:rabbit-connection-factory id="rabbitConnectionFactory" >
  <cloud:rabbit-options channel-cache-size="10" />
</cloud:rabbit-connection-factory>
~~~

`<cloud:rabbit-options>`要素は、`channel-cache-size`というアトリビュートを定義し、チャンネルのキャッシュ・サイズの大きさを指定します。デフォルトの値は1です。前の例では、RabbitMQ connection factoryのキャッシュ・サイズは10です。

