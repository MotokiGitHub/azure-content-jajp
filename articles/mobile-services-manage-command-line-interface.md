<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="コマンド ラインによる管理" pageTitle="コマンド ラインでのモバイル サービスの管理 - Azure チュートリアル" metaKeywords="" description="コマンド ライン ツールを使用して Azure モバイル サービスを作成、展開、および管理する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="コマンド ライン ツールを使用したモバイル サービスの自動化" authors="glenga" solutions="" manager="" editor="" />




# コマンド ライン ツールを使用したモバイル サービスの自動化

このトピックでは、Azure コマンド ライン ツールを使用して、Azure モバイル サービスの作成と管理を自動化する方法を紹介します。このトピックでは、コマンド ライン ツールのインストールとその使用を始めるための方法、およびコマンド ライン ツールを使用して次のモバイル サービス タスクを実行する方法ついて説明します。

-	[新しいモバイル サービスを作成する]
-	[新しいテーブルを作成する]
-   [テーブル操作にスクリプトを登録する][Register a new table script]
-   [テーブルをリストする]
- 	[既存のテーブルを削除する]
-	[モバイル サービスをリストする]
-   [既存のモバイル サービスを削除する]
 
個々のコマンドを単一のスクリプトまたはバッチ ファイルと組み合わせることにより、モバイル サービスの作成、確認、および削除のプロセスを自動化することができます。

Azure コマンド ライン ツールを使用してモバイル サービスを管理するには、Azure モバイル サービス機能を有効にした Azure アカウントが必要です。

+ アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/" target="_blank">Azure の無料評価版サイト</a>を参照してください。

+ 既にアカウントがあっても、Azure モバイル サービスのプレビューを有効にする必要がある場合は、<a href="../create-a-windows-azure-account/#enable" target="_blank">Azure プレビュー機能の有効化に関するページ</a>を参照してください。

このトピックでは、Azure コマンド ライン ツールでサポートされている一般的な管理タスクを取り上げます。詳細については、[Azure コマンド ライン ツールに関するドキュメント][reference-docs]を参照してください。

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a><span class="short-header">ツールのインストール</span>Azure コマンド ライン ツールをインストールする</h2>

次の説明に従って、お使いのオペレーティング システムに対応するコマンド ライン ツールをインストールしてください。

***Windows**: [Azure コマンド ライン ツール インストーラー][windows-installer]をダウンロードします。ダウンロードした .msi ファイルを開き、指示に従ってインストール手順を実行します。

* **Mac**: [Azure SDK インストーラー][mac-installer]をダウンロードします。ダウンロードした .pkg ファイルを開き、指示に従ってインストール手順を実行します。

* **Linux**: 最新バージョンの [Node.js][nodejs-org] をインストールし ([パッケージ マネージャーを使った Node.js のインストールに関するページ][install-node-linux]を参照)、次のコマンドを実行します。

		npm install azure-cli -g

インストールをテストするには、コマンド プロンプトで「`azure`」と入力します。インストールが正常に完了している場合は、使用可能なすべての `azure` コマンドの一覧が表示されます。
<h2><a name="import-account"></a><span class="short-header">設定のインポート</span>発行の設定をダウンロードおよびインポートする方法</h2>

最初に、発行の設定をダウンロードしてインポートする必要があります。これにより、Azure サービスを作成および管理するためのツールを使用できるようになります。発行の設定をダウンロードするには、次のように `account download` コマンドを使用します。

		azure account download

既定のブラウザーが開き、管理ポータルにサインインするよう求められます。サインインすると、`.publishsettings` ファイルがダウンロードされます。このファイルを保存した場所をメモしておきます。

その後で、次のコマンドを実行して `.publishsettings` ファイルをインポートします。ここでは、`<path-to-settings-file>` に、お使いの `.publishsettings` ファイルのパスを指定してください。

		azure account import <path-to-settings-file>

<code>account clear</code> コマンドを使用すると、<code>import</code> コマンドによって格納されたすべての情報を削除できます。

		azure account clear

`account` コマンドのオプションの一覧を表示するには、`-help` オプションを使用します。

		azure account -help

発行の設定をインポートした後は、セキュリティ上の理由から、`.publishsettings` ファイルを削除する必要があります。詳細については、「[Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]」を参照してください。これで、コマンド ラインまたはバッチ ファイルを使用して Azure モバイル サービスの作成および管理を開始する準備が整いました。

<h2><a name="create-service"></a><span class="short-header">サービスの作成</span>モバイル サービスを作成する方法</h2>

コマンド ライン ツールを使用して、新しいモバイル サービス インスタンスを作成することができます。モバイル サービスを作成する間に、SQL データベース インスタンスを新しいサーバーに作成することもできます。

次のコマンドでは、新しいモバイル サービス インスタンスがサブスクリプション内で作成されます。`<service-name>` は新しいモバイル サービスの名前、`<server-admin>` は新しいサーバーのログイン名、`<server-password>` は新しいログインのパスワードです。

		azure mobile create <service-name> <server-admin> <server-password>

指定されたモバイル サービスが既に存在する場合、`mobile create` コマンドは失敗します。自動化スクリプト内では、モバイル サービスの再作成を試行する前に、そのモバイル サービスを削除する必要があります。

<h2><a name="list-services"></a><span class="short-header">サービスのリスト</span>サブスクリプション内の既存のモバイル サービスをリストする方法</h2>

次のコマンドでは、Azure サブスクリプション内のすべてのモバイル サービスのリストが返されます。

		azure mobile list

このコマンドでは、各モバイル サービスの現在の状態と URL も表示されます。

<h2><a name="delete-service"></a><span class="short-header">サービスの削除</span>既存のモバイル サービスを削除する方法</h2>

