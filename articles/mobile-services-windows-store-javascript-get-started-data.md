<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="データの使用" pageTitle="データの使用 (Windows ストア JavaScript) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して Windows ストア JavaScript アプリケーションのデータを活用する方法について説明します。" metaCanonical="https://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="モバイル サービスでのデータの使用" authors="glenga" solutions="" manager="" editor="" />


# モバイル サービスでのデータの使用
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	


<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET バックエンド">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>


<p>このトピックでは、Azure のモバイル サービスを使用して Windows ストア アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>

<div class="dev-callout"><b>メモ</b>
<p>このチュートリアルには、Windows ストア アプリをモバイル サービスに簡単に接続できる機能を備えた Visual Studio 2013 が必要です。Visual Studio 2012 を使用して同じ基本手順を実行するには、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js-vs2012/">Visual Studio 2012 を使用したモバイル サービスでのデータの使用</a>」の手順に従ってください。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード][Get the Windows Store app]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<h2><a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード</h2>

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリケーション プロジェクトの [GetStartedWithMobileServices アプリケーション][Developer Code Samples site]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1. JavaScript バージョンの GetStartedWithData サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。

   	![][10]

2. Visual Studio 2012 Express for Windows 8 で、ダウンロードしたプロジェクトを開き、**js** フォルダーを展開して、default.js ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **List** オブジェクトに格納されます。

3. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]  

   	保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

<h2><a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>Visual Studio からの新しいモバイル サービスの作成</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>ソリューション エクスプローラーで、**[サービス]**、**[モバイル サービス]**、**&lt;モバイル サービス名&gt;** の各フォルダーを順に展開します。service.js スクリプト ファイルを開き、新しいグローバル変数を確認します。次に例を示します。</p>

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>このコードは、グローバル変数を使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。このスクリプトへの参照は default.html ファイルに追加されているため、この変数は、このページからも参照されるすべてのスクリプト ファイルで使用できます。</p>
</li>
</ol>

<h2><a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加とアプリケーションの更新</h2>

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

6. default.js スクリプト ファイル内の既存の items コレクションを定義する行をコメント アウトし、次のコード行をコメント解除または追加して、`<yourClient>` を、プロジェクトをモバイル サービスに接続したときに service.js ファイルに追加された変数と置き換えます。

		var todoTable = <yourClient>.getTable('TodoItem');

   	このコードでは、新しいデータベース テーブルのプロキシ オブジェクト (**todoTable**) を作成します。

7. **InsertTodoItem** 関数を次のコードに置き換えます。

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	このコードでは、新しい項目をテーブルに挿入します。

	<div class="dev-callout"><strong>注</strong><p>Id 列のみを持つ新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx">動的スキーマ</a>」を参照してください。</p></div>

8. **RefreshTodoItems** 関数を次のコードで置き換えます。

		var refreshTodoItems = function () {
		    // This code refreshes the entries in the list by querying the table. 
		    todoTable.read().done(function (results) {
		        todoItems = new WinJS.Binding.List(results);
		        listItems.winControl.itemDataSource = todoItems.dataSource;
		    });
		};

   	これにより、todoTable 内で、モバイル サービスから返されたすべての **TodoItem** オブジェクトが格納される項目のコレクションへのバインディングが設定されます。

9. **UpdateCheckedTodoItem** 関数を次のコードで置き換えます。
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

   	これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

<h2><a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト</h2>

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5. アプリケーションで、リストの項目の 1 つをチェックします。ポータルの [参照] タブに戻り、**[最新の情報に更新]** をクリックします。

  	"complete" 値が **false** から **true** に変更されます。

6. default.js プロジェクト ファイルで、既存の **RefreshTodoItems** 関数を、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

   	リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]
  <br/>HTML および JavaScript でモバイル サービスを使用する方法について説明します。

<!-- Anchors. -->

[Windows ストア アプリの入手]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-js
[モバイル サービスの使用]: ./mobile-services-get-started.md
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[C# と XAML]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/
