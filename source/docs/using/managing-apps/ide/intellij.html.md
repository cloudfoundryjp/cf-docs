---
title: IntelliJ IDEA
---

> ** IntelliJプラグインは数週以内に更新されます。Cloud Foundry v2のサポートのためです。organizations, spaces, custom buildpacksも含まれます **

IntelliJ IDEAの作者であるJetBrainsはJVMのCloud Foundry上での動作をサポートします。このためには、IntelliJ IDEAがインストールされていて、Cloud Foundry supportが有効になっている必要があります。

## <a id='adding-a-configuration'></a>設定の追加 ##

Cloud Foundryへデプロイするためには、Run/Debug設定を追加する必要があります。**Run**メニューから**Edit Configurations**を選んでください。**plus sign**をクリックして新しい設定を追加し、メニューから**Cloud Foundry**を選んでください。

<img src="/images/intellij/add-configuration.png" />

Cloud Foundryのインスタンスを選択し、資格情報を追加し、**OK**をクリックしてください。

<img src="/images/intellij/credentials.png" />

**Deployment**タブで**plus
button**をクリックし、デプロイの対象を追加してください。下はGrailsの例で、対象は生成されたWARファイルになります。

<img src="/images/intellij/add-artifact.png" />

対象が追加された後、タイプ、リソース、サービスを設定してください。

<img src="/images/intellij/config-artifact.png" />

## <a id='deploying'></a>アプリケーションのデプロイ ##

**Run**メニューからエントリーを選択します。"Production-CF"が設定の名前なら、"Production-CF"がメニューに書かれます**Run**タブの出力部が下の図と同様になるはずです。

<img src="/images/intellij/deploy-output.png" />

デプロイ時に、対象の名前がURLとして使われます。この例では、grails\_hello-0.1.warが名前なので、URLはhttp://grails\_hello\_0\_1\_war.cloudfoundry.coになります。
