---
title: アプリケーションのマニフェスト
---

マニフェストはアプリケーションのデプロイの設定を定義します。例として、アプリケーションの名前、インスタンスの数、一つのインスタンスが使えるメモリ量の上限、使用するサービスなどが挙げられます。マニフェストの名前のデフォルトはmanifest.ymlです。

## <a id='purpose'></a>マニフェスト・ファイルの目的 ##


マニフェストの目的は、アプリケーションのデプロイを自動化することです - デプロイの設定をファイルに書くことができ、コマンド・ラインで指定しなくて済みます。cf pushコマンドで指定できるオプションは、すべてマニフェストで定義できます。cf pushコマンドが入力を求めないような設定もマニフェストに書くことができます。   


## <a id='sample'></a>マニフェストの簡単な例 ##

以下のマニフェストの例は、"nodetestdh01"という名前のNode.jsアプリケーションのデプロイの設定を定義しています。このマニフェストを使ってアプリケーションをプッシュすると、二つのインスタンスが作られます。アプリケーションのURLはcrn.csapps.ioになります。

~~~
applications:
- name: nodetestdh01
 runtime: node08
 mem: 64M
 instances: 2
 host: crn
 domain: csapps.io 
 path: .
~~~

マニフェストはYAML形式で記述されます。YAMLについては、www.yaml.orgをご参照ください。

## <a id='push-and-manifest'></a>cfのプッシュとマニフェスト ##

アプリケーションがプッシュされた時、cfはmanifest.ymlというファイルを探します。プッシュ・コマンドはカレント・ディレクトリ内でマニフェストを見つけようとします。見つからなければ、アプリケーションのルート上のディレクトリ内を探します。マニフェストのファイル名がmanifest.ymlでなければ、--manifest
(-m)オプションでファイル名を指定します。以下がその例です:

<pre class="terminal"> cf push -m prod-manifest.yml </pre>

注意:cfコマンドは以下の場合はマニフェストを探しません:

* cf pushコマンドで--interactiveオプションを使った場合、cfコマンドはマニフェストを探さず、デプロイの設定の入力を求めます。
* cf pushコマンドの--no-manifestオプションを使った場合、マニフェストが存在しても無視されます。

初めてあるアプリケーションをプッシュする場合、マニフェストが存在すればcfコマンドはその内容をデプロイの設定として使用します。

マニフェストが見つけられない場合、cfコマンドは必要なデプロイの設定の入力を求めます。以下のセクションで記述するように、必要な項目が入力されると、cfコマンドはマニフェストとして保存するかたずねます。

あるアプリケーションの2回目以降のデプロイでは、cf
pushコマンドはマニフェスト内の設定を反映しません。その代わりに、直近の設定を使用します。例えば、マニフェスト内でinstances:
1”と指定されていて、プッシュ後にcf
scaleコマンドでインスタンス数を2にした場合、次にアプリケーションをプッシュするとインスタンス数は2になります。マニフェスト内の設定を反映させるには、--resetオプションを使う必要があります。


## <a id='create'></a>マニフェストの作り方 ##

マニフェストを作る方法は二通りあります:

* 自動で作成 -
  あるアプリケーションを初めてプッシュする時(マニフェストがないとして)、自動的に作成できます。コマンドのオプションか対話型の入力で指定します。cfコマンドが設定をマニフェストとして保存するか聞いてきます。“yes”と答えると、アプリケーションのルート・ディレクトリにmanifest.ymlというファイル名で設定が保存されます。

* 手動で作成 – テキスト・ファイルに設定を書き込んで作成します。マニフェストをプロジェクトのルート・ディレクトリまたはルートに置きます。


## <a id='manual'></a>マニフェスト内の必須項目 ##

いくつかの設定項目はマニフェストでのみ指定できます。例:

