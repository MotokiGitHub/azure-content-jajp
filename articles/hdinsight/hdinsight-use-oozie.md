<properties
	pageTitle="HDInsight での Hadoop Oozie の使用 | Microsoft Azure"
	description="ビッグ データ サービスとして HDInsight で Hadoop Oozie を使用します。Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>


# HDInsight での Oozie と Hadoop を使用したワークフローの定義と実行

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

##概要
HDInsight で Apache Oozie を使用して、ワークフローを定義する方法と実行する方法について説明します。Oozie コーディネーターについては、「[HDInsight での時間ベースの Hadoop Oozie コーディネーターの使用][hdinsight-oozie-coordinator-time]」を参照してください。Azure Data Factory については、「[Data Factory で Pig および Hive を使用する][azure-data-factory-pig-hive]」を参照してください。

##Oozie とは

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。Hadoop スタックと統合されていて、Apache MapReduce、Apache Pig、Apache Hive、Apache Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

このチュートリアルの手順に従って実装するワークフローには次の 2 つのアクションが含まれます。

![ワークフロー図][img-workflow-diagram]

1. Hive アクションは、HiveQL スクリプトを実行して、log4j ファイルのログ レベル タイプごとの出現回数をカウントします。各 log4j ファイルは、タイプと重要度を表す [LOG LEVEL] フィールドを含むフィールド行で構成されています。以下に例を示します。

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	Hive スクリプトの出力は次のようになります。

		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Hive の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」を参照してください。

2.  Sqoop アクションは、Azure SQL データベースのテーブルに HiveQL 出力をエクスポートします。Sqoop の詳細については、「[HDInsight での Hadoop Sqoop の使用][hdinsight-use-sqoop]」を参照してください。

> [AZURE.NOTE]HDInsight クラスターでサポートされている Oozie のバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。

> [AZURE.NOTE]このチュートリアルでは、HDInsight バージョン 3.0 と 2.1 を扱います。このチュートリアルは HDInsight エミュレーターではテストされていません。


