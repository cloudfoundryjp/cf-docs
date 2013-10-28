---
title: アプリのデプロイ
---

Cloud Foundryは多くのフレームワークやランタイムをサポートしています。

| ランタイム        | フレームワーク                                                                             |
| :------------- | :-------------                                                                        |
| Javascript     | [Node.js](/docs/using/deploying-apps/javascript/index.html)                           |
| Java / JVM     | [Java Spring, Grails, Scala Lift, Play](/docs/using/deploying-apps/jvm/index.html)|
| Ruby           | [Rack, Rails, or Sinatra](/docs/using/deploying-apps/ruby/index.html)                 |

ビルドパック・モデルを使ってCloud Foundry 以下のフレームワークとランタイムをサポートします。<a href="https://devcenter.heroku.com/articles/third-party-buildpacks">Heroku
third party buildpacks</a>のいくつかは動作するでしょうが、動かないものもあるでしょう。これらのビルドパックの一つを用いてアプリケーションをプッシュするには、`cf
push [appname] --buildpack=[git url]`を実行してください。

あなた独自のカスタム・ビルドパックを作ることができます。[独自のビルドパック](buildpacks.html)

デプロイの手順はどのCloud Foundryを使っているかにより異ります。

run.pivotal.ioについては、こちらの文書をご参照ください。 [入門](../../dotcom/getting-started.html)
