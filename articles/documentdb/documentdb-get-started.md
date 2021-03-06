<properties
	pageTitle="NoSQL データベース - DocumentDB .NET SDK の使用 | Microsoft Azure"
	description="データベースを作成し、Azure DocumentDB アカウントを構成する方法について説明します。データベースおよびコレクションを作成し、NoSQL データベース アカウント内に JSON ドキュメントを格納します。"
	keywords="データベースの作成, データベースの作成, NoSQL データベース, NoSQL データベース, NuGet, DocumentDB, Azure, Microsoft Azure"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="11/05/2015"
	ms.author="anhoh"/>

#DocumentDB .NET SDK の使用  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

DocumentDB .NET SDK の使用へようこそ。 このチュートリアルに従うことで、DocumentDB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

ここで説明する操作は以下のとおりです。

- DocumentDB アカウントを作成して接続する
- Visual Studio ソリューションを構成する
- データベースを作成する
- コレクションを作成する
- JSON ドキュメントを作成する
- コレクションをクエリする
- データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) で完全なソリューションを入手できます。手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

その後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## 前提条件

以下のものがそろっていることを確認してください。

- アクティブな Azure アカウント。これがない場合は、[Azure の無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/)。

## 手順 1: DocumentDB アカウントを作成する

DocumentDB アカウントを作成しましょう。使用するアカウントが既にある場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでかまいません。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a>手順 2: Visual Studio ソリューションをセットアップする

1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]**、**[Visual C#]**、**[コンソール アプリケーション]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。
4. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックします。
5. メニューの **[NuGet パッケージの管理...]** をクリックします。
6. **[NuGet パッケージの管理]** ウィンドウの左端のパネルで、**[オンライン]**、**[nuget.org]** の順にクリックします。
7. **[オンライン検索]** 入力ボックスで、**DocumentDB クライアント ライブラリ**を検索します。
8. 結果で **Microsoft Azure DocumentDB クライアント ライブラリ**を探し、**[インストール]** をクリックします。DocumentDB クライアント ライブラリのパッケージ ID は [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) です。

以上です。 これでセットアップは終了です。いくつかのコードの記述を開始しましょう。

##<a id="Connect"></a>手順 3: DocumentDB アカウントに接続する

まず、Program.cs ファイルで、C# アプリケーションの先頭に以下の参照を追加します。

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT]このアプリケーションを完成させるには、上記の依存関係を追加します。

次に、DocumentDB アカウント エンドポイントと、プライマリまたはセカンダリ アクセス キーを保存します。これは、[Azure プレビュー ポータル](https://portal.azure.com)にあります。

![DocumentDB アカウントを示す、アクティブなハブ、[DocumentDB アカウント] ブレードの [キー] ボタン、[キー] ブレードの URI の値、プライマリ キーの値、およびセカンダリ キーの値が強調表示されている Azure プレビュー ポータルのスクリーン ショット][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

**DocumentClient** の新しいインスタンスを作成して、作業の開始デモ アプリケーションを起動します。**GetStartedDemo** という新しい非同期タスクを作成し、新しい **DocumentClient** をインスタンス化します。

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

以下のコードのような **Main** メソッドから非同期タスクを呼び出します。

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]ソース コード内に資格情報を保存することは絶対に避けてください。このサンプルでは、単純化するために資格情報をあえてソース コード内に記述しています。運用環境で資格情報を格納する方法の詳細については、「[Azure Web Sites: How Application Strings and Connection Strings Work (Azure Web Sites: アプリケーション文字列と接続文字列の動作)](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)」を参照してください。ソース コードの外部に資格情報を格納する例については、[GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) のサンプル アプリケーションをご覧ください。

これで、DocumentDB アカウントへの接続方法および **DocumentClient** クラスのインスタンスの作成方法がわかりました。次に、DocumentDB リソースの使用方法について説明します。

## 手順 4: データベースを作成する
[データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) メソッドを使用して作成できます。データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。**DocumentClient** を作成した後、**GetStartedDemo** メソッドで新しいデータベースを作成します。

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>手順 5: コレクションを作成する  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** は新しい S1 コレクションを作成します。これによって価格に影響があります。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) メソッドを使用して作成できます。コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。新しく作成されたコレクションは、[S1 パフォーマンス レベル](documentdb-performance-levels.md)にマップされます。**GetStartedDemo** メソッドでデータベースを作成してから、**FamilyCollection** という名前の新しいコレクションを作成します。

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>手順 6: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) メソッドを使用して作成できます。ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。ここで 1 つ以上のドキュメントを挿入できます。データベースに保存するデータが既にある場合には、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用できます。

最初に、**Parent**、**Child**、**Pet**、**Address**、**Family** の各クラスを作成する必要があります。**GetStartedDemo** の後に次の内部サブクラスを追加することで、これらのクラスを作成します。

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

次に、**GetStartedDemo** 非同期メソッド内でドキュメントを作成します。

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

DocumentDB アカウントで、次のデータベース、コレクション、およびドキュメントを作成しました。

![アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>手順 7: DocumentDB リソースをクエリする

DocumentDB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](documentdb-sql-query.md)をサポートしています。次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。DocumentDB SQL 構文と LINQ の両方が使用されています。これらのクエリを **GetStartedDemo** 非同期メソッドに追加します。

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

次の図では、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるか、および同じロジックが LINQ クエリにも適用されることを示します。

![クエリのスコープおよび意味を示す図](./media/documentdb-get-started/collection-documents.png)

DocumentDB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md#from-clause) キーワードを省略できます。したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。DocumentDB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 8: データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。**GetStartedDemo** 非同期メソッドの末尾に次のコード スニペットを追加することで、データベースとドキュメント クライアントを削除できます。

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>手順 9: アプリケーションを実行する

これで、アプリケーションを実行する準備が整いました。**Main** メソッドの最後に、次のコード行を追加します。このコードにより、アプリケーションの実行が完了する前にコンソール出力を読み取ることができます。

	Console.ReadLine();

Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。

開始したアプリケーションの出力が表示されます。出力では追加したクエリの結果が表示されます。次の例のようなものになるはずです。

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

ご利用ありがとうございます。 最初の DocumentDB アプリの作成が終わりました。

##<a id="GetSolution"></a>完全なソリューションを取得する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]。
-   GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) ソリューション。

Visual Studio で DocumentDB .NET SDK への参照を復元するには、ソリューション エクスプローラーで **GetStarted** ソリューションを右クリックし、**[NuGet パッケージの復元を有効にする]** をクリックします。次に、「[DocumentDB アカウントへの接続](#Connect)」の説明に従って、App.config ファイルの EndpointUrl と AuthorizationKey の値を更新します。

## 次のステップ

-   さらに複雑な ASP.NET MVC のサンプルが必要ですか。 「[DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築](documentdb-dotnet-application.md)」を参照してください。
-	[DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
-	[クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
-	プログラミング モデルの詳細については、[DocumentDB ドキュメント ページ](../../services/documentdb/)の「開発」セクションを参照してください。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Nov15_HO3-->