##前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。Windows PowerShell スクリプトを実行するには、管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。詳細については、「[Windows PowerShell スクリプトの実行][powershell-script]」を参照してください。
- **HDInsight クラスター**。HDInsight クラスターの作成については、「[HDInsight でカスタム オプションを使用して Hadoop クラスターをプロビジョンする][hdinsight-provision]」または「[HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

	<table border = "1">
	<tr><th>クラスター プロパティ</th><th>Windows PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>HDInsight クラスター名</td><td>$clusterName</td><td></td><td>このチュートリアルを実行する HDInsight クラスター。</td></tr>
	<tr><td>HDInsight クラスター ユーザー名</td><td>$clusterUsername</td><td></td><td>HDInsight クラスターのユーザー名。</td></tr>
	<tr><td>HDInsight クラスター ユーザー パスワード </td><td>$clusterPassword</td><td></td><td>HDInsight クラスター ユーザーのパスワード。</td></tr>
	<tr><td>Azure ストレージ アカウント名</td><td>$storageAccountName</td><td></td><td>HDInsight クラスターで使用できる Azure ストレージ アカウント。このチュートリアルでは、クラスターのプロビジョニング プロセス中に指定された既定のストレージ アカウントを使用します。</td></tr>
	<tr><td>Azure BLOB コンテナー名</td><td>$containerName</td><td></td><td>この例では、既定の HDInsight クラスター ファイル システムで使用する BLOB コンテナーの名前を使用します。既定では、HDInsight クラスターと同じ名前です。</td></tr>
	</table>

- **Azure SQL データベース**。ワークステーションから Azure SQL データベースに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。Azure SQL データベースを作成して、ファイアウォールを構成する手順については、「[Microsoft Azure SQL Database の概要][sqldatabase-get-started]」を参照してください。この記事には、このチュートリアルに必要な Azure SQL データベース テーブルを作成するための Windows PowerShell スクリプトが示されています。

	<table border = "1">
	<tr><th>SQL データベースのプロパティ</th><th>Windows PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>SQL データベース サーバー名</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop によるデータのエクスポート先となる Azure SQL データベース。</td></tr>
	<tr><td>SQL データベース ログイン名</td><td>$sqlDatabaseLogin</td><td></td><td>Azyre SQL データベース ログイン名。</td></tr>
	<tr><td>SQL データベース ログイン パスワード</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Azure SQL データベース ログイン パスワード。</td></tr>
	<tr><td>SQL データベース名</td><td>$sqlDatabaseName</td><td></td><td>Sqoop によるデータのエクスポート先となる Azure SQL データベース。</td></tr>
	</table>

	> [AZURE.NOTE]既定では、Azure SQL データベースは Azure HDinsight などの Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure ポータルから有効にする必要があります。SQL データベースの作成とファイアウォール ルールの構成の手順については、「[Azure SQL データベースの作成と構成方法][sqldatabase-create-configue]」を参照してください。


> [AZURE.NOTE]テーブルに値を入力すると、このチュートリアルを読み進める際に役立ちます。


##Oozie ワークフローと関連 HiveQL スクリプトを定義する

Oozie ワークフロー定義は hPDL (XML プロセス定義言語) で書かれています。既定のワークフロー ファイル名は *workflow.xml* です。ワークフロー ファイルはローカルに保存し、このチュートリアルの後半で Windows PowerShell を使用して HDInsight クラスターにデプロイします。

ワークフローの Hive アクションは、HiveQL スクリプト ファイルを呼び出します。このスクリプト ファイルは HiveQL ステートメントを 3 つ含んでいます。

1. **DROP TABLE ステートメント**は、log4j Hive テーブルが存在する場合、削除します。
2. **CREATE TABLE ステートメント**は、log4j ログ ファイルの場所を指す log4j Hive 外部テーブルを作成します。フィールド区切り記号はコンマ (,) です。既定の行区切り記号は "\\n" です。Hive 外部テーブルは、Oozie ワークフローを複数回実行する場合に、データ ファイルが元の場所から削除されないようにするために使用されています。
3. **INSERT OVERWRITE ステートメント**は、log4j Hive テーブルの各ログ レベル タイプの出現回数をカウントし、その出力を Azure Storage の BLOB に保存します。

Hive パスには既知の問題があります。この問題は Oozie ジョブの送信時に発生します。この問題の修正手順については、TechNet Wiki の「[HDInsight Hive エラー: 名前を変更できません][technetwiki-hive-error]」を参照してください。

**ワークフローによって呼び出される HiveQL スクリプト ファイルを定義するには**

1. 次の内容を持つテキスト ファイルを作成します。

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	スクリプトでは 3 つの変数が使用されています。

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	ワークフロー定義ファイル (このチュートリアルでは workflow.xml) は、実行時にこの HiveQL スクリプトにこれらの値を渡します。

2. **ANSI(ASCII)** エンコードを使用して、ファイルを **C:\\Tutorials\\UseOozie\\useooziewf.hql** として保存します。(テキスト エディターにこのオプションがない場合はメモ帳を使用します。) チュートリアルでは、このスクリプト ファイルは後で HDInsight クラスターにデプロイされます。



**ワークフローを定義するには**

1. 次の内容を持つテキスト ファイルを作成します。

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/>

		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>

		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg>
			    <arg>${sqlDatabaseConnectionString}</arg>
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg>
			    <arg>--export-dir</arg>
			    <arg>${hiveOutputFolder}</arg>
			    <arg>-m</arg>
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>

		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>

		   <end name="end"/>
		</workflow-app>

	ワークフローでは 2 つのアクションが定義されています。start-to アクションは *RunHiveScript* です。このアクションが正常に実行された場合、次のアクションは *RunSqoopExport* です。

	RunHiveScript には、変数がいくつかあります。その値は、Windows PowerShell を使用してワークステーションから Oozie ジョブを送信するときに渡します。

	<table border = "1">
	<tr><th>ワークフローの変数</th><th>説明</th></tr>
	<tr><td>${jobTracker}</td><td>Hadoop ジョブ トラッカーの URL を指定します。HDInsight バージョン 3.0 と 2.1 では <strong>jobtrackerhost:9010</strong> を使用します。</td></tr>
	<tr><td>${nameNode}</td><td>Hadoop 名前ノードの URL を指定します。既定のファイル システム アドレス (たとえば、<i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>) を使用します。</td></tr>
	<tr><td>${queueName}</td><td>ジョブの送信先になるキュー名を指定します。<strong>既定値</strong>を使用します。</td></tr>
	</table>
	
	<table border = "1">
	<tr><th>Hive アクションの変数</th><th>説明</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Hive の CREATE TABLE コマンドのソース ディレクトリを指定します。</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE ステートメントの出力フォルダーを指定します。</td></tr>
	<tr><td>${hiveTableName}</td><td>log4j データ ファイルを参照する Hive テーブルの名前を指定します。</td></tr>
	</table>
	
	<table border = "1">
	<tr><th>Sqoop アクションの変数</th><th>説明</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>Azure SQL データベースの接続文字列を指定します。</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>データのエクスポート先となる Azure SQL データベース テーブルを指定します。</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Hive の INSERT OVERWRITE ステートメントの出力フォルダーを指定します。これは Sqoop エクスポート (export-dir) と同じフォルダーです。</td></tr>
	</table>

Oozie ワークフローとワークフロー アクションの使用の詳細については、[Apache Oozie 4.0 のドキュメント][apache-oozie-400] (HDInsight クラスター バージョン 3.0 の場合) または [Apache Oozie 3.3.2 のドキュメント][apache-oozie-332] (HDInsight バージョン 2.1 の場合) を参照してください。

2. ANSI(ASCII) エンコードを使用して、ファイルを **C:\\Tutorials\\UseOozie\\workflow.xml** として保存します。(テキスト エディターにこのオプションがない場合はメモ帳を使用します。)

##Oozie プロジェクトをデプロイしてチュートリアルを準備する

Windows PowerShell スクリプトを実行して、以下を実行します。

- HiveQL スクリプト (useoozie.hql) を Azure Storage (wasb:///tutorials/useoozie/useoozie.hql)) にコピーします。
- workflow.xml を wasb:///tutorials/useoozie/workflow.xml にコピーします。
- データ ファイル (/example/data/sample.log) を wasb:///tutorials/useoozie/data/sample.log にコピーします。
- Sqoop エクスポート データを格納するための Azure SQL データベース テーブルを作成します。テーブル名は *log4jLogCount* です。

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure Storage の BLOB を使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure ストレージ アカウントと、そのアカウントの特定の BLOB コンテナーを既定のファイル システムとして指定します。プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属するストレージ アカウントをさらに追加することもできます。ストレージ アカウントの追加手順については、「[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。このチュートリアルで使用する Windows PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/useoozie* にある既定のファイル システム コンテナーに格納されています。既定では、このコンテナーの名前は HDInsight クラスター名と同じです。の構文は次のとおりです。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]HDInsight バージョン 3.0 クラスターでは、**wasb://* 構文のみがサポートされます。旧バージョンの **asv://* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされません。

> [AZURE.NOTE]wasb:// パスは仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (以下のコードでは例として workflow.xml を使用しています)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	tutorials/useoozie/workflow.xml

**Hive の内部テーブルと外部テーブルについて**

Hive の内部テーブルと外部テーブルについて知っておく必要のある事項がいくつかあります。

- CREATE TABLE コマンドは、マネージ テーブルとも呼ばれる内部テーブルを作成します。データ ファイルは既定のコンテナーに配置する必要があります。
- CREATE TABLE コマンドは、データ ファイルを既定のコンテナーにある /hive/warehouse/<TableName> フォルダーに移動します。
- CREATE EXTERNAL TABLE コマンドは外部テーブルを作成します。データ ファイルは既定のコンテナーの外部に配置することもできます。
- CREATE EXTERNAL TABLE コマンドはデータ ファイルを移動しません。
- CREATE EXTERNAL TABLE コマンドでは、LOCATION 句で指定されたフォルダーにサブフォルダーがあることが許されていません。チュートリアルで sample.log ファイルのコピーを作成しているのは、これが理由です。

詳細については、「[HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)][cindygross-hive-tables]」を参照してください。

**チュートリアルを準備するには**

1. Windows PowerShell ISE を開きます。(Windows 8 のスタート画面では、「**PowerShell\_ISE**」と入力してから、**[Windows PowerShell ISE]** をクリックします。)詳細については、「[Windows 8 と Windows での Windows PowerShell の起動][powershell-start]」を参照してください)。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、このコマンドレットを再度実行する必要があります。

	> [AZURE.NOTE]Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、<strong>Select-AzureSubscription</strong> コマンドレットを使用して現在のサブスクリプションを選択します。

