---
title: Node.jsのデプロイ
---

Node.jsアプリのデプロイの準備については右のページをご欄ください。
[入門](../../../dotcom/getting-started.html).

## <a id='packagejson'></a>アプリケーション・パッケージ・ファイル ##

Node.jsアプリケーション内に`package.json`が必要です。Node.jsのヴァージョンを`package.json`内の
`engine`ノードで指定できます。July, 2013以降、Cloud Foundryのデフォルトは0.10.xです。

## <a id='start'></a>アプリケーション起動コマンド ##

Node.jsは起動コマンドを必要とし、それは`manifest.yml`に格納されます。

最初にデプロイする時、設定を保存するかきかれます。これにより、最初のプッシュの時に入力した設定がアプリケーション内の`manifest.yml`へ格納されます。`manifest.yml`を編集し、以下のように起動コマンドを記述してください:

~~~yaml
---
applications:
- name: my-app
  command: node my-app.js
... the rest of your settings  ...
~~~

あるいは、`cf push --command`とオプションを指定してください。

<pre class="termainl"> $ cf push --command "node my-app.js" </pre>

## <a id='nodemodules'></a>アプリケーションのバンドリング ##

アプリケーションをデプロイする前に`npm install`を実行する必要はありません。アプリケーションをプッシュした時に、Cloud
Foundryがそのコマンドを実行します。`npm install`を自分で実行し、`node_modules`フォルダを作っても問題ありません。

## <a id='buildpack'></a> Node.jsビルドパック ##

Cloud
FoundryがNode.jsのアプリケーションと認識しない場合でも、Node.jsビルドパックを指定することにより認識させることができます。

どちらの場合でも、ビルドパックを`manifest.yml`で指定し、`cf push --reset`を実行してください。

~~~yaml
---
applications:
- name: my-app
  buildpack: https://github.com/cloudfoundry/heroku-buildpack-nodejs.git
... the rest of your settings  ...
~~~

あるいは、`cf push --buildpack`とオプションを指定してください。

<pre class="termainl"> $ cf push --buildpack
https://github.com/cloudfoundry/heroku-buildpack-nodejs.git </pre>

## <a id='services'></a>サービスをバインドするに##

[node.jsとサービスのバインド](../../services/node-service-bindings.html)をご参照ください。
