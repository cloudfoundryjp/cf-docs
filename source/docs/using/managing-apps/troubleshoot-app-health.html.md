---
title: アプリケーションのデプロイとヘルスのトラブルシューティング
---

## <a id='cf-commands'></a>cfトラブルシューティングのコマンド ##

Cloud Foundryのcfコマンド・ライン・インタフェースはデプロイしたアプリケーションの調査や状態を調べる様々なコマンドを備えています。


* `cf apps` --- 現在のスペースにデプロイされたアプリケーションの一覧を表示します。インスタンスの数やメモリとディスクの割当てなどのデプロイ時のオプション、各アプリケーションの現在の状態も含みます。使い方については[cf Command Line Interface](/docs/using/managing-apps/cf/index.html)ページの[apps](/docs/using/managing-apps/cf/index.html#apps)セクションをご覧ください。

* `cf app` --- 一つのアプリケーションの各インスタンスの状態を返します。現在の状態、稼動時間、CPU、メモリ、ディスクの使用量を含みます。使い方については[app](/docs/using/managing-apps/cf/index.html#app)をご覧ください。
                      
* `cf logs` --- 環境変数、ステージングのログ、最近のSTDOUTとSTDERRを見ることができます。`VCAP_SERVICES`がバインドされたサービスや利用者情報の一覧を保持しています。

  あなたのアプリケーションをSTDOUTやSTDERRへメッセージを出力するように設定する必要がある場合があります。もしlog4jを使ってログをSTDOUTへ送るようにしていれば、`logs`コマンドで見ることができます。(STDOUTやSTDERRでなくファイルへメッセージを送るようになっているのなら、`cf file`コマンドを使ってください)

  **注意:**  `cf logs`は利用者情報も返すため、公開する場合は大事な情報を削除してからにしましょう。

  使い方については[logs](/docs/using/managing-apps/cf/index.html#logs)をご覧ください。

* `cf events` --- このコマンドはアプリケーションがクラッシュした時の情報を返します。エラー・コードも含みます。Cloud Foundryのエラーについては https://github.com/cloudfoundry/errors をご覧ください。使い方については[events](/docs/using/managing-apps/cf/index.html#eventss)をご覧ください。

* `cf files`と`cf file`--- アプリケーションのディレクトリのファイル一覧やファイルの内容を見ることができます。使い方については[files](/docs/using/managing-apps/cf/index.html#files)と[file](/docs/using/managing-apps/cf/index.html#file)をご覧ください。

* `cf guid` --- アプリケーションのguidを返します。使い方については[guid](/docs/using/managing-apps/cf/index.html#guid)をご覧ください。

* `cf stats` --- アプリケーションの各インスタンスのリソース使用状況を表示します。CPU、メモリ、ディスクの使用状況を含みます。使い方については[stats](/docs/using/managing-apps/cf/index.html#stats)をご覧ください。

## <a id='java-apps'></a>JavaとGrailsのお勧めの使い方 ##

### <a id='jdbc'></a>JDBCドライバの提供 ###

JavaビルドパックはJDBCドライバを提供しません。あなたのアプリケーションがSQL RDBMSへアクセスするなら、適切なドライバをアプリケーションに含めてください。
 
### <a id='memory'></a>適切なメモリの割当 ###

デプロイ時にJavaアプリケーションに適切なメモリを割当てなかった場合、起動できないかCloud Foundryによって停止させられます。Javaのヒープ、PermGen、スレッド・スタック、JVMのための余裕などとして十分なメモリを割当ててください。JavaビルドパックはPermGenのメモリ上限の10%を確保します。JVMのメモリ関連のオプションは`config/openjdk.yml`内で設定されます。(https://github.com/cloudfoundry/java-buildpack/blob/master/config/openjdk.yml)動作中のアプリケーションについて、`cf stats`コマンドでメモリの使用率を見ることができます。

### <a id='upload'></a>アップロードの失敗のトラブルシュート ###

アプリケーションをCloud Foundryへプッシュする時のアップロードに失敗した場合、以下の理由が考えられます:

* WARが大きすぎる --- WARファイルの大きさのせいでアップロードが失敗することがあります。Cloud Foundryの試験では250MBのアップロードに成功しています。それより大きなWARファイルなら、大きさのせいで失敗が起こり得ます。

* 接続の問題 --- インターネット接続が低速だったり、ターゲットのCloud Foundryが非常に遠いところにある場合にアップロードに失敗することがあります。アップロードに時間がかかりすぎると、アップロードが終わる前に認証トークンが期限切れになってしまうことがあり得ます。応急措置として、Cloud Foundryインスタンスに近いサーバへWARファイルをコピーし、そこからプッシュすることが挙げられます。

* 古いクライアント --- より新しいcfコマンドを使った方が大きなWARファイルを高速でアップロードでき、失敗しにくくなります。大きなファイルのアップロードで問題があり、古いcfを使っているのなら、cfを最新版にしてみたください:

  <pre class="terminal">
  $ gem update cf
  </pre>

* まちがったWARターゲッティング --- デフォルトでは、`cf push`はすべてのファイルをカレント・ディレクトリへアップロードします。Javaアプリケーションについて、`cf push`はWARだけでなくソース・コードやその他の不要なファイルもアップロードします。Javaアプリケーションをアップロードする時は、WARのパスを指定しましょう:

  <pre class="terminal">
  cf push --path PathToWAR
  </pre>

デプロイのマニフェスト`manifest.yml`を見て、前回のプッシュ時にパスが指定されたかどうか知ることができます。パスがカレント・ディレクトリを指定していた場合、マニフェストは以下のような行を含みます:

 `path: .` 

 WARだけをプッシュするには:

 * `manifest.yml`を削除し、`--path`でWARの場所を指定して再度プッシュします。あるいは

 * `manifest.yml`内の`--path`でWARを指定し、再度プッシュします。

### <a id='slow-start'></a>Slow Starting Java or Grails Apps ###

 JavaやGrailsのアプリケーションでは、起動に時間がかかるのは珍しいことではありません。このことと現在のJavaビルドパックの実装から、問題が起きることがあります。JavaビルドパックはTomcatの`bindOnInit`属性を"false"に設定します。これによりTomcatはアプリケーションがデプロイされるまでHTTPリクエストを受信しません。アプリケーションの起動に時間がかかると、DEAのヘルス・チェックが失敗することがあります。応急措置として、Javaビルドパックを複製し、`bindOnInit` を"false"へ変更します。

## <a id='node-apps'></a>Nodeのベスト・プラクティス ##

* Node.jsアプリケーションのweb起動コマンドをプロファイルかデプロイのマニフェストで指定します。

## <a id='ruby-apps'></a>Rubyのベスト・プラクティス ##

* プレコンパイル・アセット --- ステージングの時間短縮のためにお勧めです。

* Rails 4では`rails_serv_static_assets`を使ってください --- デフォルトでは、Nginxなどの外部プロキシ経由ではないアセットに対してRails 4は404を返します。`rails_serve_static_assets` gemはRailsサーバが404を返すのではなくスタティック・アセット・ディレクトリを提供できるようにします。この機能によりエッジ・キャッシュCDNを満たしたりwebアプリケーションから直接ファイルを提供したりできます。`rails_serv_static_assets` gemは、`config.serve_static_assets`オプションを"true"にするとこのように振舞います。手動で設定する必要はありません。 
