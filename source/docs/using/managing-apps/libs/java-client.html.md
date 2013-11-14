---
title: Java Cloud Foundryクライアント
---

## <a id='intro'></a>紹介 ##

Cloud Foundryインスタンス上のアカウントを管理するJava Cloud Foundryクライアントの使い方を説明します。

## <a id='getting'></a>ライブラリの入手 ##

Java Cloud Foundryライブラリは、Spring Framework
milestoneリポジトリから入手できます。以下のような情報があれば、MavenまたはGradleプロジェクトへの従属としてこのライブラリを追加できます。

### <a id='maven'></a>Maven ###

MavenプロジェクトでJavaクライアントを使うには、まず`pom.xml`ファイルへSpring Framework milestoneリポジトリを追加します。`<repository>`セクションへ、以下のように追加してください:

~~~xml
  <repositories>
    <repository>
      <id>repository.springframework.milestone</id>
      <name>Spring Framework Milestone Repository</name>
      <url>http://repo.springframework.org/milestone</url>
    </repository>
  </repositories>
~~~

リポジトリを`pom.xml`へ追加した後、依存関係を以下のように追加します:

~~~xml
  <dependencies>
    <dependency>
      <groupId>org.cloudfoundry</groupId>
      <artifactId>cloudfoundry-client-lib</artifactId>
      <version>0.8.4</version>
    </dependency>
  </dependencies>
~~~ 

### <a id='gradle'></a>Gradle ###

GradleプロジェクトでJavaクライアントを使うには、次のようにSpring Framework
milestoneリポジトリを`build.gradle`ファイルへ追加してください。:

~~~groovy
repositories {
  mavenCentral()
  mavenRepo url:'http://repo.springframework.org/milestone/'
}

dependencies {
  compile 'org.cloudfoundry:cloudfoundry-client-lib:0.8.4'
} 
~~~

## <a id='sample'></a>Sample Code ##

以下はとてもシンプリなサンプル・アプリケーションです。これはCloud Foundryインスタンスへ接続し、ログインし、アカウントに関する情報を表示します。このプログラムを実行する時は、適切なターゲット(i.e.  http://api.run.pivotal.io)とユーザ名とパスワードをコマンド・ラインのパラメータとして与えてください。

~~~java
package org.cloudfoundry.sample;

import org.cloudfoundry.client.lib.CloudCredentials; import
org.cloudfoundry.client.lib.CloudFoundryClient; import
org.cloudfoundry.client.lib.domain.CloudApplication; import
org.cloudfoundry.client.lib.domain.CloudService;

import java.net.URI; import java.net.URL;

public class JavaSample {
    public static void main(String[] args) {
        String target = args[0];
        String user = args[1];
        String password = args[2];

        CloudCredentials credentials = new CloudCredentials(user, password);
        CloudFoundryClient client = new CloudFoundryClient(credentials, getTargetURL(target));
        client.login();
        
        System.out.println("\nSpaces:");
        for (CloudSpace space : client.getSpaces()) {
            System.out.println(space.getName() + ":" + space.getOrganization().getName());
        }

        System.out.println("\nApplications:");
        for (CloudApplication app : client.getApplications()) {
            System.out.println(app.getName());
        }

        System.out.println("\nServices");
        for (CloudService service : client.getServices()) {
            System.out.println(service.getName() + ":" + service.getLabel());
        }
    }

    private static URL getTargetURL(String target) {
        try {
            return new URI(target).toURL();
        } catch (Exception e) {
            System.out.println("The target URL is not valid: " + e.getMessage());
        }
        System.exit(1);
        return null;
    }
}
~~~

Java APIの詳細については[source on GitHub](https://github.com/cloudfoundry/vcap-java-client/tree/master/cloudfoundry-client-lib)をご覧ください。[domain package](https://github.com/cloudfoundry/vcap-java-client/tree/master/cloudfoundry-client-lib/src/main/java/org/cloudfoundry/client/lib/domain)が読み書きできるオブジェクトを示しています。

[Cloud Foundry Maven plugin](https://github.com/cloudfoundry/vcap-java-client/tree/master/cloudfoundry-maven-plugin)のソースがJava client libraryの使い方のサンプルにもなっています。
