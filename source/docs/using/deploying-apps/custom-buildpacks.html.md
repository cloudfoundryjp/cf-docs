---
title: カスタム・ビルドパック
---

ビルドパックは、フレームワークとランタイムをまとめる便利な方法です。たとえば、Cloud FoundryはDjangoやPythonをサポートしていません。ビルドパックを使えば、PythonやDjangoのサポートをデプロイする段階で追加することができます。

これらの言語やフレームワークで書かれたアプリケーションがプッシュされる時、必要なビルドパックが自動的にDEA(Droplet Execution Agentの略。アプリはここで実行される)へインストールされます。


## <a id='custom-buildpacks'></a>カスタム・ビルドパック ##

ビルドパックの構造は簡単明瞭です。ビルドパックのリポジトリは三つの主なスクリプトを含み、'bin'フォルダに置きます。

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

build_path = ARGV[0]
cache_path = ARGV[1]

install_ruby

private

def install_ruby
  puts "Installing Ruby"

  # !!!build tasks go here !!!
  # download ruby to cache_path
  # install ruby
end

~~~

### <a id='detect-script'></a>bin/release ###

リリース・スクリプトがCloud Foundryへフィードバック・メタデータを提供し、どのように実行されるべきか指示します。アプリケーションのビルド・ロケーションを引数で指定してこのスクリプトを実行します。このスクリプトは以下の形式のYAMLスクリプトを生成しなければなりません。

~~~yaml
  config_vars:
    name: value
  default_process_types:
    web: commandLine
~~~

`config_vars`がアプリケーションの実行環境の環境変数とします。`default_process_types`は実行されるアプリケーションのタイプと起動コマンドを示します。現時点では`web`タイプのみがサポートされています。

以下の例はRackアプリケーションの`release`スクリプトの返り値を示しています。

~~~ruby

config_vars:
  RACK_ENV: production
default_process_type:
  web: bundle exec rackup config.ru -p $PORT

~~~

## <a id='deploying-with-custom-buildpacks'></a>カスタム・ビルドパックを使ってのデプロイ ##

カスタム・ビルドパックがパブリックなgitリポジトリに置かれていると、アプリケーションをプッシュする時にそのgit URLを使うことができます。

以下にGithubにプッシュされたビルドパックの例を示します;

<pre class="terminal">
$ cf push my-new-app --buildpack=git://github.com/johndoe/my-buildpack.git
</pre>

あるいは、以下のようにプライベートなgitリポジトリを(httpsとユーザ名/パスワード認証とともに)使うことができます。

<pre class="terminal">
$ cf push my-new-app --buildpack=https://username:password@github.com/johndoe/my-buildpack.git
</pre>

アプリケーションがCloud Foundryへデプロイされ、ビルドパックが複製されてアプリケーションへ適用されます。(`detect`スクリプトが0を返すものとします)

