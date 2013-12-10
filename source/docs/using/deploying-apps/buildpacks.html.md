---
title: ビルドパック
---

Cloud Foundryはフレームワークやランタイム特有のビルドパックを使ってアプリケーションを公開します。Herokuがビルドパックを使うやり方を公開し、OSSコミュニティが使用できるようにしました。

* **Cloud Foundry ビルドパック** ---  Cloud Foundryがサポートしているランタイムとフレームワーク上で動作しているアプリケーションをプッシュする時、適切なビルドパックが自動的に適用されます。各ビルドパックについては次をご参照ください。

     * [Java Buildpack](/docs/using/deploying-apps/java-buildpack.html)
     * [Ruby Buildpack](/docs/using/deploying-apps/ruby-buildpack.html)
     * [Node.js Buildpack](/docs/using/deploying-apps/node-buildpack.html)

<br>

* **外部のビルドパック** --- あなたのアプケーションがCloud Foundryがサポートしていない言語やフレームワークを使っている場合、サード・パーティやコミュニティのビルドパックが使えるかもしれません。

    * [Cloud Foundryコミュニティによるビルドパック](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks) --- このページにはCloud Foundryコミュニティのメンバーから寄贈されたビルパックへのリンクが載っています。

    * [Herokuのサード・パーティのビルドパック](https://devcenter.heroku.com/articles/third-party-buildpacks) for a list of community-developed buildpacks. --- このページにはHerokuによって開発されたビルドパックへのリンクが載っています。これらはCloud Foundryでも使える可能性がありますが、動作は未確認です。
    * [カスタム・ビルドパック](/docs/using/deploying-apps/custom-buildpacks.html) --- カスタム・ビルドパックの作り方についてはこちらのページをご覧ください。


      Cloud Foundryに含まれていないビルドパックを使うには、アプリケーションをプッシュする時に`--buildpack`オプションを使ってビルドパックのURLを指定します。

