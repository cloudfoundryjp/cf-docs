---
title: Spring Tool Suite
---

> ** The Spring Tool Suite™プラグインは数週以内に更新されます。Cloud Foundry
v2のサポートのためです。オーガナイゼーション、スペース、独自のビルドパックのサポートも含まれます **

Spring Tool Suite™
(STS)は、Eclipseを元にしたSpringによるエンタープライズ・アプリケーションを開発するための最高の開発環境です。STS supplies
toolsはJave、Spring、Eclipseの最新版に対応しています。

本チュートリアルではCloud Foundry
Eclipse拡張のインストール、サーバー・エンドポイントの登録、Springアプリケーションのデプロイの手順を説明します。

## <a id='installing-the-extension'></a>拡張のインストール ##

Cloud
FoundryサポートはEclipse拡張として提供されています。**Dashboard**を選び、次に**Extensions**タブを選びます。*Cloud
Foundry*を検索し、適切な拡張をインストールします。

<img src="/images/sts/install_extension.png" />

## <a id='register-a-server'></a>サーバの登録 ##

Cloud Foundryサーバーを登録できるようになります。**Servers**で右クリックし、**New >
Server**を選びます。**New Server**ダイアログ内のVMWareフォルダで**Cloud
Foundry**が選択されていることを確認します。**Next**をクリックします。

<img src="/images/sts/new_server.png" />

エンドポイントの資格情報を入力し、エンドポイントのタイプを選びます。 (たいていの場合、**VMWare Cloud Foundry**です)
**Finish**をクリックします。**Servers**ペーンに新しいサーバーが追加され、ラベルがVMWare Cloud
Foundryになっているはずです。

<img src="/images/sts/enter_credentials.png" />

新世代のCloud Foundryインスタンスに接続している場合は、オーガナイゼーションとスペースを選ぶよう求めるウィンドウを見るでしょう:

<img src="/images/sts/select_org_and_space.png" />

## <a id='deploying-an-application'></a>Deploy an Application ##

サーバーへデプロイするには、アプリケーションを**Servers**へドラッグするか、Run on
Serverメニューを使います。(緑色の矢印です). Cloud Foundryインスタンスの場合、以下のようなペーンが表示されます:

<img src="/images/sts/deploy-1.png" />

**Next**をクリックします。通常、拡張機能は適切なオプションを自動的に選びますが、URLと割当メモリの調整が必要なこともあります。

<img src="/images/sts/deploy-2.png" />

データ・サービスをバインドし、**Finish**をクリックします。

<img src="/images/sts/deploy-3.png" />

アプリケーションが**Servers**に現われ、Cloud
Foundryインスタンスの下に表示されます。デプロイ後、指定されたURLで応答があるはずです。
