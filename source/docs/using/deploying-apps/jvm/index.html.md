---
title: JVMアプリ(Java, Groovy, Scala)をデプロイする
---

ここでは、右のページ[getting started
guide](../../../dotcom/getting-started.html)を通じて、Java, Groovy,
Scalaなどの言語とSpring, Grails, Lift, Playなどのフレームワークのアプリケーションをデプロイする準備をします。

## <a id='war'></a>warファイルのビルド ##

Spring, Grails, Lift,
Playのアプリケーションをデプロイする場合、コンパイルしてwarファイルにする必要があります。warファイルのパスを`cf`コマンドの`--path`オプションで指定します。さまざまなフレームワークを用いたいくつかの例を示します:

Spring with Maven

<pre class="terminal"> $ mvn package $ cf push --path
target/my-app-1.0.0.war </pre>

Spring with Gradle

<pre class="terminal"> $ gradle assemble $ cf push --path
build/libs/my-app-1.0.war </pre>

Grails

<pre class="terminal"> $ grails prod war $ cf push --path
target/my-app-1.0.0.war </pre>

Play

<pre class="terminal"> $ play dist $ cf push --path dist/my-app-1.0.war
</pre>

Lift

<pre class="terminal"> $ mvn package $ cf push --path
target/my-app-1.0.0.war </pre>

## <a id='services'></a>サービスとのバインド ##

アプリとサービスのバインドについては、以下のページに情報があります:
 
* [Spring](../../services/spring-service-bindings.html)
* [Grails](../../services/grails-service-bindings.html)
* [Lift](../../services/lift-service-bindings.html)

