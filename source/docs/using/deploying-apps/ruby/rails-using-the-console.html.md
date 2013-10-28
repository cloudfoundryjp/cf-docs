---
title: Rails 3, コンソールの使用
---

## <a id='intro'></a>紹介 ##

**RailsコンソールはCloud Foundry v2では未サポートです。**

## <a id='invoke'></a>コンソールの起動 ##

Cloud Foundry v2でRailsコンソールが使えるようになれば、以下のようなコマンドで起動できるはずです:

<pre class="terminal"> $ cf console [application name] Opening console on
port 10000... OK irb():001:0> </pre>

見慣れたIRBスタイルのコンソールが表示され、動作中のRailsに接続されています。
