<properties
	pageTitle="Elastic Database ジョブの概要 | Microsoft Azure" 
	description="エラスティック データベース ジョブ サービスの説明" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="ddove; sidneyh" />

# Elastic Database ジョブの概要

**Elastic Database ジョブ**機能 (プレビュー) を利用すると、次を含むデータベースのグループ全体で、信頼できる方法で Transact-SQL (T-SQL) スクリプトを実行したり、DACPAC ([データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)) を適用したりできます。

* データベースのカスタム定義コレクション (詳細は後述)
* [Elastic Database プール](sql-database-elastic-pool.md)のすべてのデータベース
* シャード セット ([Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)により作成) 
 
インストール手順については、「[Elastic Database ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。

現在のところ、**Elastic Database ジョブ**は顧客がホストする Azure クラウド サービスです。**ジョブ**と呼ばれるアドホック タスクとスケジュールされた管理タスクを実行できます。ジョブを利用すると、Transact-SQL スクリプトで管理作業を実行することで、大規模なグループの Azure SQL Database を簡単かつ信頼できる方法で管理できます。

![エラスティック データベース ジョブ][1]

## メリット
* スキーマの変更、資格情報の管理、参照データの更新、パフォーマンス データの収集、テナント (顧客) テレメトリの収集を簡単に管理します。
* オーバーヘッド削減: 通常、Transact-SQL ステートメントを実行するか、その他の管理タスクを実行するには、各データベースに個別に接続する必要があります。ジョブを利用すれば、ターゲット グループ内の各データベースのログイン タスクが処理されます。
* アカウンティング: ジョブによりスクリプトが実行され、各データベースの実行状態が記録されます。 
* 柔軟性: Azure SQL Database のカスタム グループを定義する
* Azure SQL Database のグループに対して実行される Transact-SQL スクリプトの定義、保守、維持 
* データ層アプリケーション (DACPAC) をデプロイする
* スクリプトの実行時に自動的に再試行する
* 実行スケジュールを定義する
* Azure SQL Database の集合から 1 つの対象テーブルにデータを集める

> [AZURE.NOTE]Azure ポータルでは、SQL Azure エラスティック プールに限定された一連の少ない機能のみ利用できます。現在のすべての機能にアクセスするには、PowerShell API を使用してください。

## シナリオ

* 新しいスキーマのデプロイなどのパフォーマンスの管理タスク
* 営業時間の終了後に更新が毎週自動実行されるスケジュールも使用して、すべてのデータベース全体で共通の製品情報など、参照データを更新します。
* インデックスを再構築して、クエリのパフォーマンスを向上します。ピーク時以外の時間などに、データベース コレクション全体に対して定期的に実行するように再構築を構成できます。
* データベース セットのクエリ結果をリアルタイムで中央のテーブルに収集します。パフォーマンス クエリを継続的に実行して、その他のタスクの実行をトリガーするように構成できます。
* 顧客の製品利用統計情報収集など、大規模なデータセット全体に対して、時間がかかるデータ処理クエリを実行します。結果は 1 つの対象テーブルに収集され、分析に使用されます。

## Elastic Database ジョブ: エンド ツー エンド 
1.	**Elastic Database ジョブ** コンポーネントをインストールします。詳細については、「[Elastic Database ジョブのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。インストールが失敗した場合は、「[アンインストールする方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。
2.	カスタム定義のデータベース コレクションの作成、スケジュールの追加、結果セットの収集など、その他の機能にアクセスするには、PowerShell API を使用します。**Elastic Database プール**に対して実行するように制限されたジョブの簡単なインストール、作成、監視の場合は、ポータルを使用します。 
3.	ジョブ実行用に暗号化された資格情報を作成し、[グループ内の各データベースにユーザー (またはロール) を追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。
4.	グループ内のすべてのデータベースに対して実行できるべき等 T-SQL スクリプトを作成します。 
5.	Azure ポータルの「[Elastic Database ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」の手順に従い、ジョブを作成します。 
6.	あるいは、PowerShell スクリプトの「[PowerShell を使用した SQL Database のエラスティック データベース ジョブの作成と管理 (プレビュー)](sql-database-elastic-jobs-powershell.md)」を使用します。

## べき等スクリプトの重要性
スクリプトは[べき等](https://en.wikipedia.org/wiki/Idempotence)にする必要があります。簡単に言えば、「べき等」とは、スクリプトが成功し、再度実行されるとき、結果が同じになることを意味します。一時的なネットワークの問題により、スクリプトが失敗することがあります。その場合、ジョブは事前に設定した回数に達するまで自動的にスクリプトを再試行します。べき等スクリプトは、2 回実行して成功した場合、結果が同じになります。

単純な方法として、作成前に、オブジェクトの存在をテストします。

	IF NOT EXIST (some_object)
	-- Create the object 
	-- If it exists, drop the object before recreating it.

同様に、スクリプトは、それが検出する条件を論理的に試験し、対処することで正常に実行できる必要があります。

## エラーとログ

スクリプトが数回の試行後に失敗した場合、ジョブはエラーをログに記録し、続行します。ジョブの終了後 (つまり、グループ内のすべてのデータベースに対して実行した後)、エラーが発生した試行の一覧を確認できます。ログには、問題のあるスクリプトをデバッグするための詳細があります。

## グループの種類と作成

次の 2 つの種類のグループがあります。

1. シャード セット
2. カスタム グループ

シャード セットは [Elastic Database ツール](sql-database-elastic-scale-introduction.md)で作成されます。シャード セット グループを作成すると、データベースが自動的にグループに追加されるか、グループから削除されます。たとえば、新しいシャードは自動的にグループに入ります。ジョブは、調整なしで、グループに対して実行されます。

その一方で、カスタム グループは厳格に定義されます。明示的にカスタム グループにデータベースを追加するか、カスタム グループからデータベースを削除する必要があります。データベースがグループから削除されると、ジョブはデータベースに対してスクリプトを試行し、結果的にエラーが発生します。Azure ポータルで作成されたグループは現在のところ、カスタム グループです。


## コンポーネントと価格 
次のコンポーネントは、連携して管理ジョブのアドホック実行を可能にする Azure クラウド サービスを作成します。コンポーネントはサブスクリプションで、セットアップ時に自動的にインストールされ、構成されます。これらはすべて同じ自動生成された名前を持っているため、サービスを識別できます。名前は一意であり、プレフィックス "edj" とその後に続くランダムに生成された 21 文字で構成されます。

* **Azure Cloud Service**: エラスティック データベース ジョブ (プレビュー) は、顧客によってホストされる AzureCloud Service として配信され、要求されたタスクを実行します。ポータルから、サービスは Microsoft Azure サブスクリプションにデプロイされ、ホストされます。デプロイされる既定のサービスは、高可用性のための 2 つの worker ロールの最小値で実行されます。各 worker ロール (ElasticDatabaseJobWorker) の既定のサイズは A0 インスタンスで実行されます。料金については、「[Cloud Services 料金](http://azure.microsoft.com/pricing/details/cloud-services/)」をご覧ください。 
* **Azure SQL Database**: このサービスは、**管理データベース**と呼ばれる Azure SQL Database を使用してすべてのジョブ メタデータを保持します。既定のサービス層は、S0 です。詳細については、「[SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。
* **Azure Service Bus**: Azure Service Bus は、Azure Cloud Service 内の作業を調整します。「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。
* **Azure Storage**: Azure ストレージ アカウントは問題にさらにデバッグが必要な場合に、診断出力のログ記録を格納するために使用されます ([Azure 診断](cloud-services-dotnet-diagnostics.md)の一般的な方法)。料金については、「[Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

## Elastic Database ジョブの機能
1.	Azure SQL Database には、すべてのメタデータと状態データが保存される管理データベースが指定されています。
2.	実行するジョブの開始と追跡のために、**Elastic Database ジョブ**から管理データベースにアクセスされます。
3.	次の 2 種類のロールが管理データベースと通信します。 
	* コントローラー: 要求されたジョブを実行するタスクが必要なジョブを決定し、新しいジョブ タスクを作成して失敗したジョブを再試行します。
	* ジョブ タスク実行: ジョブ タスクを実行します。

### ジョブ タスクの種類
ジョブを実行するジョブ タスクの種類は複数あります。

* ShardMapRefresh: シャード マップに対してクエリを実行して、シャードとして使用されているすべてのデータベースを決定します
* ScriptSplit: ‘GO’ ステートメント全体のスクリプトを複数のバッチに分割します
* ExpandJob: データベース グループを対象とするジョブから各データベースの子ジョブを作成します
* ScriptExecution: 定義済みの資格情報を使用して、特定のデータベースに対してスクリプトを実行します
* Dacpac: 特定の資格情報を使用して、DACPAC を特定のデータベースに適用します

## エンドツーエンドの実行ワークフロー
1.	ポータルまたは PowerShell API を使用して、ジョブを**管理データベース**に挿入します。ジョブから、特定の資格情報を使用してデータベース グループに対して Transact-SQL スクリプトの実行が要求されます。
2.	コントローラーで、新しいジョブが識別されます。スクリプトを分割し、グループのデータベースを更新するジョブ タスクが作成され、実行されます。最後に、ジョブを展開し、新しい子ジョブを作成する新しいジョブが作成され、実行されます。各子ジョブは、グループ内の個々のデータベースに対して Transact-SQL スクリプトを実行するように指定されています。
3.	コントローラーで、作成された子ジョブが識別されます。コントローラーで、ジョブごとにデータベースに対してスクリプトを実行するジョブ タスクが作成され、トリガーされます。 
4.	すべてのジョブのタスクが完了すると、コントローラーのジョブの状態は完了に更新されます。ジョブ実行中の任意の時点で、ジョブ実行の現在の状態を確認する PowerShell API を使用できます。PowerShell API から返されるすべての時刻は、UTC 形式です。必要に応じて、取り消し要求を開始してジョブを停止できます。 

## 次のステップ
[コンポーネントをインストールし](sql-database-elastic-jobs-service-installation.md)、[データベース グループ内の各データベースにログインを作成し、追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。ジョブの作成と管理の詳細については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO2-->