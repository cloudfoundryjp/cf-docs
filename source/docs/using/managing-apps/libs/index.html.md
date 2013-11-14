---
title: APIとライブラリ
---

### REST API ###

Cloud Controllerコンポーネントは、Cloud Foundry環境を管理するREST APIの実装です。REST APIのドキュメントは[reference section](/docs/reference/cc-api.html)にあります。

Cloud ControllerのREST APIへ接続する言語ごとのライブラリが利用可能です。

### Java client library (vcap-java-client) ###

vcap-java-clientライブラリは、Cloud Foundryインスタンスと連携するJava
APIを提供します。このライブラリは以下のものでも使われているます。[Cloud Foundry Maven plugin](../build-tools/maven.html)、 [Cloud Foundry Gradle plugin](../build-tools/gradle.html)、[Cloud Foundry STS integration](../ide/sts.html)、および他のJavaベースのツール。

このライブラリの使用法については[Java Cloud Foundry Library](./java-client.html)をご覧ください。

### Ruby gem (cfoundry) ###

CFoundryは[cf](/docs/using/managing-apps/cf/index.html)コマンドと同じライブラリです。詳細については、[RubyDoc](http://rubydoc.info/gems/cfoundry)をご覧ください。

Cloud Foundry上のアプリケーションの管理については、[CFoundry Ruby Gem](./ruby-cfoundry.html)ページをご覧ください。

