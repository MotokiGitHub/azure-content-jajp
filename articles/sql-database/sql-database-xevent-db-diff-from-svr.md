<properties 
	pageTitle="SQL Database の拡張イベント | Microsoft Azure" 
	description="Azure SQL Database での拡張イベント (XEvents) について、またイベント セッションが Microsoft SQL Server におけるイベント セッションと若干異なる点について説明します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/13/2015" 
	ms.author="genemi"/>


# SQL Database の拡張イベント


このトピックでは、Azure SQL Database での拡張イベントの実装が Microsoft SQL Server の拡張イベントの場合と若干異なる点について説明します。


- SQL Database V12 では 2015 年の後半に拡張イベント機能が追加されました。
- SQL Server には 2008 年から拡張イベント機能があります。
- SQL Database での拡張イベントの機能セットは SQL Server における機能の堅牢なサブセットです。 


*XEvents* は「拡張イベント」の非公式のニックネームで、ブログや他の非公式な場所で使われます。


> [AZURE.NOTE]2015 年 10 月より、Azure SQL Database において拡張イベント セッション機能がプレビュー レベルでアクティブ化します。完全一般公開 (GA) の日付はまだ設定されていません。
> 
> GA のお知らせの際は、Azure の[サービス更新](http://azure.microsoft.com/updates/?service=sql-database)ページに投稿されます。


## 前提条件


このトピックは、以下の知識をある程度持っていることを想定しています。


- [Azure SQL Database サービス](http://azure.microsoft.com/services/sql-database/)。


- Microsoft SQL Server の[拡張イベント](http://msdn.microsoft.com/library/bb630282.aspx)。
 - 拡張イベントに関するドキュメントの大部分は、SQL Server と SQL Database の両方に適用されます。


イベント ファイルを[ターゲット](#AzureXEventsTargets)として選択する際、以下の項目について事前に明確にしておくと便利です。


- [Azure Storage サービス](http://azure.microsoft.com/services/storage/)


- PowerShell
 - [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md) - PowerShell および Azure Storage サービスに関する包括的な情報を提供します。


## コード サンプル


関連するトピックで次の 2 つのコード サンプルを提供します。


- [SQL Database の拡張イベントのためのリング バッファーのターゲット コード](sql-database-xevent-code-ring-buffer.md)
 - 短く単純な Transact-SQL スクリプト。
 - コード サンプルのトピックで強調しているように、リング バッファーのターゲットの作業が完了したら、alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` ステートメントの実行によりそのリソースを解放する必要があります。後ほど、`ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` によりリング バッファーの他のインスタンスを追加できます。


- [SQL Database の拡張イベントのためのイベント ファイル ターゲット コード](sql-database-xevent-code-event-file.md)
 - フェーズ 1 は PowerShell で、Azure Storage コンテナーを作成します。
 - フェーズ 2 は Trasact-SQL で、Azure Storage コンテナーを使用します。


## Transact-SQL の相違点


- [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) コマンドを SQL Server で実行する際は、**ON SERVER** 句を使用します。ところが、SQL Database では **ON DATABASE** 句を使用します。


- **ON DATABASE** 句も [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) および [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL コマンドに適用されます。


- **CREATE EVENT SESSION** または **ALTER EVENT SESSION** ステートメントで **STARTUP\_STATE = ON** のイベント セッション オプションを含ませるベスト プラクティス。
 - **= ON** 値は、フェールオーバーに伴う論理データベース再構成の後の自動再起動をサポートします。


## 新しいカタログ ビュー


拡張イベント機能をサポートする[カタログ ビュー](http://msdn.microsoft.com/library/ms174365.aspx)がいくつかあります。カタログ ビューでは、現在のデータベースにおけるユーザー作成のイベント セッションの*メタデータまたは定義*がわかります。ビューでは、アクティブなイベント セッションのインスタンスについてはわかりません。


| カタログ ビューの名前<br/> | 説明 |
| :-- | :-- |
| **sys.database\_event\_session\_actions** | イベント セッションの各イベントに対する操作ごとに行を返します。 |
| **sys.database\_event\_session\_events** | イベント セッションのイベントごとに行を返します。 |
| **sys.database\_event\_session\_fields** | イベントとターゲットに明示的に設定されたカスタマイズ可能な列ごとに行を返します。 |
| **sys.database\_event\_session\_targets** | イベント セッションに対してイベント ターゲットごとに行を返します。 |
| **sys.database\_event\_sessions** | SQL Database のデータベース内のイベント セッションごとに行を返します。 |


Microsoft SQL Server では、同様のカタログ ビュー名には *.database\_* ではなく、*.server\_* が含まれています。名前のパターンは、**sys.server\_event\_%**のようになっています。


## 新しい動的管理ビュー [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx)


Azure SQL Database には、拡張イベントをサポートする[動的管理ビュー (DMV)](http://msdn.microsoft.com/library/bb677293.aspx)があります。DMV では*アクティブな*イベント セッションについて参照できます。


| DMV の名前 | 説明 |
| :-- | :-- |
| **sys.dm\_xe\_database\_session\_event\_actions** | イベント セッション アクションに関する情報を返します。 |
| **sys.dm\_xe\_database\_session\_events** | セッション イベントに関する情報を返します。 |
| **sys.dm\_xe\_database\_session\_object\_columns** | セッションにバインドされているオブジェクトの構成の値を示します。 |
| **sys.dm\_xe\_database\_session\_targets** | セッション ターゲットに関する情報を返します。 |
| **sys.dm\_xe\_database\_sessions** | 現在のデータベースを対象としたイベント セッションごとに行を返します。 |


Microsoft SQL Server では、同様のカタログ ビューは次のように名前から *\_database* 部分を削除した命名がなされています。


- <br/>**sys.dm\_xe\_database\_sessions** ではなく、**sys.dm\_xe\_sessions** というようにです。


### 両者に共通の DMV


拡張イベントについては、次のような Azure SQL Database と Microsoft SQL Server の両方に共通した DMV も存在します。


- **sys.dm\_xe\_map\_values**
- **sys.dm\_xe\_object\_columns**
- **sys.dm\_xe\_objects**
- **sys.dm\_xe\_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## 使用可能な拡張イベント、アクション、ターゲットを検索


簡単な SQL **SELECT** を実行して、使用可能なイベント、アクション、ターゲットのリストを取得できます。


```
SELECT
		o.object_type,
		p.name         AS [package_name],
		o.name         AS [db_object_name],
		o.description  AS [db_obj_description]
	FROM
		           sys.dm_xe_objects  AS o
		INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
	WHERE
		o.object_type in
			(
			'action',  'event',  'target'
			)
	ORDER BY
		o.object_type,
		p.name,
		o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## SQL Database イベント セッションのターゲット


SQL Database のイベント セッションから結果を取得できるターゲットを次に挙げます。


- [リング バッファー ターゲット](http://msdn.microsoft.com/library/ff878182.aspx) - イベント データを一時的にメモリに保持します。
- [イベント カウンター ターゲット](http://msdn.microsoft.com/library/ff878025.aspx) - 拡張イベント セッションの間に発生したすべてのイベントをカウントします。
- [イベント ファイル ターゲット](http://msdn.microsoft.com/library/ff878115.aspx) - Azure Storage コンテナーに完了したバッファーを書き込みます。


[Event Tracing for Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API は SQL Database の拡張イベントでは使用できません。


## 制限


SQL Database のクラウド環境に利点となるセキュリティ関連の相違点を次にいくつか挙げます。


- 拡張イベントは単一テナントの分離モデルが元になっています。あるデータベースのイベント セッションが他のデータベースからのデータやイベントにアクセスすることはできません。

- **マスター**データベースのコンテキストで、**CREATE EVENT SESSION** ステートメントを実行することはできません。


## 権限モデル


**CREATE EVENT SESSION** ステートメントを実行するには、データベースで**コントロール**権限が必要です。データベース所有者 (dbo) には**コントロール**権限があります。


### ストレージ コンテナーの承認


Azure Storage コンテナーのために生成した SAS トークンには、権限として **rwl** を指定する必要があります。これにより次のアクセスが許可されます。


- 読み取り
- 書き込み
- 一覧表示


## パフォーマンスに関する考慮事項


システム全体にとって不健全と言える程、拡張イベントの使い過ぎによるアクティブメモリの蓄積が起きるシナリオがあります。そのため、Azure SQL Database システムはイベント セッションによって蓄積され得るアクティブ メモリの量に対する制限を動的に設定、調整します。多くの要因が動的な計算に影響します。


メモリの最大量が使用されたというエラー メッセージを受信した場合、次の修正措置を実行することができます。


- 同時実行するイベント セッションを減少させる。


- イベント セッションの **CREATE** と **ALTER** ステートメントにより、**MAX\_MEMORY** 句で指定するメモリ量を減少させる。


### ネットワーク待ち時間


Azure Storage BLOB にデータを保持する際に、**イベント ファイル**ターゲットでネットワークの遅延や障害が発生することがあります。ネットワーク通信の完了を待機する際に、SQL Database の他のイベントが遅延することがあります。この遅延によってワークロードが遅くなる可能性があります。

- このパフォーマンス リスクを防ぐため、イベント セッション定義の **NO\_EVENT\_LOSS** に対して **EVENT\_RETENTION\_MODE** オプションの設定を避けてください。


## 関連リンク


- [Azure Storage における Azure PowerShell の使用](storage-powershell-guide-full.md)。
- [Azure Storage コマンドレット](http://msdn.microsoft.com/library/dn806401.aspx)


- [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md) - PowerShell および Azure Storage サービスに関する包括的な情報を提供します。
- [.NET から BLOB ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md)


- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Jonathan Kehayias の Microsoft SQL Server の拡張イベントに関するブログ投稿](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


拡張イベントの他のコード サンプル トピックは次のリンクから入手可能です。ただし、サンプルが Microsoft SQL Server と Azure SQL Database のどちらを対象にしているのかを常に確認しなければなりません。それにより、サンプルを実行するのにわずかな変更が必要かどうか判断できます。


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=Oct15_HO3-->