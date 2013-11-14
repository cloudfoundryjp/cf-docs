---
title: ドメイン、サブドメイン、ルート
---


このページはアプリケーションへのルートまたはURLについて説明しています。アプリケーションをプッシュする時に指定できるサブドメインとドメインからルートが作られます。

以下のルートでは`myapp`がサブドメインで、`example.com`がドメインです:

`myapp.example.com`
## <a id='domains'></a>ドメインについての重要な情報 ##

Cloud Foundryでは、ドメインはスペースに関連づけられます。

Cloud Foundryのインスタンスはすべてのスペースに使えるデフォルトのドメインを持っています。たとえば、PivotalのCloud
Foundryのインスタンスでは`cfapps.io` ドメインがすべてのオーガナイゼーションのすべてのスペースに割り当てられます。

また、Cloud Foundryはカスタムドメインもサポートします -- 以下のように、独自のドメインを登録し、Cloud
Foundryの中のスペースに割り当てることができます。

## <a id='map-domain'></a>カスタム・ドメインのスペースへのマッピング ##

自分のドメインを使いたいなら、`cf map-domain`コマンドでCloud Foundryへ登録しアプリケーションのスペースに割り当てる必要があります。

`cfapps.io`や`example.com`のようなトップ・レベル・ドメインを指定してください。以下のコマンドは独自ドメイン`example.com`を“development”へ割り当てます。

`cf map-domain --space development example.com`

そのURLでアプリケーションにアクセスしたくないなら、ドメインをアプリケーションへ割り当てないでください。

## <a id='view-domains'></a>スペース内のドメインの表示 ##

`cf domains`コマンドであるスペースへ割り当てられたドメインを見ることができます。この例では、“development”スペースに二つのドメインが割り当てられています。デフォルトの`cfapps.io`と独自の`example.com`です:

<pre class="terminal">
cf push my-new-app
cf domains --space development
Getting domains in development... OK

name           owner   
cfapps.io      none    
example.com    jdoe
</pre>

 

## <a id='unmap-domain'></a>Unmap a Domain ##
`cf unmap-domain`コマンドでドメインを切り離すことができます。この例では、`example.com`ドメインが“development”から切り離されます:

<pre class="terminal">
cf unmap-domain --space development example.com
Unmapping example.com from development... OK
</pre>

## <a id='subdomain'></a>サブドメインについての重要な情報 ##

ルートのサブドメインにあたる部分を指定しなくてよい場合もあります。しかし、一般論としてはルートがユニークであることを保証するために必要です。以下のような場合、指定が必要であることに注意してください:

- デフォルトのドメインを使う場合 (たとえば`cfapps.io`)-
独自のドメインを使い、他のアプリケーションでもそのドメインを使う場合。次のことに注意してください。独自のドメインを使う場合、サブドメインもDNSに登録しておく必要があります。

次のような場合はサブドメインを指定しなくてもかまいません。アプリケーションがブラウザからのアクセスを受けない場合と、一つのアプリケーションだけがそのドメインを使う場合です。

## <a id='assign-at-push'></a>Assign Domain and Subdomain at push Time ##

`cf push`を対話的に実行している場合、アプリケーションのためのサブドメインとドメインの入力を求められます。以下の例で、注意が必要です:
 
- アプリケーション名として入力された“myapp,”と“myapp,”と“none”がサブドメインの候補です。ここで文字列を入力することもできます。-
ドメインの候補は、デフォルトの(1) `cfapps.io`、以前に使われた独自ドメインの(2) `example.com`、なし(3)
“none”の三つです。

選択の結果、作成されるルートは以下のようになります:

`myapp.example.com`

<pre class="terminal">
cf push
Name> myapp

Instances> 1

1: 64M
2: 128M
3: 256M
4: 512M
Memory Limit> 256M

Creating myapp... OK

1: myapp
2: none
Subdomain> 1     

1: cfapps.io
2: example.com
3: none Domain> 2

Creating route myapp.example.com... OK
Binding myapp.example.com to myapp... OK

</pre>


## <a id='assign-in-manifest'></a>マニフェストでのサブドメインの指定 ##

マニフェストを使うなら、ルートの構成要素として`host` (subdomain)と
``domain`を記述することができます。詳細については[Application Manifests](../../deploying-apps/manifest.html)をご覧ください。

## <a id='list-routes'></a>ルーティングの表示 ##

`cf routes`コマンドを使って現在のスペースへのルートの一覧を表示できます。ここで、サブドメインは“host”として表示され、ドメインとは分離されていることに注意してください。For example:
<pre class="terminal">
cf routes Getting routes... OK

host                     domain   
myapp                    example.com 
1test                    cfapps.io
sinatra-hello-world      cfapps.io
sinatra-to-do            cfapps.io
</pre>

## <a id='define-route'></a>コマンド・ラインからのルートの設定や変更 ##
`cf map`コマンドでルートを定義したり変更したりできます。この例では、ルート`myapp.example.com`はアプリケーション“myapp”に割り当てられています:
<pre class="terminal">

cf map --app myapp --host myapp --domain example.com
</pre>
`--host`オプションを使ってサブドメインを指定することに注意してください。

ルートを割り当てた時点でアプケーションが動作中だった場合、アプリケーションを再起動してください。アプリケーションが再起動されるまでは新しいルートは有効になりません。