3. 次のスクリプトをスクリプト ウィンドウにコピーしてから、最初の 6 個の変数を設定します。

		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"

		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"

		# Oozie files for the tutorial
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

3. スクリプト ウィンドウで、スクリプトの末尾に以下を追加します。

		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		function uploadOozieFiles()
		{
		    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
		}

		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}

		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
				[Level] ASC
				)
				)"

			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()

			$conn.close()
		}

		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;

		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;

		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。次のように出力されます。

	![チュートリアルの準備の出力][img-preparation-output]

##Oozie プロジェクトを実行する

現在、Azure PowerShell には Oozie ジョブを定義するコマンドレットが用意されていません。**Invoke-RestMethod** コマンドレットを使用して Oozie Web サービスを呼び出すことができます。Oozie Web サービス API は、HTTP REST JSON API です。Oozie Web サービス API の詳細については、[Apache Oozie 4.0 のドキュメント][apache-oozie-400] (HDInsight クラスター バージョン 3.0 の場合) または [Apache Oozie 3.3.2 のドキュメント][apache-oozie-332] (HDInsight クラスター バージョン 2.1 の場合) を参照してください。

**Oozie ジョブを送信するには**

1. Windows PowerShell ISE を開きます。(Windows 8 のスタート画面では、「**PowerShell\_ISE**」と入力してから、**[Windows PowerShell ISE]** をクリックします。)詳細については、「[Windows 8 と Windows での Windows PowerShell の起動][powershell-start]」を参照してください)。

