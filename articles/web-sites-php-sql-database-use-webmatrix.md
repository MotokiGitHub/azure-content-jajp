<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="SQL と WebMatrix を使用した Web サイト" pageTitle="SQL データベースと WebMatrix を使用した PHP Web サイト - Azure" metaKeywords="" description="無料の WebMatrix IDE を使用して、SQL データベースにデータを保存する PHP Web サイトを作成および展開する方法を示すチュートリアル。" metaCanonical="" services="" documentationCenter="" title="WebMatrix を使用して PHP Web サイトと SQL データベースを作成および展開する" authors="" solutions="" manager="" editor="mollybos" />





#WebMatrix を使用して PHP Web サイトと SQL データベースを作成および展開する

このチュートリアルでは、Azure SQL データベースを使用する PHP アプリケーションを WebMatrix で作成し、Azure の Web サイトに展開する方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトの開発に必要なものがすべて用意されています。WebMatrix では PHP がサポートされており、PHP 開発用 Intellisense が含まれています。

このチュートリアルは、アプリケーションをローカルでテストできるように、コンピューターに [SQL Server Express][install-SQLExpress] がインストールされていることを前提としています。ただし、SQL Server Express をインストールせずにチュートリアルを完了することもできます。その場合は、アプリケーションを直接 Azure の Web サイトに展開します。

このチュートリアルを完了すると、Azure で動作する PHP-SQL データベース Web サイトが完成します。
 
学習内容:

* 管理ポータルを使用して Azure の Web サイトおよび SQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* WebMatrix を使用して PHP アプリケーションを作成する方法。
* WebMatrix を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。実行中のアプリケーションのスクリーンショットは次のようになります。

![Azure の PHP Web サイト][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##前提条件

1. Tasklist アプリケーションのファイルを [ダウンロード][tasklist-sqlazure-download]します。Tasklist アプリケーションは、タスク一覧のアイテムの追加、完了済みとしてのマーク付け、および削除を行うための、単純な PHP アプリケーションです。タスク一覧のアイテムは SQL データベースに保存されます (ローカル テストには SQL Server Express を使用します)。アプリケーションは、次のファイルで構成されます。

* **index.php**: タスクを表示し、一覧にアイテムを追加するためのフォームを提供します。
* **additem.php**: 一覧にアイテムを追加します。
* **getitems.php**: データベース内のアイテムをすべて取得します。
* **markitemcomplete.php**: アイテムのステータスを "完了済み" に変更します。
* **deleteitem.php**: アイテムを削除します。
* **taskmodel.php**: データベース内のアイテムの追加、取得、更新、および削除を行う関数が含まれています。
* **createtable.php**: アプリケーション用の SQL データベース テーブルを作成します。このファイルは 1 度しか呼び出されません。

2. `tasklist` という SQL Server データベースを作成します。これには、`sqlcmd` コマンド プロンプトで次のコマンドを実行します。

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	この手順は、アプリケーションをローカルでテストする場合にのみ必要です。

## Web サイトと SQL データベースの作成

1. [管理ポータル][preview-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。

	![新しい Azure の Web サイトの作成][NewWebSite1]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![新しい Web サイトのカスタム作成][NewWebSite2]

	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい SQL データベースを作成する]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Web サイトの詳細の入力][NewWebSite3_SQL]

4. データベースの **[名前]** を入力し、**[新しい SQL データベース サーバー]** を選択します。サーバーのログイン名とパスワードを入力し、確認のためパスワードをもう一度入力します。新しい SQL データベース サーバーを作成するリージョンを選択します。

	![SQL データベースの設定の指定][NewWebSite4_SQL]

	Web サイトが作成されると、**"Web サイト [サイト名] の作成に成功しました"** というテキストが表示されます。次に、データベースの接続情報を取得します。

5. **[リンク済みリソース]** をクリックして、データベース名をクリックします。

	![リンク済みリソース][NewWebSite6_SQL]

6. **[接続文字列の表示]** をクリックします。

	![接続文字列][NewWebSite7]
	
表示されたダイアログの **[PHP]** セクションから、`UID`、`PWD`、`Database`、および `$serverName` の値をメモします。この情報は後で使用します。

##WebMatrix のインストール

WebMatrix は、[管理ポータル][preview-portal]からインストールできます。

1. ログインした後、Web サイトのクイック スタート ページに移動して、ページの下部にある WebMatrix アイコンをクリックします。

	![WebMatrix のインストール][InstallWebMatrix]

	表示される手順に従って WebMatrix をインストールします。

