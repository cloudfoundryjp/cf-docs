---
title: Rubyのインストール
---

<!--
Ruby can easily be installed on a variety of operating systems. See the [Ruby website](http://www.ruby-lang.org/en/downloads/) for more details, or the operating system-specific sections below for additional suggestions.
-->

Rubyは、オペレーティングシステムの種類によって簡単にインストール出来ます。詳細は[RubyのWebサイト](http://www.ruby-lang.org/en/downloads/)、またはオペレーティングシステム特有の追加事項については、以下を参照してください

<!--
The RubyGems package management framework can be installed on all operating systems supported by Ruby. See the [RubyGems website](http://rubygems.org/) for more details.
-->

Rubygemsパッケージは、Rubyによってすべてのオペレーティングシステムにインストールすることが出来ます。詳細については、[RubygemsのWebサイト](http://rubygems.org)を参照してください。

* [Ruby on Linux](#linux)
* [Ruby on Mac OS X](#osx)
* [Ruby on Windows](#windows)

## <a id="linux"></a>Ruby on Linux

<!--
There are two good Ruby environment managers available for Linux and Unix-like systems - rvm and rbenv. These environment managers make it easier to manage your Ruby environment and to keep up to date with the latest Ruby releases. These environment managers include RubyGems.
-->

rvmとrbenvは、LinuxとUnixライクなシステムで利用可能なRuby環境マネージャに最適です。これらの環境は、あなたが簡単にRuby環境を管理するために、最新のRubyのリリースと最新の状態に保つようにしてください。これらの環境マネージャにはRubyGemsが含まれています。

* [rvm installation instructions](https://rvm.io/rvm/install/)
* [rbenv installation instructions](https://github.com/sstephenson/rbenv/#installation)

<!--
Ruby can also be installed using the package manager available for each Linux distribution - `apt` on Ubuntu and Debian, `yum` on RedHat/Fedora and Centos, or `yast` on SuSE Linux. When installing using a package manager, install the "ruby" and "rubygems" packages. 
-->

Rubyは様々なLinuxディストリビューションのパッケージマネージャ（UbuntuとDebianのapt、RedHat、FedoraやCentOSのyum、またはSUSE Linuxのyast）を使ってインストールすることが出来ます。パッケージマネージャを利用してインストールする場合は、Rubyとrubygemsのパッケージをインストールします。

## <a id="osx"></a>Ruby on Mac OS X

<!--
OS X version 10.5 and higher ship with Ruby installed, but it may not be the latest version of Ruby. Use of a Ruby environment manager is recommended, as these make it easier to manage your Ruby environment and to keep up to date with the latest Ruby releases. There are two good Ruby environment managers available for OS X - rvm and rbenv. These environment managers include RubyGems.  
-->

OS X 10.5以上にはRubyがインストールされています。しかし、それはRubyの最新バージョンではありません。推奨されるRuby環境マネージャを利用する場合、最新のRubyのリリースと最新の状態に保つ事が簡単にできます。
rvmとrbenvはOS Xで利用可能な最適のRuby環境マネージャです。これらの環境マネージャにはRubyGemsが含まれています。

* [rvm installation instructions](https://rvm.io/rvm/install/)
* [rbenv installation instructions](https://github.com/sstephenson/rbenv/#installation)

<!--
These environment managers, or a newer version of Ruby and RubyGems directly, can also be installed using the [Homebrew](http://mxcl.github.com/homebrew/) package manager. 
-->

これらの環境のマネージャー、またはRubyとRubyGemsの新しいバージョンは、[homebrew]（http://mxcl.github.com/homebrew/）パッケージマネージャを使ってインストールすることができます。


## <a id="windows"></a>Ruby on Windows

<!--
The RubyInstaller project can be used to easily install Ruby on Windows.
-->
RubyInstallerプロジェクトは、Windows上のRubyを簡単に行うことが出来ます。

* [RubyInstaller](http://rubyinstaller.org/)

<!--
There is also a Ruby environment manager available for Windows, named pik.
-->

pikと呼ばれる環境マネージャもまた利用可能です。


* [pik installation instructions](https://github.com/vertiginous/pik#install)

