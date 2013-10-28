---
title: cfコマンド
---

cfはCloud
Foundryのコマンドです。cfコマンドを使ってアプリケーションをデプロイしたり管理することができます。CloudFoundry.comを含むCloud
Foundryベースの環境で使えます。

## <a id='commands'></a>機能ごとに分類したコマンド ##

下のテーブルはcfのすべてのサブコマンドを示しています。各コマンドをクリックすると詳細を見ることができます。共通のオプションについては右のページ
[コマンドの使い方とオプション](#usage)を参照してください。

|   |  | |
| :-------- | :---------- |:---------- |
| **Basics** <br>[info](#info) <br>[login EMAIL](#login) <br>[logout](#logout) <br>[targets](#targets) <br>[target URL](#target)  <br> <br> **Manage Users** <br>  [create-user EMAIL](#create-user)  <br>   [passwd](#passwd) <br> [register EMAIL](#register) <br> [users](#users)<br><br> **Manage Apps** <br>[app APP](#app) <br> [apps](#apps) <br>[bind-service SVC APP](#bind-service)<br> [console APP](#console) <br>[delete APP](#delete) <br>[map APP HOST DOMAIN](#map) <br>[push NAME](#push) <br>[rename APP APP](#rename) <br>[restart APP](#restart) <br> [scale APP](#scale) <br> [set-env APP NAME VALUE](#set-env)<br>[start APPS](#start) <br>[stop APPS](#stop) <br>[unbind-service SVC APP](#unbind-service)   <br> [unmap URL APP](#unmap) <br> [unset-env APP NAME](#unset-env)    <br> <br> | **Get Information About Apps**  <br>[crashes APPS](#crashes) <br>[crashlogs APP](#crashlogs)  <br> [env APP](#env)  <br> [file APP PATH](#file)  <br> [files APP PATH](#file) <br> [guid TYPE NAME](#guid) <br>[health APP](#health) <br>[instances APP](#instances) <br>[logs APP](#logs) <br>[stats APP](#stats) <br>[tail APP PATH](#tail)        <br> <br>  **Manage Services** <br>[bind-service SVC APP](#bind-service) <br>[create-service-auth-token LABEL PROVIDER](#create-service-auth-token)<br>[create-service OFFERING NAME](#create-service) <br>[delete-service-auth-token TOKEN](#delete-service-auth-token)<br>[delete-service SVC](#delete-service) <br>[rename-service SVC SVC](#rename-service) <br>[tunnel INSTANCE CLIENT](#tunnel)  <br>[unbind-service SVC APP](#unbind-service)  <br>[update-service-auth-token TOKEN](#update-service-auth-token)   <br> <br>**Get Information about Services** <br>[info --service](#info) <br> [service-auth-tokens](#service-auth-tokens) <br>[service SERVICE](#service) <br>[services](#services)   |**Manage Organizations and Spaces** <br>[create-org ORG](#create-org) <br>[create-space NAME ORG](#create-space)  <br>  [delete-org ORG](#delete-org) <br>[delete-space SPACE](#delete-space)   <br>[rename-org ORG ORG](#rename-org)   <br>[rename-space SPACE SPACE](#rename-space) <br> [set-quota QUOTA ORG](#set-quota) <br>[switch-space SPACE](#switch-space) <br> <br> **Get Information About Organizations and Spaces** <br> [guid TYPE NAME](#guid) <br>[org ORG](#org)  <br>[orgs](#orgs)  <br>[space SPACE](#space)  <br> [spaces ORG](#spaces)  <br> <br>**Manage Domains and Routes** <br>[domains SPACE](#domains) <br> [guid TYPE NAME](#guid)<br>[routes](#routes) <br>[unmap-domain DOMAIN](#unmap-domain )  <br>
|  | |

## <a id='installing'></a>cfのインストール ##

cfはRubyとRubyGemsを必要とします。RubyとRubyGemsについては、右のページを参照してください。[Installing
Ruby](/docs/common/install_ruby.html).

RubyとRubyGemsをインストールした後、cfの最新版をインストールするには以下のコマンドを実行してください。:

<pre class="terminal"> $ gem install cf </pre>

プレリリース版のcfをインストールするには:

<pre class="terminal"> $ gem install cf --pre </pre>

## <a id='usage'></a>コマンドの使い方とオプション ##

cfコマンドは、Cloud
Foundryのアプリケーション、サービス、オーガナイゼーション、スペース、ドメインなどの参照、作成、変更を行なうことができます。applications,
services, organisations, spaces, domains, and so on. 各コマンドはcfではじまります。例えば:

<pre class="terminal"> $ cf push my-new-app </pre>


大半のコマンドは振舞を変えたり引数の値を与えたりするためのオプションを供えています。必要な引数の値をコマンドラインで指定しなかった場合、cfは対話的に必要なデータの入力を求めます。cfコマンドをスクリプトの中で使う場合、必要な引数をすべて指定しておく必要があります。コマンドのオプションは任意の順番で指定できます。

このページでは各コマンドのオプションについて記述します。同等の情報を `cf help [command]` あるいは `-h` か
`--help`オプションで表示できます。

**グローバルなオプション**

以下のオプションはすべてのcfコマンドに共通です。

| オプション | 説明 |
| :------------------------ | :---------- |
|  --color, --no-color                  | |
|  --debug | コマンドが失敗した場合、スタック・トレースを~/.cf/crashへ書く代わりにコンソールへ出力します。|
|  --force | 確認をスキップします。`--force`を指定した場合、必要なオプションすべてを指定してください。 <br>不足している場合はコマンドは失敗します。|  --help | コマンドの説明とオプションの一覧を表示します。|
| --http-proxy HTTP_PROXY |httpプロキシを経由して接続します。|
| --https-proxy HTTP_PROXY |httpsプロキシを経由して接続します。 |
| --manifest FILE, -m
|  --quiet, --no-quiet |  Return a minimal response. たとえば、`cf apps --quiet`は各アプリの`name`アトリビュートを返します<br>`status`, `usage`, `url`は返しません。通常はこれらも表示されます。The `--quiet` <br>スクリプト内で使う場合に有用です。|
| --script |  コマンドは`--force`と`--quiet`付で実行されます。出力をリダイレクトしたりパイプへ向けた場合、cfコマンドは自動的に`--script` オプションを追加することに注意してください。この機能を抑止するには、<br>`--no-script`オプションを指定してください。|
|  --trace, -t | コマンドとターゲットの間の通信をすべて表示します。デバッグの時に有用です。以下に注意<br>`--trace`は取り扱い注意のデータを返す場合があります。たとえば、あなたのトークンなどです。|
|  --verbose, -V | Return a verbose response, if available. すべてのコマンドが詳細な結果を表示できるわけではありません。|
|  --version, -v | cfのヴァージョンを表示します。|





## <a id='details'></a>コマンドの詳しい説明 ##

#### <a id='app'></a> app #### アプリケーションの情報を表示する。

<div class="command-doc">
  <pre class="terminal">$ cf app [application name]</pre>
</div>

以下のようなデータが返されます:

* status -- アプリの状態です。たとえば"running"などです
* usage -- 割り当てられたメモリの量やインスタンスの数です
* urls -- アプリケーションに割り当てられたURLです
* services --- アプリケーションにバインドされたサービスのインスタンスです

#### <a id='apps'></a> apps #### 現在のスペースのアプリケーションの一覧

<div class="command-doc">
  <pre class="terminal">$ cf apps</pre>
</div>

個々のアプリケーションについて、以下のデータが返されます:

* name -- 名前
* status -- アプリの状態です。たとえば"running"などです
* usage -- 割り当てられたメモリの量やインスタンスの数です
* url -- アプリケーションのURLです

以下のテーブルにコマンドのオプションを示します

| オプション |  | 説明 |
| :-------- | :------- | :---------- |
| --full     |   n       | より詳細な出力|
| --name NAME     |  n        |  正規表現で指定した名前に一致するアプリケーションの一覧|
| --space SPACE    |   n       |指定されたスペースに属するアプリケーションの一覧|
| --url URL     |   n       | 正規表現で指定したURLに一致するアプリケーションの一覧|

#### <a id='bind-service'></a> bind-service ####

サービスをアプリケーションにバインドするバインドできるサービスとできないサービスがあります。バインドをサポートしているサービスについて、アプリケーションにバインドすると資格情報が環境変数`VCAP_SERVICES`へ追加されます。バインドが使えるようにするためにはアプリケーションの再起動が必要です。

[サービスの作成](#create-service)にあるように、サービスの作成と同時にアプリケーションへバインドできることに注意してください。サービスのインスタンスの作成とバインドについて、詳細は
[サービスの追加をするには](/docs/dotcom/adding-a-service.html)をご覧ください




<div class="command-doc">
  <pre class="terminal">$ cf bind-service [instance name] [application name]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --app     |   y       | サービスにバインドされるアプリケーションの名前|
| --service     |  y        |  アプリケーションにバインドされるサービス|


#### <a id='console'></a> console ####

Railsのコンソールを開き、アプリケーションへ接続する。Railsコンソールの詳細については、"Deploying Rails
Apps"の[Rails 3,
コンソールを使う](/docs/using/deploying-apps/ruby/rails-using-the-console.html)をご覧ください。**

<div class="command-doc">
  <pre class="terminal">$ cf console  [application name] [port}</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --app     |   y       | アプリケーションの名前|
| --port     |  y        | アプリケーションが実行されているポート|

#### <a id='crashes'></a> crashes ####

指定されたアプリケーションのリストについて、反応のないアプリケーションを表示します。Nin

<div class="command-doc">
  <pre class="terminal">$ cf crashes [list of application names]</pre>
  <div class="break"></div>
</div>


#### <a id='crashlogs'></a> crashlogs ####

反応のないアプリケーションの標準出力と標準エラー出力を表示します。

<div class="command-doc">
  <pre class="terminal">$ cf crashlogs [application name]</pre>
  <div class="break"></div>
</div>

#### <a id='create-org'></a> create-org ####

オーガナイゼーションを作る

<div class="command-doc">
  <pre class="terminal">$ cf create-org [organisation name]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --[no-]add-self     |     n    | このオプションは、あなたをオーガナイゼーションへ所属させたい、あるいはさせたくないということを指定するのに使います。|
| --name     |   y       | オーガナイゼーションに割当てられた名前|
|  -t, --[no-]target    | n         | このオプションは、オーガナイゼーションの作成後に、そのオーガナイゼーションへスイッチするかどうかを指定するのに使います。|

#### <a id='create-service'></a> create-service ####

サービスのインスタンスを作ります。アプリケーションへバインドすることもできます。サービスを作成した時にバインドしなかった場合、後で[bind-service](#bind-service)コマンドを使ってバインドすることもできます。サービスのインスタンスの作成とバインドについて、詳細は
[サービスの追加をするには](/docs/dotcom/adding-a-service.html)をご覧ください

既存のサービスのインスタンスの一覧を表示するには、[services](#services)コマンドを使います。


<div class="command-doc">
  <pre class="terminal">$ cf create-service [service type] [instance name]</pre>
  <div class="break"></div>
</div>



以下のテーブルにコマンドのオプションを示しますcfコマンドは、オプションで指定されなかった必要な情報の入力を求めます。

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --app, --bind APP   |  n       | サービスをバインドするアプリケーション|
| --name     |   y       | サービスの名前`create-service`を対話的に実行した場合、cfはデフォルトのインスタンス名を表示します。上書きしてかまいません。名前として、英文字、数字、([a-z], [A-Z], [0-9]), ハイフン (-), アンダースコア (_)が使えます。|
|--offering | y| 作成するサービスのタイプ。たとえば、 "rediscloud", "mongolab", など`create-service`を対話的に実行した場合、cfコマンドはサービスのタイプとヴァージョンとプロヴァイダを含む一覧を表示します。|
|--plan | y |サービスのプランサービス・プランはサービスのフィーチャーとリソースの組合せを定義します。`create-service`を対話的に実行した場合、cfコマンドはプロヴァイダーが提供するプランの一覧を表示します。|
|--provider | n|サービスのプロヴァイダー|
|--version |n |サービスのヴァージョン|

#### <a id='create-service-auth-token'></a> create-service-auth-token ####

サービスの認証トークンを作成する

<div class="command-doc">
  <pre class="terminal">$ cf create-service-auth-token [label] [provider]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--label | |Token
lable.| |--provider | |トークンのプロヴァイダー| |--token TOKEN | |トークンの値|


#### <a id='create-space'></a> create-space ###

オーガナイゼーションの中にスペースを作成する

<div class="command-doc">
  <pre class="terminal">$ cf create-space [space name] [organization name]</pre>
  <div class="break"></div>
</div>

| オプション | | 説明 | | :-------- | :------- | :---------- | |--[no-]developer |
|あなたをスペースへ開発者として追加するかどうかを指定します。| | --[no-]manager|
|あなたをスペースへ管理者として追加するかどうかを指定します。| |--auditor |
|あなたをスペースへ監査者として追加するかどうかを指定します。| |--name NAME | |新しいスペースの名前| |-o,
--organization, --org ORGANIZATION | |スペースを追加するオーガナイゼーションの名前| |-t, --target
| |スペースの作成後、そのスペースへスイッチするかどうかを指定する。|


#### <a id='create-user'></a> create-user ####

ユーザ・アカウントを作成するコマンド・ラインで必要なオプションを指定しなかった場合、cfは入力を求めてきます。


<div class="command-doc">
  <pre class="terminal">$ cf create-user [email]</pre>
  <div></div>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--email EMAIL |
y|Email of the new user. | |--password PASSWORD |y |新規ユーザのパスワード| |--verify
VERIFY |y |もう一度パスワードを指定する| |-o, --organization, --org ORGANIZATION|
|新規ユーザの割当てられるオーガナイゼーション。|

#### <a id='delete'></a> delete ####

アプリケーションを削除するcfはアプリケーションとバインドされたサービスを削除してよいか、確認を求めてきます。

<div class="command-doc">
  <pre class="terminal">$ cf delete [list of application names]</pre>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |n
|現在のスペースのアプリケーションすべてを削除する。| |--apps, --app APPS |y | 削除されるアプリケーションの名前|
|--routes | n |削除されるアプリケーションに対応づけられたルートを削除するか | |-o, --delete-orphaned
DELETE_ORPHANED |n | |

#### <a id='delete-org'></a> delete-org ####

オーガナイゼーションを削除する。

<div class="command-doc">
  <pre class="terminal">$ cf delete-org [organisation name]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | | --[no-]warn| |
| | -o, --organization, --org ORGANIZATION| | | | -r, --recursive| | |


#### <a id='delete-route'></a> delete-route ####

ルートを削除する。

<div class="command-doc">
  <pre class="terminal">$ cf delete-route [route]</pre>
  <div class="break"></div>
</div>



#### <a id='delete-service-auth-token'></a> delete-service-auth-token ####

サービスの認証トークンを削除する。

<div class="command-doc">
  <pre class="terminal">$ cf delete-service-auth-token [service auth token]</pre>
  <div class="break"></div>
</div>

#### <a id='delete-service'></a> delete-service ####

サービスを削除する。

<div class="command-doc">
  <pre class="terminal">$ cf delete-service [instance name]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
|--[no-]unbind    | |削除する前にアプリケーションとのバインドを解消するか?|
| --all| |すべてのサービスを削除する|
| --service SERVICE| |削除するサービス|

#### <a id='delete-space'></a> delete-space ####

スペースとその内容を削除する

<div class="command-doc">
  <pre class="terminal">$ cf delete-space [list of space names]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--[no-]warn |
|削除しようとしているスペースがオーガナイゼーション内で最後の一つの場合に警告するかどうか。| |--space SPACE | |削除するスペース|
|-o, --organization, --org ORGANIZATION | |削除するスペースを含んでいるオーガナイゼーション| |-r,
--recursive | |

#### <a id='delete-user'></a> delete-user ####

ユーザ・アカウントの削除

<div class="command-doc">
  <pre class="terminal">$ cf delete-user [email]</pre>
  <div class="break"></div>
</div>



#### <a id='domain'></a> domains ####

ドメインの一覧を表示する

<div class="command-doc">
  <pre class="terminal">$ cf domains [space]</pre>
  <div class="break"></div>
</div>

ドメインそれぞれに対して、以下のデータが返される:

* name -- ドメインの名前
* owner -- ドメインの所有者

#### <a id='env'></a> env ####

アプリケーションの環境変数すべてAmazon
S3のAPIキーのような知られたくないデータを格納する際、ソース・コードの中ではなく環境変数を使うのはよい方法です。

<div class="command-doc">
  <pre class="terminal">$ cf env [application name]</pre>
</div>

#### <a id='file'></a> file ####

指定されたアプリケーションに属する、パスで指定されたファイルの内容を表示する。

<div class="command-doc">
  <pre class="terminal">$ cf file [application name] [path]</pre>
</div>

#### <a id='files'></a> files ###

アプリケーションに属するファイルの一覧を表示する。

#### <a id='guid'></a> guid ####

オブジェクトとタイプ(アプリ、オーガナイゼーション、スペース、ドメインなど)のGUIDを得ます。

<div class="command-doc">
  <pre class="terminal">$ cf guid type [name]</pre>
  <div></div>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--name NAME |
n|オブジェクトの名前| |--type TYPE |y |オプジェクトのタイプ: "app", "org", "space", "domain".
|


#### <a id='health'></a> health ####

指定されたアプリケーションの状態を表示します。

<div class="command-doc">
  <pre class="terminal">$ cf health [list of application names]</pre>
  <div class="break"></div>
</div>

アプリケーションの状態("running", "flapping",
"stopped")を返します。アプリケーションのインスタンスの一部だけが実行されている場合、そのパーセンテージを表示します。

#### <a id='info'></a> info ####

ターゲットの情報の表示

| オプション	 | 必須 | 説明 | | :-------- | :------- | :---------- | |-a, --all | n|
| |-s, --services |n | |

以下の情報が返されます:

* target -- ターゲットとなっているCloud Foundryのインスタンス
* version -- Cloud Foundryのヴァージョン
* `--all`か`--services`オプションを指定した場合、サービスのタイプごとに以下の情報が返されます:
    * service -- サービスのタイプ    * version -- サービスのヴァージョン
    * provider -- サービスを提供している企業    * plans -- サービスの属するプラン    * description -- サービスの説明

#### <a id='instances'></a>インスタンス #### インスタンスの一覧を表示する。

<div class="command-doc">
  <pre class="terminal">$ cf instances [list of application names]</pre>
  <div class="break"></div>
</div>

実行中のインスタンスについて、以下のデータが返されます:

* started -- インスタンスが起動した時刻
* console -- インスタンスが実行中のポートとIPアドレス

#### <a id='login'></a> login ####

ターゲットに対して認証する。コマンド・ラインで必要なオプションを指定しなかった場合、cfは入力を求めてきます。

<div class="command-doc">
  <pre class="terminal">$ cf login [email]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--password
PASSWORD |y |認証用パスワード| |--username, --email EMAIL |y
|ユーザ・アカウントを識別するためのユーザ名またはメールアドレス| |-o, --organization, --org ORGANIZATION |n
|ログインした時にスイッチするオーガナイゼーションを指定する。| |-s, --space SPACE |n
|ログインした時にスイッチするスペースを指定する。|

#### <a id='logout'></a> logout ####

ターゲットからログアウトする。

<div class="command-doc">
  <pre class="terminal">$ cf logout</pre>
  <div class="break"></div>
</div>

#### <a id='logs'></a> logs ####

ステージング、標準出力、標準エラー出力などのログを表示します。ログ・ファイルはいくつかの種類がありえます。

アプリケーション特有のログを見たい場合は'cf file'コマンドを実行してください。

<div class="command-doc">
  <pre class="terminal">$ cf logs [application name]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |n
|アプリケーションのすべてのインスタンスのログの一覧を表示する| |--app APP |y |アプリケーションの名前| |--instance
INSTANCE |n
|ログを見たいアプリケーションのインスタンスを指定する。たとえば、"2"。<br>指定がなければ、最初のインスタンス("0")が表示される。|


#### <a id='map'></a> map ####

URLとアプリケーションを関連づけます。

<div class="command-doc">
  <pre class="terminal">$ cf map [application name] [url]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--app APP|
|URLと関連づけたいアプリケーション| |--domain DOMAIN | |トップ・レベルのドメイン| |--host HOST | | |


#### <a id='map-domain'></a> map-domain ####

ドメインを追加し、スペースへ割当てる。<div class="command-doc">
  <pre class="terminal">$ cf map-domain [domain]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --name NAME | |ドメイン名トップからのドメインを指定します。たとえば`mydomain.com`などです。|
| --shared | |ドメインを共有します|
|--space SPACE | |ドメインがマップされるスペース指定されなかった場合、現在のスペースが使われます。|
|-o, --organization, --org ORGANIZATION   | |ドメインがマップされるオーガナイゼーション指定がない場合、ドメインはオーガナイゼーションに関連づけられます。|


#### <a id='org'></a> org ####

オーガナイゼーションの情報の表示

<div class="command-doc">
  <pre class="terminal">$ cf org [organisation name]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--full |
|オーガナイゼーション内のスペースの情報を帰す| |--org ORGANIZATION |
|データ表示の対象となるオーガナイゼーション指定されなければ、現在のオーガナイゼーションの情報が返されます。|

以下のようなデータが返されます:

* domains -- オーガナイゼーションへマップされたドメイン
* spaces -- オーガナイゼーション内のスペース
* `--full`オプションが指定されていれば、オーガナイゼーション内の各スペースについて以下のデータが返されます:
     * apps -- スペースへデプロイされたアプリケーション     * services -- スペースの中のサービス

#### <a id='orgs'></a> orgs ####

オーガナイゼーションの一覧

<div class="command-doc">
  <pre class="terminal">$ cf orgs</pre>
  <div class="break"></div>
</div>

以下のようなデータが返されます:

* domains -- オーガナイゼーションへマップされたドメイン
* spaces -- オーガナイゼーション内のスペース
* `--full`オプションが指定されていれば、オーガナイゼーション内の各スペースについて以下のデータが返されます:
     * apps -- スペースへデプロイされたアプリケーション     * services -- スペースの中のサービス


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--full |n
|オーガナイゼーション内のスペースの情報を返す| #### <a id='passwd'></a> passwd ####

ユーザのパスワードを設定する。<div class="command-doc">
  <pre class="terminal">$ cf passwd [email]</pre>
  <div class="break"></div>
</div>

#### <a id='push'></a> push ####

アプリケーションを新たにデプロイしてください。あるいは、既にあるなら、最後にプッシュしてからの変更分をアップロードしてください。

デプロイのオプションは、コマンドラインか対話的にかマニフェスト・ファイルで指定できます。`cf
push`を最初に実行した時、コマンド・ラインで指定しないかぎり、cfはカレント・ディレクトリで`manifest.yml`を探します。見つからない場合、cfは対話的に設定するよう求めます。必要な情報を入力した後、cfは設定を保存するかきいてきます。肯定すると、カレント・ディレクトリに`manifest.yml`として保存されます。

アプリケーションを更新する場合、cfは`manifest.yml`を参照しないことに注意してください。その代わりに、cfはその時点で有効な設定を使用します。二回目以降にマニフェストを適用させるには、`--reset`オプションを指定する必要があります。詳細については[アプリケーションのマニフェスト](../../deploying-apps/manifest.html)ページの[cf
pushとマニフェスト](../../deploying-apps/manifest.html#push-and-manifest)をご覧ください。


cfはアプリケーションのファイルをすべてアップロードしますが、ヴァージョン・コントロール・システム`.svn`, `.git`, and
`.darcs`は除外します。他にも除外したいファイルがあれば、プッシュする時のディレクトリの`.cfcignore`に記述してください。`.cfignore`
behaves similarly to `.gitignore`.


<div class="command-doc">
  <pre class="terminal">$ cf push [APP]</pre>
</div>

| オプション           | 必須 | 説明 |
| :------------------ | :------- | :---------- |
| --[no-]bind-services                |  | バインドするサービスを指定するかどうか<br>バインドしたいと指定すれば、どのタイプのサービスをバインドするかの入力を求められます。|
| --[no-]create-services         |   |サービスを作成したいかどうかを指定します。|
| --[no-]restart              |  | 更新後にアプリを再起動したいかどうかを指定します。|
| --[no-]start      |          |デプロイ後にアプリケーションを起動したいかどうかを指定します。|
| --buildpack BUILDPACK         |          | 使用したいビルドパックのURLを指定します。|
| --command COMMAND        |         |アプリケーションを起動するためのコマンド**注意:** Cloud FoundryのビルドパックはHerokuのビルドパックに似ていますが、 Herokuとは異なり、Cloud Foundryは`procfile`から起動コマンドを探そうとはしません。|
| --domain DOMAIN              |          | アプリケーションのトップ・レベル・ドメイン|
| --host HOST    |        |サブドメイン。独自のドメインを使う場合は指定しないでください。|
| --instances INSTANCES       |          | アプリケーションのインスタンスの数|
| --memory MEMORY             |          | 各インスタンスのメモリの最大値|
| --name NAME             | | プッシュするアプリケーションの名前|
| --plan PLAN              | | 使いたいサービスのプラン(プランによってサービスの価格と利用できるリソースが決まります)     |
|--path | |プッシュするアプリケーションへのパス|
| --reset             | |`push` のオプションの値を指定し、以前の値を上書きする場合に使います。|
| --stack STACK           | | |

#### <a id='register'></a> register ####

ユーザーを作り、ログインします。

<div class="command-doc">
  <pre class="terminal">$ cf register [email]</pre>
</div>


| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
|--[no-]login  | | |
|--email EMAIL  | | |
|--password PASSWORD  | | |
|--verify VERIFY
 | | |
#### <a id='rename'></a> rename ####

アプリケーションの名前を変更します。コマンド・ラインで必要なオプションを指定しなかった場合、cfは入力を求めてきます。

アプリケーションの名前を変更すると、cfは以前の名前を認識できなくなることに注意してください。

<div class="command-doc">
  <pre class="terminal">$ cf rename [current application name] [new application name]</pre>
</div>

#### <a id='rename-org'></a> rename-org ####

オーガナイゼーションの名前を変更します。
<div class="command-doc">
  <pre class="terminal">$ cf rename-org [organization name] [new organization name]</pre>
  <div class="break"></div>
</div>

#### <a id='rename-service'></a> rename-service ####

サービスの名前を変更します。<div class="command-doc">
  <pre class="terminal">$ cf rename-service [instance name] [new instance name]</pre>
  <div class="break"></div>
</div>

#### <a id='rename-space'></a> rename-space ####

Rename a space.

<div class="command-doc">
  <pre class="terminal">$ cf rename-space [space name] [new space name]</pre>
  <div class="break"></div>
</div>

#### <a id='restart'></a> restart ####

アプリケーションを停止し、その後起動します。

<div class="command-doc">
  <pre class="terminal">$ cf restart [APP]</pre>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |
|現在のスペースのアプリケーションすべてを削除する。| |--app APP | |削除されるアプリケーションの名前| |-d,
--debug-mode DEBUG_MODE | |デバッグ・モードに設定し、アプリケーションを再起動します。|

#### <a id='routes'></a>ルート ####

スペース内のルートの一覧表示

<div class="command-doc">
  <pre class="terminal">$ cf routes</pre>
  <div></div>
  <div class="break"></div>
</div>

#### <a id='scale'></a> scale ####

インスタンスの数を指定し、各インスタンスへ割当てられるメモリの量を指定します。

<div class="command-doc">
  <pre class="terminal">$ cf scale [application name]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--[no-]restart |
|アプリケーションを更新した後に再起動したいかどうかを指定する。 ||--app APP | |更新するアプリケーション||--disk DISK |
|アプリケーションへ割当てるディスク容量 ||--instances INSTANCES | |実行するインスタンスの数 ||--memory
MEMORY | |アプリケーションへ割当てるメモリ容量 |||--plan PLAN | |アプリケーションのプラン ||


#### <a id='service-auth-token'></a> service-auth-token ####

認証用トークンの一覧

<div class="command-doc">
  <pre class="terminal">$ cf service-auth-token</pre>
  <div class="break"></div>
</div>

トークンごとに以下のデータが返されます:

* guid -- サービスのタイプのGUID
* provider -- サービスの提供者

#### <a id='service'></a> service ####

サービスのインスタンスの情報の表示

<div class="command-doc">
  <pre class="terminal">$ cf service [instance name]</pre>
  <div class="break"></div>
</div>

#### <a id='services'></a> services ####

現在のスペース内のサービスのインスタンの情報の表示

<div class="command-doc">
  <pre class="terminal">$ cf services</pre>
  <div class="break"></div>
</div>

以下のテーブルにコマンドのオプションを示します。

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --app APP | n | 指定したアプリケーションへバインドされたサービスのインスタンスの一覧.  |
| --full | n | より詳細な出力|
| --name NAME | n |指定した名前と一致するサービスのインスタンスのみ表示|
| --plan PLAN | n | 指定したプランで提供されているサービスのインスタンスの一覧|
|--provider PROVIDER   | n |指定した提供者からのサービスのインスタンスの一覧|
|--service SERVICE   |n  |指定した文字列に一致するサービスのインスタンスの一覧|
| --space SPACE |  |指定したスペース内のサービスのインスタンスの一覧|
| --version VERSION |  |指定したヴァージョンのサービスのインスタンスの一覧|

      
インスタンスごとに以下のデータが返されます:

* name -- サービスのインスタンスが作られた時につけられた名前
* service -- サービスのタイプ。たとえば、"cleardb"や"rediscloudなど。
* provider -- サービスの提供者
* version -- サービスのヴァージョン
* plan -- 提供されたサービスのプラン
* description -- `--full`オプションを指定した場合に返される、プランの説明
* bound apps -- サービスがバインドされているアプリケーション

#### <a id='set-env'></a> set-env ####

環境変数の設定境変数の削除については[set-env](#set-env)をご覧ください。

<div class="command-doc">
  <pre class="terminal">$ cf set-env [App] [Variable] [Value]</pre>
</div>

| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
|--[no-]restart |n |アプリケーションの更新後に再起動したいかどうかを指定する|
|--app APP | y |環境変数の設定の対象となるアプリケーション|
|--name NAME    | y |環境変数の名前
|--value VALUE   | y |環境変数の値|

#### <a id='set-quota'></a> set-quota ####

アプリケーションのクォータの設定

<div class="command-doc">
  <pre class="terminal">$ cf set-quota [QUOTA_DEFINITION] [ORGANIZATION]</pre>
</div>

| Qualifier | Required | Description | | :-------- | :------- | :----------
| |--quota-definition QUOTA-DEFINITION| y| 設定できる値は "free", "paid",
"runaway", "trial"| |--organization ORG |y |クォータの対象となるオーガナイゼーション ||


#### <a id='space'></a> space ####

スペースの情報の表示

<div class="command-doc">
  <pre class="terminal">$ cf space [space name]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--full |
|スペース内のアプリケーションの情報を含める| |--space SPACE |
|情報を見たいスペース指定がなければ、現在のスペースの情報が返される。| |--org ORGANIZATION |
|データ表示の対象となるオーガナイゼーション指定がなければ、現在のオーガナイゼーションの情報が返される。|

以下のようなデータが返されます:

* organization -- スペースを含むオーガナイゼーション
* apps -- スペースへデプロイされたアプリケーション
* services -- スペース内のサービス
* domains -- ドメイン内のサービス
* `full`オプションが指定されていれば、スペース内のアプリケーションについて、以下のような情報が返されます:
     * status -- アプリケーションの状態。たとえば、"stopped", running", "flapping"など。

#### <a id='spaces'></a> spaces ####

オーガナイゼーション内のスペース

<div class="command-doc">
  <pre class="terminal">$ cf spaces [organization name]</pre>
  <div class="break"></div>
</div>

| Qualifier | Required | Description | | :-------- | :------- | :----------
| |--full | |各スペースのアプリケーションとサービスの情報を含める||--name NAME |
|名前でフィルター指定がなければ、指定のオーガナイゼーション内のすべてのスペースの情報になる。| |--org ORGANIZATION |
|オーガナイゼーションの指定指定がなければ、現在のオーガナイゼーション内のスペースが対象。| #### <a id='start'></a> start
####

アプリケーションの起動

<div class="command-doc">
  <pre class="terminal">$ cf start [APP]</pre>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |
|スペース内のすべてのアプリケーションを起動する| |--apps, --app APPS | |起動するアプリケーションを指定する| |-d,
--debug-mode DEBUG_MODE | |アプリケーションをデバッグ・モードで起動する|

#### <a id='stats'></a> stats ####

アプリケーションの各インスタンスの情報(CPU、メモリ、ディスク容量など)を表示

<div class="command-doc">
  <pre class="terminal">$ cf stats [application name]</pre>
  <div class="break"></div>
</div>

各インスタンスについて、以下の情報が返される:

* instance -- インスタンスの数
* cpu -- CPU使用率
* memory -- メモリ使用率
* disk -- ディスク利用率

#### <a id='stop'></a> stop ####

すでに"Stopped"になっていないアプリケーションを停止する

<div class="command-doc">
  <pre class="terminal">$ cf stop [list of application names]</pre>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |
|スペース内のすべてのアプリケーションの停止||--apps, --app APPS | |停止するアプリケーションの指定|

#### <a id='switch-space'></a> switch-space ####

別のスペースへの切り替え

<div class="command-doc">
  <pre class="terminal">$ cf switch-space [space name]</pre>
</div>

#### <a id='tail'></a> tail ####

アプリケーションとパスが指定されたファイルを監視し、更新されるごとに表示する。(\*nixの'tail'コマンドと同様)

<div class="command-doc">
  <pre class="terminal">$ cf tail [application name] [path]</pre>
  <div class="break"></div>
</div>

| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--app APP| | |
|--path PATH | | |

#### <a id='target'></a> target ####

ターゲット、オーガナイゼーション、スペースの指定cfを実行してアプリケーションやサービスのインスタンスの情報を読み書きする場合、デフォルトではその時点で指定されているターゲット、オーガナイゼーション、スペースが用いられます。

<div class="command-doc">
  <pre class="terminal">$ cf target [URL]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | | --url URL |n|
別のCloud Foundryインスタンスを指定する時に使う | | --org ORGANIZATION |n
|別のオーガナイゼーションを指定する時に使う||--space SPACE |n |別のスペースを指定する時に使う|

コマンド・ラインでオプションが指定されなかった場合、以下のデータが返されます:

* target -- ターゲットとなっているCloud Foundryのインスタンス
* organization -- 現在のオーガナイゼーション
* space -- 現在のスペース

#### <a id='targets'></a> targets ####

ターゲットの一覧

<div class="command-doc">
  <pre class="terminal">$ cf targets</pre>
  <div class="break"></div>
</div>

各Cloud FoundryインスタンスのURLが返される。


#### <a id='tunnel'></a> tunnel ####

サービスのインスタンスへのトンネルトンネルを開く、あるいは自動的にクライアントを起動してサービスへ接続することができます。トンネルについては、[Tunneling
to Services](../../services/tunnelling-with-services.html)をご覧ください。

<div class="command-doc">
  <pre class="terminal">$ cf tunnel [instance name] [application name]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
| --client CLIENT | |起動したいクライアント|
|--instance INSTANCE | |トンネルを接続したいサービスのインスタンス|
|--port PORT             | |トンネルをバインドするポート|

#### <a id='unbind-service'></a> unbind-service ####

アプリケーションからバインドされていたサービスを切り離す。cfコマンドは、コマンド・ラインで指定されなかった必要なオプションの入力を求めます。

<div class="command-doc">
  <pre class="terminal">$ cf unbind-service [instance name] [application name]</pre>
  <div class="break"></div>
</div>

#### <a id='unmap-domain'></a> unmap-domain ####

現在のスペースからドメインを切り離す。削除もできる。

<div class="command-doc">
  <pre class="terminal">$ cf unmap-domain [domain] [--delete]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--delete | |
ドメインの切り離し、または削除を指定する||--domain DOMAIN | |切り離すドメイン | | --space SPACE| |
ドメインを切り離すスペース| | --org ORGANIZATION| |ドメインを切り離すオーガナイゼーション|

#### <a id='unmap'></a> unmap ####

URLとアプリケーションの関連を断ちきる。

<div class="command-doc">
  <pre class="terminal">$ cf unmap [application name] [url]</pre>
  <div class="break"></div>
</div>


| オプション | 必須 | 説明 | | :-------- | :------- | :---------- | |--all |
|すべてのルートについて動作する | | --app APP| |URLを切り離すアプリケーション| |--url URL | |切り離すURL| |
| | |

#### <a id='unset-env'></a> unset-env ####

Remove an environment variable. 環境変数については、[set-env](#set-env)をご覧ください。

<div class="command-doc">
  <pre class="terminal">$ cf unset-env [application name] [variable name]</pre>
</div>


| オプション | 必須 | 説明 |
| :-------- | :------- | :---------- |
|--[no-]restart |n |アプリケーションの更新後に再起動したいかどうかを指定する|
|--app APP | y |環境変数の設定の対象となるアプリケーション|
|--name NAME    | y |環境変数の名前|

#### <a id='update-service-auth-token'></a> update-service-auth-token ####

サービスの認証トークンを更新する。

<div class="command-doc">
  <pre class="terminal">$ cf update-service-auth-token [SERVICE_AUTH_TOKEN]</pre>
</div>

#### <a id='users'></a> users ##

ユーザの一覧

<div class="command-doc">
  <pre class="terminal">$ cf users</pre>
</div>





