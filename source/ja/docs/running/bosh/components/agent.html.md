---
title: エージェント
---

BOSHエージェントとは[BOSHディレクター](director.html)からの指示を受けるために、各仮想マシンにインストールされています。ディレクターとエージェントの交信により、仮想マシンはジョブやCloud Foundryでのロールを与えられます。
例として、仮想マシンに与えられたジョブがMySQLの実行であれば、ディレクターはエージェントに対して、どのパッケージをインストールすればよいのか、そして、どのようにそれらのパッケージを設定すれば良いのかについての情報を伝達します。
