---
title: Cloud Foundry Java クライアント・ライブラリ
---

## <a id='intro'></a>はじめに ##

Cloud Foundryインスタンス上のアカウントを管理するCloud Foundry Javaクライアント・ライブラリの使い方を説明します。

## <a id='getting'></a>ライブラリの入手 ##

Cloud Foundry Javaクライアント・ライブラリは、Maven Centralから入手できます。以下のような情報があれば、MavenまたはGradleプロジェクトへの従属としてこのライブラリを追加できます。

### <a id='maven'></a>Maven ###

以下のように`pom.xml`へ`cloudfoundry-client-lib`の依存関係を追加できます:

~~~xml
  <dependencies>
    <dependency>
      <groupId>org.cloudfoundry</groupId>
      <artifactId>cloudfoundry-client-lib</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
~~~ 

### <a id='gradle'></a>Gradle ###

GradleプロジェクトでJavaクライアント・ライブラリを使うには、次のように`cloudfoundry-client-lib`の依存関係を`build.gradle`ファイルへ追加してください。:

~~~groovy
repositories {
  mavenCentral()
  mavenRepo url:'http://repo.springframework.org/milestone/'
}

dependencies {
  compile 'org.cloudfoundry:cloudfoundry-client-lib:1.0.0'
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

Cloud Foundry Javaクライアント・ライブラリについて、詳しくは[source on GitHub](https://github.com/cloudfoundry/cf-java-client/tree/master/cloudfoundry-client-lib). The [domain package](https://github.com/cloudfoundry/cf-java-client/tree/master/cloudfoundry-client-lib/src/main/java/org/cloudfoundry/client/lib/domain)をご覧ください。問合せや検査可能なオブジェクトを示しています。

[Cloud Foundry Maven plugin](https://github.com/cloudfoundry/cf-java-client/tree/master/cloudfoundry-maven-plugin)のソースも使い方の良い例です。
