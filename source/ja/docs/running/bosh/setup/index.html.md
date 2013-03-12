---
title: ローカルセットアップ
---

BOSH CLIはMicroBOSHおよびBOSHを操作するためのコマンドラインインターフェース（CLI）です。MicroBOSHやBOSHを使う前には、BOSH CLIをあらかじめインストールしておく必要があります。このページではBOSH CLIのインストール方法を説明します。BOSH CLIは物理マシン、仮想マシンのどちらにでもインストール可能です。

## 必要なもの ##

* BOSH CLIをインストールためには、あらかじめRubyとRubyGemsがをインストールされている必要があります。[Rubyのインストール](/docs/common/install_ruby.html)ページを参照してインストールを行ってください
* BOSHをGithubから取得するために、Gitクライアントが必要です。[Gitのインストール](/docs/common/install_git.html)ページを参照してインストールを行ってください

## ローカルBOSHのインストール ##

BOSH CLIのgemをインストールします：

<pre class="terminal">
$ gem install bosh_cli
</pre>

もしRuby環境の管理にrbenvを使用している場合は、rehashを行ってrbenvにgemを認識させてください：

<pre class="terminal">
$ rbenv rehash
</pre>

## BOSHリリースのインストール ##

Gitを使用してBOSHおよびbosh-releaseレポジトリをクローンします：

<pre class="terminal">
$ git clone git@github.com:cloudfoundry/bosh.git
$ git clone git@github.com:cloudfoundry/bosh-release.git
</pre>

リリース11をbosh-releaseのブランチから取得します（このリリースは最新ではありませんが、新しリリースが機能するまで使用します）：

<pre class="terminal">
$ cd bosh-release
$ git checkout 9e0b649da80a563ba64229069299c57f72ab54ad
</pre>

