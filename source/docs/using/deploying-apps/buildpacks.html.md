---
title: カスタム・ビルドパックの紹介
---

## <a id='intro'></a>紹介 ##

ビルドパックは、フレームワークとランタイムをまとめる便利な方法です。たとえば、Cloud FoundryはDjangoやPythonをサポートしていません。ビルドパックを使えば、PythonやDjangoのサポートをデプロイする段階で追加することができます。

## <a id='standard-buildpacks'></a>標準的なビルドパック ##

Cloud Foundryは標準的なビルドパックをサポートし、それらの一つを伴うアプリケーションをプッシュする際、簡単にプッシュすることができます。

標準的なビルドパックには以下が含まれます;

* Ruby (Rails, Rack and Sinatra)
* Java (Java_web, Spring, Grails and Play)
* Node

## <a id='custom-buildpacks'></a>独自のビルドパック ##

ビルドパックという概念はHerokuから来ていますが、簡単明瞭です。ビルドパックのリポジトリは三つの主なスクリプトを含み、'bin'フォルダに置きます。

### <a id='detect-script'></a>bin/detect ###

このスクリプトはビルドパックをアプリケーションに適用するかどうかを決めるために使われます。このスクリプトは、引数としてアプリケーションをビルドしたディレクトリを必要とします。ビルドパックがアプリケーションをサポートしている場合、exitコードとして0を返します。スクリプトが0を返した時、フレームワークの名前を標準出力へ出力します。

以下に'Gemfile'を元にしたRubyアプリケーションを検知するRubyスクリプトの例を示します。

~~~ruby

#!/usr/bin/env ruby

gemfile_path = File.join ARGV[0], "Gemfile"

if File.exist?(gemfile_path)
  puts "Ruby"
  exit 0
else
  exit 1
end

~~~
### <a id='detect-script'></a>bin/compile ###

コンパイル・スクリプトはDEA上で実行されるドロップレットのビルドに責任を持ちます。ドロップレットはアプリケーションを実行するのに必要なすべてのコンポーネントを含んでいます。

このスクリプトの引数は二つあります。アプリケーションをビルドしたディレクトリと、キャッシュ・ディレクトリです。後者は、ビルド時にアセットを格納する場所です。

このスクリプトの実行時、標準出力はCFコマンドを通じてエンド・ユーザへ渡されます。一般的な考え方は、'language_pack'で分割することです。右のページに例があります。[https://github.com/cloudfoundry/cloudfoundry-buildpack-java/blob/master/lib/language_pack/java.rb](https://github.com/cloudfoundry/cloudfoundry-buildpack-java/blob/master/lib/language_pack/java.rb)

簡単な例を以下に示します;

~~~ruby

#!/usr/bin/env ruby

#sync output

$stdout.sync = true

build_path = ARGV[0] cache_path = ARGV[1]

install_ruby

private

def install_ruby
  puts "Installing Ruby"

  # !!!build tasks go here !!!  # download ruby to cache_path
  # install ruby
end

~~~

### <a id='detect-script'></a>bin/release ###

リリース・スクリプトがCloud Foundryへフィードバック・メタデータを提供し、アプリケーションのビルド・ロケーションを引数で指定します。

結果はYAMLで返されるのが期待され、Cloud Foundryはconfig\_varsとdefault\_process\_typesの二つのキーを必要とします。

~~~ruby

{
  "config_vars" => {}, # environment variables that should be set
  "default_process_types" => {} #
}.to_yaml

~~~

Rackのアプリケーションが返すデータの例を示します;

~~~ruby

{
  "config_vars" => { "RACK_ENV" => "production" },
  "default_process_types" => { "web" => "bundle exec rackup config.ru -p $PORT" }
}.to_yaml

~~~

この例で、default\_process\_typesが'web'キーをともなう値を持つなら、そのアプリケーションを起動するコマンドを含みます。

## <a id='deploying-with-custom-buildpacks'></a>独自のビルドパックを使ってのデプロイ ##

カスタム・ビルドパックがパブリックなgitに置かれていると、アプリケーションをプッシュする時にそのgit
URLを使うことができます。以下にgithubにプッシュされたビルドパックの例を示します;

<pre class="terminal">
$ cf push my-new-app --buildpack=git://github.com/johndoe/my-buildpack.git
</pre>

アプリケーションがCloud Foundryへデプロイされ、ビルドパックが複製されてアプリケーションへ適用されます。もちろんbin/detectスクリプトが0を返したとして!

