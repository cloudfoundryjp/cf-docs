---
title: Rubyビルドパックについて
---
Rubyビルドパックの使い方と拡張については https://github.com/cloudfoundry/heroku-buildpack-ruby をご覧ください。

Rubyビルドパックにインストールされたソフトウェアについては、以下の節をご覧ください。

## <a id='software-versions'></a>Software Installed by the Ruby Buildpack ##


下の表には以下の情報があります:

* **Resource** --- Rubyビルドパックにインストールされたソフトウェア。
* **Available Versions** --- ビルドパックから利用可能なソフトウェアのヴァージョ>ン。
* **Installed by Default** --- デフォルトで用意されているソフトウェアのヴァージ>ョン。
* **To Install a Different Version** --- デフォルトとは異なるヴァージョンをイン>ストールするようにビルドパックを変更する方法。

**最終更新 2013年8月14日**

|Resource |Available Versions |Installed by Default| To Install a Different Version |
| --------- | --------- | --------- |--------- |
|Ruby |1.8.7  patchlevel 374, Rubygems 1.8.24 <br><br>1.9.2  patchlevel 320, Rubygems 1.3.7.1 <br><br>1.9.3  patchlevel 448, Rubygems 1.8.24 <br><br>2.0.0  patchlevel 247, Rubygems 2.0.3   | The latest security patch release of 1.9.3|Specify desired version in application gem file. |
|Bundler |1.2.1 <br><br>1.3.0.pre.5<br><br>1.3.2 |1.3.2 |Not supported. |