* Rubyのシンボル –
  Rubyのシンボルを使いたい場合、マニフェスト内で定義する必要があります。[マニフェスト内でのシンボル](#symbols)をご参照ください。

* 環境変数 – 環境変数を使いたいなら、マニフェスト内で定義する必要があります。[マニフェスト内での環境変数](#vars)をご参照ください

* 複数のアプリケーション向けのマニフェスト –
  一回のプッシュ・コマンドで複数のアプリケーションをデプロイしたい場合、マニフェスト・ファイルを編集して個々のアプリケーションの設定を定義する必要があります。複数のアプリケーション向けのマニフェストでは、アプリケーションの依存関係を定義することができます。[依存関係のある複数のアプリケーションのマニフェスト](#multi-app).

* 継承 –
  マニフェストの継承に記述されているように、あるマニフェストの内容を別のマニフェストに含めたい場合、その旨マニフェスト内で定義する必要があります。

* アプリケーション・スタック — アプリケーション・スタックを定義したい場合、stack属性を書き加える必要があります。

## <a id='specify-service'></a>マニフェストでのサービスの指定 ##

アプリケーションのデプロイ時にサービスのインスタンスを作成してバインドしたい場合、以下のような行をマニフェスト内に書き加える必要があります:

<tt> services:<br> &nbsp;&nbsp;&nbsp;<i>service-instance-name:</i><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type: <i>service-type</i><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;provider: <i>service-provider</i><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plan: <i>service-plan</i><br> </tt>

以下の例では二つのサービスを定義しています。

~~~
applications:
- name: mysample
  mem: 256M
  instances: 1
  host: tapp
  domain: ctapps.io
  path: .
  services:
    rediscloud-8cccc:
      type: rediscloud
      provider: garantiadata
      plan: 20mb
    mongolab-6d208:
      type: mongolab
      provider: mongolab
      plan: sandbox
~~~

## <a id='var'></a>マニフェスト内での環境変数 ##

環境変数を定義するには、以下のような行をマニフェストへ書き加える必要があります:

<tt> env:<br> &nbsp;&nbsp;&nbsp;<i>var_name: var_value</i><br> </tt>

例:

<tt> env:<br> &nbsp;&nbsp;&nbsp;greeting: hello<br> </tt>

マニフェストの[あまり簡単ではない例](#not-simple)の“base-manifest”を参照してください。


## <a id='symbols'></a>マニフェスト内でのシンボル ##

`manifest.yml`は以下のシンボルをサポートしています。(シンボルとはRubyのオブジェクトで、オブジェクトの名前は後で解決されます)

* target-base –
  あなたのターゲットのURLのベースです。例えば、“api.mycloud.com”がターゲットだとして、“mycloud.com”がベースとなります。複数のCloud
  Foundryへプッシュされるアプリケーションのマニフェストにとってtarget-baseは有用です。

* random-word – URLがユニークであることを保証するためのランダムな文字列です。

あるいは、シンボルの解決は語句のスコープをシミュレートします —
属性を停止し、子マニフェストで上書きするか、ネストしたハッシュで解決することができます。 (上の複数のマニフェストの継承でも説明されています)

## <a id='inheritance'></a>複数のマニフェストと継承 ##

マニフェストは以下の行で親から設定を継承することができます:

~~~ inherit: path/to/parent.yml ~~~

これにより、親マニフェストの内容が子マニフェストへ引きつがれ、設定がマージされます。シンボル(上のマニフェスト内でのシンボルで言及されている)はマージの後に解決され、子マニフェストで定義された属性は親マニフェストの属性の集合の中でのみ使うことができます。これにより、サービスのバインド情報のような基本的な情報を元となるマニフェストで定義し、子マニフェストで拡張することが可能になります。例:

* 種々のデプロイのために複数の子マニフェストを作り、それぞれ元になるマニフェストを拡張することができます。(例えば、例、デバッグ、ローカル、パブリックなど)

* 元になるマニフェストを用意し、利用者が子マニフェストで設定を追加したり上書きすることができます。

## <a id='multi-app'></a>依存関係のある複数のアプリの定義 ##

マニフェストのより、一回のプッシュ・コマンドで複数のアプリケーションをデプロイすることができます。例えば、独立してはいるが関連があるアプリケーション、発行者と購読者を考えてみましょう。購読者は発行者の前に起動すべきです。これにより、発行者がメッセージを送信しだい、それを受信することができます。この二つのアプリケーションを同じプロジェクトで定義すれば、複数のアプリケーション向けのマニフェストを作り、その中で依存関係を記述することができます。例えば、以下のように書けます:

~~~ ./big-app ./big-app/publisher ./big-app/subscriber ~~~

The manifest below defines two applications, “publisher” and “subscriber”,
that use a single Redis service.  Because the “publisher” application
depends on the “subscriber” application, when you do `cf push` from the
"big-app" directory, “subscriber” will be started before “publisher”.

~~~
applications: 
- name: publisher
  runtime: node08
  mem: 64M
  path: ./publisher
  domain: ${target-base}
  host: publisher
    instances: 1
    services: 
      work-queue: 
        type: rediscloud
        provider: garantiadata
        plan: 20mb
  depends-on: ./subscriber
- name: subscriber
  runtime: node08
  mem: 64M
  path: ./subscriber
  domain: ${target-base}
  host: subscriber
    instances: 1
    services: 
      work-queue: 
        type: rediscloud
        provider: garantiadata
        plan: 20mb
~~~



## <a id='not-simple'></a>あまり簡単ではないマニフェストの例 ##
このセクションはマニフェストのいろいろな機能を示す二つのマニフェストを含んでいます。

**Notes:**

* 最初の行はダッシュ(---)からなります。

* すべてのアプリケーションに共通の設定は、最初のアプリケーションのブロックの前に書きます。環境変数やサービスなどのアプリケーション共通の設定の例は“base-manifest.yml”に示されています。

* どちらのマニフェストも複数のアプリケーション向けのマニフェストの例です。

* 最初のアプリケーションごとの設定nameの前にダッシュ(“-”)を書きます。

* “production-manifest.yml”は“base-manifest.yml”の設定を受けついでいます;
  “base-manifest.yml”内のすべての設定が
  “production-manifest.yml”内で定義されたアプリケーションそれぞれに適用されます。例えば、“base-manifest.yml”内で定義された環境変数は、
  “production-manifest.yml”内で定義されたすべてのアプリケーションで有効です。

* “#”ではじまる行はコメントです。


### production-manifest.yml ###

~~~
---
inherit: base-manifest.yml

properties:
  rails-env: production
  app-host: app1-host

applications:
  - name: app1-web
    host: ${app-host}
    domain: ${target-base}
    instances: 8
    command: bundle exec rake server:start_command
  - name: app1-worker1
    instances: 4
    command: bundle exec rake VERBOSE=true QUEUE=* 
  - name: app1-worker2
    command: bundle exec rake VERBOSE=true 
~~~

### base-manifest.yml ###

~~~
---
properties:
  app-host: ${name}

path: .
env:
  RAILS_ENV: ${rails-env}
  RACK_ENV: ${rails-env}
  BUNDLE_WITHOUT: test:development
services:
  frontend-db:
    type: cleardb
    provider: cleardb
    plan: shock
mem: 512M
disk: 1G
instances: 1
host: none
domain: none

# app-specific configuration
applications:
- name: frontend
  host: ${app-host}
  domain: ${target-base}
  instances: 2
  command: bundle exec rake server:start_command
- name: app2-worker1
  instances: 2
  command: bundle exec rake VERBOSE=true QUEUE=* 
- name: app2-worker2
  command: bundle exec rake VERBOSE=true 

~~~

## <a id='attributes'></a>サポートされるアトリビュート ##


|アトリビュート|説明 |必須?|例 |
| --------- | --------- | --------- |--------- |
|inherit |他のマニフェストを使う場合、そのマニフェストを指定します| n| inherit: base-manifest.yml|
|properties |プロパティと値のペアを指定します|n |properties: <br>&nbsp;&nbsp;app-host: ${name}  |
|env | アプリケーションが使う環境変数を指定します| n | env:  <br>&nbsp;&nbsp;RAILS_ENV: ${rails-env}|
|name  |アプリケーションの名| y | [単純ではないマニフェストの例](#not-simple)の例をご覧ください。|
|buildpack  |外部または独自のビルドパックのURLを指定します  | n |  |
|command  |アプリケーションを起動するコマンド  |n  |command: bundle exec rake server:start_command  |
|domain  |アプリケーションのドメイン|  |  |
|host  | アプリケーションのホスト|  |  |
|instances  |アプリケーションのインスタンスの数|y, defaults to 1<br> 指定されない場合|instances: 2  |
|mem  |アプリケーションが使えるメモリ量の上限|y, defaults to 256M<br> 指定されない場合|mem: 64M |
|disk  |アプリケーションが使えるディスク容量の上限|  |disk:1G  |
|path  |ワーキング・ディレクトリからプッシュするアプリケーションへの相対パス| y  | path: . |
|stack  |  |n  |  |
|depends-on  |カレント・ディレクトリから依存する別アプリケーションへの相対パス|n  |  |
|*Service_Name* |作成されアプリケーションへバインドされるサービスのインスタンスの名前 |y | "base-manifest.xml"の例をご覧ください|
|type |サービスのタイプ|y |"base-manifest.xml"の例をご覧ください|
|provider|サービスのプロヴァイダー |y |"base-manifest.xml"の例をご覧ください|
|plan |サービスの属するプラン|y |"base-manifest.xml"の例をご覧ください|

