---
title: Node.jsビルドパックについて
---

Node.jsビルドパックの使い方と拡張については https://github.com/cloudfoundry/heroku-buildpack-nodejs をご覧ください。

Node.jsビルドパックにインストールされたソフトウェアについては、以下の節をご覧ください。

## <a id='software-versions'></a>Node.jsにインストールされたソフトウェア ##

下の表には以下の情報があります:

* **Resource** --- Node.jsビルドパックにインストールされたソフトウェア。
* **Available Versions** --- ビルドパックから利用可能なソフトウェアのヴァージョン。
* **Installed by Default** --- デフォルトで用意されているソフトウェアのヴァージョン。 
* **To Install a Different Version** --- デフォルトとは異なるヴァージョンをインストールするようにビルドパックを変更する方法。

 **最終更新 2013年8月2日**

|Resource |Available Versions |Installed by Default| To Install a Different Version |
| --------- | --------- | --------- |--------- |
|Node.js |0.10.0 - 0.10.6 <br> 0.10.8  - 0.10.12<br>0.8.0 - 0.8.8<br>0.8.10 - 0.8.14<br>0.8.19<br>0.8.21 -  0.8.25<br>0.6.3<br>0.6.5 - 0.6.8<br>0.6.10 - 0.6.18<br>0.6.20<br>0.4.10<br>0.4.7  |latest version of 0.10.x  |To change the default version installed by the buildpack, see <br>“hacking” on https://github.com/cloudfoundry/heroku-buildpack-nodejs. <br><br>To specify the versions of Node.js and npm an application <br>requires, edit the application’s `package.json`, as described in “node.js and npm <br>versions” on https://github.com/cloudfoundry/heroku-buildpack-nodejs.|
|npm |1.3.2<br>1.2.30<br>1.2.21 - 1.2.28<br>1.2.18<br>1.2.14 - 1.2.15<br>1.2.12<br>1.2.10<br>1.1.65<br>1.1.49<br>1.1.40 - 1.1.41<br>1.1.39<br>1.1.35 - 1.1.36<br>1.1.32<br>1.1.9<br>1.1.4<br>1.1.1<br>1.0.10 |latest version of 1.2.x |as above|