2. WebMatrix がインストールされると、サイトを WebMatrix プロジェクトとして開く試行が行われます。ライブ サイトを直接編集するか、ローカル コピーをダウンロードするかを選択できます。このチュートリアルではローカル コピーを編集します。

3. サイトをダウンロードするかどうかを確認するメッセージが表示されたら、**[はい、テンプレート ギャラリーからインストールします]** を選択します。

	![Web サイトをダウンロードする][download-site]

4. 使用できるテンプレートから、**[PHP]** を選択します。

	![テンプレートからサイトを作成する][site-from-template]

5. **[空のサイト]** テンプレートを選択します。サイトの名前を指定し、**[次へ]** をクリックします。

	![サイト名の指定][site-from-template-2]

サイトが WebMatrix で開き、既定のファイルが配置されます。

##アプリケーションの作成

次に示す数ステップで、Tasklist アプリケーションを作成します。これには、ダウンロードしておいたファイルを追加し、いくつかの変更を行います。ただし、独自の既存ファイルを追加することも、新しいファイルを作成することもできます。

1. WebMatrix でサイトを開き、**[既存項目の追加]** をクリックしてアプリケーション ファイルを追加します。

	![WebMatrix - 既存ファイルを追加する][edit_addexisting]

	表示されたダイアログで、ダウンロードしておいたファイルの場所に移動し、すべて選択して、[開く] をクリックします。確認のメッセージが表示されたら、`index.php` ファイルの置き換えを選択します。

2. 次に、ローカル SQL Server データベースの接続情報を `taskmodel.php` ファイルに追加する必要があります。`taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します (**注**: アプリケーションをローカルでテストせず、直接 Azure の Web サイトに発行する場合は、「[アプリケーションの発行](#Publish)」に進んでください)。

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	`taskmodel.php` ファイルを保存します。

3. アプリケーションを実行するには、`items` テーブルを作成する必要があります。`createtable.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。これにより、`createtable.php` がブラウザーで起動し、`tasklist` データベースに `items` テーブルを作成するコードが実行されます。

	![WebMatrix - ブラウザーで createtable.php を起動する][edit_run]

4. これで、アプリケーションのテストをローカルで行うことができます。`index.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。アイテムの追加、完了済みとしてのマーク付け、および削除を行うことにより、アプリケーションをテストします。


<h2><a id="Publish"></a>アプリケーションの発行</h2>

アプリケーションを Azure の Web サイトに発行する前に、`taskmodel.php` ファイル内のデータベース接続情報を、先ほど (「[Web サイトと SQL データベースの作成](#CreateWebsite)」セクションで) 取得した接続情報に更新する必要があります。

1. `taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します。

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web site";
		$db = "value of Database";
	
	`taskmodel.php` ファイルを保存します。

2. WebMatrix で **[発行]** をクリックし、**[発行のプレビュー]** ダイアログの **[続行]** をクリックします。

	![WebMatrix - 発行][edit_publish]

3. `items` テーブルを作成するには、http://[Web サイト名].azurewebsites.net/createtable.php に移動します。

4. 最後に、http://[Web サイト名].azurewebsites.net/index.php に移動してアプリケーションを起動します。
	
##アプリケーションを変更して再発行する

アプリケーションを変更するには、前にダウンロードしたサイトのローカル コピーを編集して、再発行する方法と、リモート モードでサイトを直接編集する方法があります。ここでは、`index.php` ファイルの見出しに簡単な変更を加え、それをライブ サイトへ直接保存します。

1. WebMatrix で目的のサイトの [リモート] タブをクリックし、**[リモート ビューを開く]** を選択します。リモート サイトが開き、直接編集できるようになります。
	 ![WebMatrix - リモート ビューを開く][OpenRemoteView]
 
2. `index.php` ファイルをダブルクリックして開きます。
	![WebMatrix - インデックス ファイルを開く][Remote_editIndex]

3. **title** タグと **h1** タグの **My ToDo List** を **My Task List** に変更し、このファイルを保存します。


4. ファイルを保存したら [実行] ボタンをクリックし、ライブ サイトで変更内容を確認します。
	![WebMatrix - リモートでサイトを実行][Remote_run]



## 次のステップ

これで WebMatrix から Web サイトを作成して Azure に展開する方法はわかりました。WebMatrix の詳細については、次のリソースを参照してください。

* [Azure 用 WebMatrix の概要](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix の Web サイト](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png



[preview-portal]: https://manage.windowsazure.com

