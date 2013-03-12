---
title: ディレクター
---

ディレクターはBOSHがオーケストレーションを行う上でのコアコンポーネントです。仮想マシンの生成やデプロイ実行を初めとしたソフトウェアとサービスのライフサイクルイベントの制御を行います。CPIがリソースを生成した後の命令、制御はディレクターエージェントに引き渡されます。

ディレクターには、前述したそれぞれのタスクを実行するためのサブコンポーネントが存在します。これらのサブコンポーネントはApiContollerから参照される次のクラスのインスタンスです。
There are specific sub components to manage each of the tasks mentioned above. All these are instances of the following classes referenced from the ApiController.

![director-components](/images/director-components.png)

### デプロイメントマネージャー ###
デプロイメントファイルで指定されたデプロイメントの生成、更新、削除を担います。

ディレクターがデプロイメントマネージャからのアクセスのために公開しているエンドポイントとHTTPメソッドのタイプは以下の通りです。
Endpoints and Http Method type exposed by the director which are used to access the deployment manager are described below.

| URL 	| Http メソッドタイプ	| 概要
| ----------------------------------------------------------------------	| ---------------------------	| ------------------
| /deployments 	| POST	|
| /deployments/:deployment/jobs/:job 	| PUT	| ジョブの状態をパラメータに基づいて変更する
| /deployments/:deployment/jobs/:job/:index/logs 	| GET	| デプロイメントから指定したジョブのログを取得する
| /deployments/:name	| DELETE	| デプロイを削除します

### インスタンスマネージャー ###
インスタンスマネージャーはBOSHデプロイメントを使用して生成された仮想マシンインスタンスの管理を手助けします。

インスタンスマネージャーが提供する機能は例えば以下のようなものです
1. Agent Clientに対するSSHを用いてた想マシンインスタンスへの接続を手助けする
2. 仮想マシンインスタンスを発見する
3. 仮想マシンインスタンスからログを取得する


次の図はユーザがBOSH CLIを用いて仮想マシンへのSSH接続を行う際のフローを示したものです。

![director-instance_manager_1](/images/director-instance_manager_1.png)

### プロブレムマネージャー ###
プロブレムマネージャーはデプロイメントの問題を発見したり対応の実行を行う際の手助けをします。
プロブレムマネージャーは問題についての情報を保存するためのdeployment_problemモデルを持っており、Deploymentモデルと1:manyで結びつけられています。


### プロパティーマネージャー ###
プロパティーはデプロイメントファイル中のジョブに設定された値です。
プロパティマネージャーにより、デプロイメントに関連づけられたプロパティを見つけたり、特定のプロパティを更新したりすることができます。プロパティマネージャーはデプロイメントマネージャーを参照します。


### リソースマネージャー ###
リソースマネージャーはブロブストアに保存されたリソースにアクセスするために使用されます。リソースマネージャーを通して実行される動作は、例えば以下のものが存在します。
Used to get access to the resources stored in the BlobStore. Some of the actions performed through a resource manager are

	1. IDによるリソースの取得
	2. IDにひもづけられたリソースの削除
	3. IDによるリソースパスの取得

### リリースマネージャー ###
リリースマネージャーはリリースの作成や削除を行います。それぞれのリリースはリリースマネージャーを参照しており、テンプレートの配列と共にデプロイメントプランオブジェクトを含んでいます。

ディレクターは以下のエンドポイントへのリクエストをリリースライフサイクルを管理するリリースマネージャーにルーティングします。

| URL 	| Http メソッドタイプ	| レスポンスボディ	| 概要
| -------------	| ---------------------------	| ---------------------------------------------------------------------------------------------------------------------------	| ------------------------------------------------------
| /releases	|        GET	| {"name"     => release.name,"versions" => versions, "in_use"   => versions_in_use}	| アップロードされた全てのリリースのリストを取得する 
| /releases 	|        POST	| 	| ユーザが指定したリリースを生成する


#### リリースのライフサイクル ####
次の図はリリースが生成、更新、削除される際にディレクターの各コンポーネントが行うインタラクションを示します。

![release-lifecycle](/images/director-release-manager.png)


### Stemcellマネージャー ###
StemcellマネージャーはStemcellの生成、削除、および発見を行います。

![director-stemcell-manager](/images/director-stemcell-manager.png)

以下の表はStemcellのライフサイクルを管理するためにディレクターが提供するエンドポイントです。

|     URL 	| Http メソッドタイプ	| レスポンスボディ	| 概要
| -----------------	| ---------------------------	| ---------------------------------------------------------------------------------------------------------------------------	| -------------------------
| /stemcells	|        GET	| { "name" => stemcell.name, "version" => stemcell.version, "cid"     => stemcell.cid}	| Stemcellの名前、バージョンおよびcidを含むJSONデータを取得
| /stemcells 	|        POST	| 	| Stemcellバイナリファイル
| /stemcells	|       DELETE	| 	| 指定したStemellの削除


### タスクマネージャー ###

Task Manager is responsible for managing the tasks which are created and are being run the Job Runner

![director-task-manager](/images/director-task-manager.png)

Following Http Endpoints are exposed by the Director to get information about a task

|     URL 	| Http Method Type	| Response Body	| Description
| -----------------	| ---------------------------	| -----------------	| -------------------------
| /tasks	|        GET	| 	| Get all the tasks being executed of type"update_deployment", "delete_deployment", "update_release","delete_release", "update_stemcell", "delete_stemcell"
| /tasks/:id	|        GET	| 	| Send back output for a task with the given id
| /tasks/:id/output 	|        GET	| 	| Sends back output of given task id and params[:type]
| /task/:id	|       DELETE	| 	| Delete the task specified by a particular Id
	

### ユーザーマネージャー ###
ディレクターのデータベースに保存されているユーザを管理します。ユーザーマネージャーで実行される主な機能は以下の通りです。
Manages the users stored in the Director’s database. Main functions performed by the User Manager are

	1. ユーザの生成
	2. ユーザの削除
	3. ユーザの認証
	4. ユーザの取得
	5. ユーザの更新


ユーザ管理機能は以下のURLにアクセスすることでディレクターからユーザーマネージャーにデリゲートされます。

|     URL 	| Http メソッドタイプ	| Http レスポンスボディ	| 概要
| -----------------	| ---------------------------	| ------------	| -------------------------
| /users	|        POST	| 	| ユーザを作成する	
| /users/:username 	|        PUT	| 	| ユーザを更新する
| /users/:username	|       DELETE	| 	| ユーザを削除する


### VMステートマネージャ ###
VmStateジョブを実行するタスクを生成することで、VMの状態取得を手助けします。

VMの状態はGETリクエストをディレクターの`/deployments/:name/vms` に送信することで取得することが可能です。`name``はデプロイメントの名前を指定します。

![director-vm-state-manager](/images/director-vm-state-manager.png)

