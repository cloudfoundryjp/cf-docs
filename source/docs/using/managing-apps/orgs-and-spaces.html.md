---
title: オーガナイゼーションとスペース
---

オーガナイゼーションとスペースは、アプリケーションやサービスやドメインやユーザを組織化する仕組みです。以下はCloud Foundryにおける組織化の様子を表わしています。

<img src="/images/CF-Arch.png" style='margin:50px auto; display: block;'></img>

## <a id='organizations'></a>オーガナイゼーション ##

Cloud Foundryの中で、オーガナイゼーションは最上位のメタ・オブジェクトです。Cloud
Foundryインスタンスの管理者権限を持つアカウントは、オーガナイゼーションを管理することができます。

## <a id='spaces'></a>スペース ##

オーガナイゼーションはスペースを含むことができます。インストール時のデフォルトでは、**development**, **test**, **production**が存在します。ドメインは複数のスペースに割り当てることができますが、ルートは一つのスペースにのみ割り当てることができます。

## <a id='domains'></a>ドメイン ##

ドメインは、acme.comやfoo.netのようなドメイン名です。Cloud Foundryのインスタンスであるhttps://console.run.pivotal.io に対して、エンド・ユーザのオーガナイゼーションとスペースはシステムのドメイン"cfapps.io"を使うことができます。また、オーガナイゼーションに登録された独自のドメインを使うこともできます。"store.acme.com"の中の"store"のように、ドメインはサブドメインを持ったり、多層にしたりできます。ドメインのオブジェクトはあるオーガナイゼーションに属し、そのオーガナイゼーションの中の0または複数のスペースに関連づけられます。ドメインのオブジェクトはアプリに結びつけられませんが、ドメインのオブジェクトの子はルートと呼ばれます。詳細は[About Domains, Subdomains and Routes](../managing-apps/custom-domains/index.html)をご覧ください。

## <a id='routes'></a>ルート ##

ルートは、ドメインまたはホスト+ドメインをベースにし、一つまたは複数のアプリケーションに付属します。たとえば、"www.acme.com"がルートのとき、"www"がホストで"acme.com"がドメインになります。ホストなしの"acme.com"をルートとすることもできます。詳細は[About Domains, Subdomains and Routes](../managing-apps/custom-domains/index.html)をご覧ください。

## <a id='managmement'></a>管理 ##

[CF](/docs/using/managing-apps/cf/index.html) コマンド、 [API libraries](libs/)、[IDEs](ide/)を使って、オーガナイゼーションを管理することができます。
