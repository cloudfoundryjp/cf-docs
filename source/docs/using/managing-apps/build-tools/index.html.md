---
title: ビルド・ツールへの統合
---

アプリケーションをデプロイするのに別のツールを使うこともできます - MavenとGradleの二つです。

## <a id='gradle'></a>Gradle ##

Gradleはビルド・ツールの一つです。ソフトウェア・パッケージのビルド、テスト、公開、デプロイを自動化します。静的ウェブサイトの生成やドキュメントの生成、さらにその他の機能があります。

`cf-gradle-plugin`はGradleプロジェクトにCloud Foundly向けの機能を追加します。

### <a id="gradle-install"></a> プラグインのインストール ###

Cloud Foundryプラグインを使ったGradleプロジェクトの例を示します。(build.gradle):

~~~

buildscript {
  repositories {
    mavenCentral()
  }
  dependencies {
    classpath group: 'org.cloudfoundry', name: 'cf-gradle-plugin', version: '1.0.0'
  }
}

apply plugin: 'cloudfoundry'

cloudfoundry {
    target = 'https://api.run.pivotal.io'
    space = 'development'
    username = 'user@example.com'
    password = 's3cr3t'
    file = new File('build/libs/my-app.war')
    uri = 'http://my-app.run.pivotal.io'
    env = [
        "key": "value"
    ]
}
~~~

プラグインの追加と設定の後、アプリケーションを作成しCloud Foundryへプッシュすることがけいます:

<pre class="terminal">
$ gradle clean assemble cf-push
</pre>

Gradleでの設定とオプションについては、Githubの [cf-gradle-plugin project](https://github.com/cloudfoundry/cf-java-client/tree/master/cloudfoundry-gradle-plugin)をご覧ください。

## <a id='maven'></a>Maven ##


`cf-maven-plugin` Mavenプラグインを使えば、Mavenから直接アプリケーションをデプロイできます。Cloud Foundryアプリケーションのマニフェストをpom.xml書いておけるので、有益です。

### <a id='maven-install'></a>プラグインのインストール ###

以下の内容を`pom.xml`の`plugins`ノードへ追加してください:

~~~xml
<plugin>
  <groupId>org.cloudfoundry</groupId>
  <artifactId>cf-maven-plugin</artifactId>
  <version>1.0.0</version>
<configuration>
      <server>mycloudfoundry-instance</server>
      <target>http://api.run.pivotal.io</target>
      <url>hello-java-maven.cfapps.io</url>
      <memory>256</memory>
  </configuration>
</plugin>
~~~

サーバー名、ターゲットとなるCloud Foundryサーバーのアドレス、アプリケーションのURLとメモリの使用量を指定します。

~/.m2/settings.xmlを作成するか、既存なら編集して、以下を追加します:

~~~xml

<settings>
    ...
    <servers>
        ...
        <server>
          <id>mycloudfoundry-instance</id>
          <username>user@example.com</username>
          <password>mypassword</password>
        </server>
    </servers>
    ...
</settings>
~~~

server/idノードへ`pom.xml`ファイルで指定したサーバ名を設定します。また、該当アカウントのユーザ名とパスワードを設定します。

Mavenをパッケージへ適用し、デプロイできます。

<pre class="terminal">
$ mvn clean package
$ mvn cf:push
</pre>

