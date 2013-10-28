---
ビルド・ツールへの統合

アプリケーションをデプロイするのに別のツールを使うこともできます - MavenとGradleの二つです。

> ** GradlとMaven用プラグインは数週以内に更新されます。Cloud Foundry
v2のサポートのためです。オーガナイゼーション、スペース、独自のビルドパックも含まれます **

## <a id='gradle'></a>Gradle ##

Gradleはビルド・ツールの一つです。ソフトウェア・パッケージのビルド、テスト、公開、デプロイを自動化します。静的ウェブサイトの生成やドキュメントの生成、さらにその他の機能があります。

gradle-cf-pluginはGradleプロジェクトにCloud Foundly向けの機能を追加します。

### <a id="gradle-install"></a> Install the plugin ###

Cloud Foundryプラグインを使ったGradleプロジェクトの例を示します。(build.gradle):

~~~

buildscript {
  repositories {
    mavenCentral()
    maven { url "http://repo.springsource.org/libs-milestone-s3-cache" }
  }
  dependencies {
    classpath group: 'org.gradle.api.plugins', name: 'gradle-cf-plugin', version: '0.2.0'
  }
}

apply plugin: 'cloudfoundry'

cloudfoundry {
  username = 'jondoe@gmail.com'
  password = 'mypassword'
  application = 'test_grails_app'
  framework = 'grails'
  file = new File('/path/to/app.war')
  uris = ['http://grails-test.cloudfoundry.com']
}

~~~

プラグインの追加後、タスクの実行で以下のような出力が含まれているはずです:

<pre class="terminal">

$ gradle tasks :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

CloudFoundry tasks
------------------
cf-add-service - サービスを作る。アプリケーションにバインドすることもできる cf-add-user - 新規ユーザを登録する
cf-apps - アプリケーションの一覧を表示する cf-bind - サービスをアプリケーションへバインドする cf-delete-app -
アプリケーションを削除する cf-delete-service - サービスを削除する cf-delete-user - ユーザを削除すUses the
current credentials!cf-info - 指定されているCloudFoundryの情報を表示する cf-login - ログインする
cf-push - アプリケーションをプッシュする cf-restart - アプリケーションを再起動する cf-start -
アプリケーションを起動する cf-status - アプリケーションの情報を表示する cf-stop - アプリケーションを停止する cf-unbind
- サービスをアプリケーションから切り離す cf-update - デプロイ済のアプリケーションを更新する

Help tasks
----------
dependencies - Displays all dependencies declared in root project
'hello-world'.  dependencyInsight - Displays the insight into a specific
dependency in root project 'hello-world'.  help - ヘルプ・メッセージを表示する projects -
rootプロジェクト'hello-world'の情報を表示するproperties -
rootプロジェクト'hello-world'のプロパティを表示するtasks -
rootプロジェクト'hello-world'から使えるタスクを表示する (サブプロジェクトのタスクも表示され得る)

すべてのタスクと詳細を見るには --allをつけて実行してください。

BUILD SUCCESSFUL

Total time: 2.543 secs </pre>

この時点から、Gradle内からcfの一部としてほとんどのタスクが使えるようになる。Gradleの設定について、Githubのgradle-cf-plugin
- https://github.com/melix/gradle-cf-plugin をご覧ください。

## <a id='maven'></a>Maven ##


cf-maven-pluginを使えば、Mavenからアプリケーションを直接デプロできます。Cloud Foundryアプリケーションのマニフェストを
pom.xml書いておけるので、有益です。

### <a id='maven-install'></a>Install the Plugin ###

以下の内容を`pom.xml`の`plugins`ノードへ追加してください:

~~~xml
<plugin>
  <groupId>org.cloudfoundry</groupId>
  <artifactId>cf-maven-plugin</artifactId>
  <version>1.0.0.M4</version>
  <configuration>
      <server>mycloudfoundry-instance</server>
      <target>http://api.cloudfoundry.com</target>
      <url>hello-java-maven.cloudfoundry.com</url>
      <framework>lift</framework>
      <memory>256</memory>
  </configuration>
</plugin>
~~~

サーバー名、ターゲットとなるCloud Foundryサーバーのアドレス、アプリケーションのurlとメモリの使用量を指定します。

以下をpluginRepositories nodeへ追加します:

~~~xml
<pluginRepository>
    <id>repository.springframework.maven.milestone</id>
    <name>Spring Framework Maven Milestone Repository</name>
    <url>http://maven.springframework.org/milestone</url>
</pluginRepository>
~~~

~/.m2/settings.xmlを作成するか、既存なら編集して、以下を追加します:

~~~xml

<settings>
    ...
    <servers>
        ...
        <server>
          <id>mycloudfoundry-instance</id>
          <username>demo.user@vmware.com</username>
          <password>s3cr3t</password>
        </server>
    </servers>
    ...
</settings>
~~~

server/idノードへpom.xmlファイルで指定したサーバ名を設定します。また、該当アカウントのユーザ名とパスワードを設定します。

Mavenをパッケージへ適用し、デプロイします。

<pre class="terminal"> $ mvn clean package $ mvn cf:push </pre>