3. 次のスクリプトをスクリプト ウィンドウにコピーして、最初の 10 個の変数を設定します ($storageUri 変数はスキップします)。

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  

		#Oozie WF variables
		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)


	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

3. スクリプトの末尾に次のコードを追加します。このスクリプトは Oozie ペイロードを定義します。

		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>

		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>

		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>

		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>

		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>

		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>

		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>

		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>

		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>

		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		</configuration>
		"@

4. スクリプトの末尾に次のコードを追加します。このスクリプトは Oozie Web サービスの状態をチェックします。

	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."

5. スクリプトの末尾に次のコードを追加します。この部分は、Oozie ジョブを作成して開始します。

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."

	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6. スクリプトの末尾に次のコードを追加します。このスクリプトは Oozie ジョブの状態をチェックします。

	    # get job status
	    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

	    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $JobStatus = $jsonResponse[0].("status")

	    while($JobStatus -notmatch "SUCCEEDED|KILLED")
	    {
	        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
	        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
	        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	        $JobStatus = $jsonResponse[0].("status")
	    }

	    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7. HDInsight クラスターが Version 2.1 である場合は、"https://$clusterName.azurehdinsight.net:443/oozie/v2/" を "https://$clusterName.azurehdinsight.net:443/oozie/v1/" に置き換えてください。HDInsight クラスター Version 2.1 は、Web サービスの Version 2 をサポートしていません。

8. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。次のように出力されます。

	![チュートリアルのワークフローの実行の出力][img-runworkflow-output]

8. エクスポートしたデータを表示するには、Azure SQL データベースに接続します。

**ジョブのエラー ログを確認するには**

ワークフローのトラブルシューティングを行うには、クラスター ヘッド ノードの *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* または *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* にある Oozie ログ ファイルを参照します。RDP については、「[Azure プレビュー ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-admin-portal]」を参照してください。

**チュートリアルを再実行するには**

ワークフローを再実行するには、以下のものを削除する必要があります。

- Hive スクリプトの出力ファイル
- log4jLogsCount テーブル内のデータ

使用できる Windows PowerShell スクリプトのサンプルを次に示します。

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"

	#SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"

	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()

	$conn.close()


##次のステップ
このチュートリアルでは、Oozie ワークフローを定義する方法と、Windows PowerShell を使用して Oozie ジョブを実行する方法を説明しました。詳細については、次の記事を参照してください。

- [HDInsight での時間ベースの Oozie コーディネーターの使用][hdinsight-oozie-coordinator-time]
- [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
- [HDInsight Emulator の概要][hdinsight-get-started-emulator]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
- [HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]
- [HDInsight の Hadoop での Sqoop の使用][hdinsight-use-sqoop]
- [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
- [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
- [HDInsight 用 C# Hadoop ストリーミング ジョブの開発][hdinsight-develop-streaming-jobs]
- [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/ja-jp/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=Oct15_HO3-->