コマンド ライン ツールを使用して、関連する SQL データベースおよびサーバーと共に既存のモバイル サービスを削除することができます。次のコマンドでは、モバイル サービスが削除されます。`<service-name>` は、削除するモバイル サービスの名前です。

		azure mobile delete <service-name> -a -q

このコマンドでは、`-a` パラメーターと `-q` パラメーターを含めることで、モバイル サービスで使用されている SQL データベースやサーバーも、確認メッセージを表示することなく削除されます。

<div class="dev-callout"><strong>注</strong>
   <p><code>-a</code> または <code>-d</code> と共に <code>-q</code> パラメーターを指定しなかった場合、実行が一時停止され、SQL データベースの削除オプションを選択するように求めるメッセージが表示されます。<code>-a</code> パラメーターは、他のサービスでデータベースまたはサーバーが使用されていない場合にのみ使用してください。それ以外の場合は、削除するモバイル サービスに含まれるデータのみを削除できるように、<code>-d</code> パラメーターを使用してください。</p>
</div>

<h2><a name="create-table"></a><span class="short-header">テーブルの作成</span>モバイル サービスにテーブルを作成する方法</h2>

次のコマンドでは、指定されたモバイル サービスにテーブルが作成されます。`<service-name>` はモバイル サービスの名前、`<table-name>` は作成するテーブルの名前です。

		azure mobile table create <service-name> <table-name>

これにより、新しいテーブルが作成され、テーブル操作 `insert`、`read`、`update`、`delete` に対して、既定のアクセス許可 `application` が設定されます。

次のコマンドでは、パブリックの`読み取り`アクセス許可で新しいテーブルが作成されますが、管理者にのみ`削除`アクセス許可が付与されます。

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

次のテーブルでは、スクリプトのアクセス許可値と [Azure 管理ポータル]でのアクセス許可値を比較しています。

<table border="1" width="100%"><tr><th>スクリプトの値</th><th>管理ポータルの値</th></tr>
<tr><td><code>public</code></td><td>すべてのユーザー</td></tr>
<tr><td><code>application</code>(既定)</td><td> アプリケーション キーを持つユーザー</td></tr>
<tr><td><code>user</code></td><td>認証されたユーザーのみ</td></tr>
<tr><td><code>admin	</code></td><td>スクリプトと管理者のみ</td></tr></table>

指定されたテーブルが存在する場合、`mobile table create` コマンドは失敗します。自動化スクリプト内では、テーブルの再作成を試行する前に、そのテーブルを削除する必要があります。

<h2><a name="list-tables"></a><span class="short-header">テーブルのリスト</span>モバイル サービス内の既存のテーブルをリストする方法</h2>

次のコマンドでは、モバイル サービス内のすべてのテーブルのリストが返されます。`<service-name>` は、モバイル サービスの名前です。

		azure mobile table list <service-name>

このコマンドでは、各テーブルのインデックスの数と、テーブル内に現在存在するデータ行の数も表示されます。

<h2><a name="delete-table"></a><span class="short-header">テーブルの削除</span>モバイル サービスから既存のテーブルを削除する方法</h2>

次のコマンドでは、モバイル サービスからテーブルが削除されます。`<service-name>` はモバイル サービスの名前、`<table-name>` は削除するテーブルの名前です。

		azure mobile table delete <service-name> <table-name> -q

自動化スクリプト内では、実行をブロックする確認プロンプトを表示せずにテーブルを削除するために、`-q` パラメーターを使用します。

<h2><a name="register-script"></a><span class="short-header">スクリプトの登録</span>テーブル操作にスクリプトを登録する方法</h2>

次のコマンドでは、関数をアップロードし、テーブルに対する操作に登録します。`<service-name>` はモバイル サービスの名前、`<table-name>` はテーブルの名前、`<operation>` はテーブル操作 (`read`、`insert`、`update`、`delete`) です。

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

この操作では、JavaScript (.js) ファイルがローカル コンピューターからアップロードされます。ファイル名はテーブル名と操作名で構成し、ファイルは、コマンドが実行される場所の `table` サブフォルダーに配置する必要があります。たとえば、次の操作では、`TodoItems` テーブルに対する新しい `insert` スクリプトがアップロードおよび登録されます。

		azure mobile script upload todolist table/todoitems.insert.js

スクリプト ファイル内の関数宣言は、登録されているテーブル操作にも一致する必要があります。つまり、`insert` スクリプトの場合は、アップロードされたスクリプトに、次のシグネチャを持つ関数が含まれるということです。

		function insert(item, user, request) {
		    ...
		} 

スクリプトの登録方法の詳細については、[モバイル サービスのサーバー スクリプト リファレンス]を参照してください。

<!--<h2><a name="test-service"></a><span class="short-header">サービスのテスト</span>新しいモバイル サービスをテストする</h2>

モバイル サービスの作成を自動化する場合は、必要に応じて cURL または別のコマンドライン要求ジェネレーターを使用することもできます。

## <a name="nextsteps"> </a>次のステップ
次の手順をここに記載....
-->
<!-- Anchors. -->
[コマンド ライン ツールをダウンロードしインストールする]: #install
[発行設定をダウンロードしインポートする]: #import
[新しいモバイル サービスを作成する]: #create-service
[マスター キーを取得する]: #get-master-key
[新しいテーブルを作成する]: #create-table
[新しいテーブル スクリプトを登録する]: #register-script
[既存のテーブルを削除する]: #delete-table
[既存のモバイル サービスを削除する]: #delete-service
[モバイル サービスをテストする]: #test-service
[モバイル サービスをリストする]: #list-services
[テーブルをリストする]: #list-tables
[次のステップ]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Azure 管理ポータル]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /ja-jp/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]: http://go.microsoft.com/fwlink/p/?LinkId=275795

