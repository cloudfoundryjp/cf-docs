---
title: Stemcell
---

Stemcellは[BOSH Agent](agent.html)が組み込まれた仮想マシンテンプレートです。Cloud Foundryで使用されるStemcellは標準的なUbuntuディストリビューションです。
Stemcellは[BOSH CLI](#bosh-cli)によってアップロードされ、[BOSH ディレクター](director.html)がクラウドプロバイダインターフェース（CPI)を通して新しい仮想マシンを生成する際に使用されます。
このときディレクターは[Message Bus](messaging.html)や[Blobstore](blobstore.html)のロケーションや認証情報といった、ネットワークとストレージの情報をを伝達します。
