<properties 
	pageTitle="既存のアプリへの Mobile Services の追加 (Windows Universal) | モバイル デベロッパー センター" 
	description="Mobile Services を使用してユニバーサル Windows アプリでデータを活用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概要

このトピックでは、Azure のモバイル サービスを使用してユニバーサル Windows アプリのデータを活用する方法について説明します。ユニバーサル Windows アプリ ソリューションには、Windows ストア 8.1 と Windows Phone ストア 8.1 の両方のアプリのプロジェクトと、共通の共有プロジェクトが含まれます。詳細については、「[Windows と Windows Phone を対象とするユニバーサル Windows アプリの構築](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)」を参照してください。

このチュートリアルでは、メモリにデータを格納するユニバーサル Windows アプリの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。モバイル サービスを Windows Phone 8.0 または Windows Phone Silverlight 8.1 アプリに接続するには、「[Windows Phone 向けデータの使用](mobile-services-windows-phone-get-started-data.md)」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a> (Update 2 以降のバージョン)。 

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7"><li><p>Solution Explorer で、GetStartedWithData.Shared プロジェクト フォルダーの App.xaml.cs コード ファイルを開き、Windows ストア アプリの条件付きコンパイル ブロック内の <strong>App</strong> クラスに追加された新しい静的フィールドを確認します。これは、次のように表示されます。</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>このコードは、<a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient クラス</a>のインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。</p>
</li>
<li><p>Windows Phone アプリ プロジェクトを右クリックし、<strong>[追加]</strong>、<strong>[接続済みサービス...]</strong> をクリックし、作成されたばかりのモバイル サービスを選択してから、<strong>[OK]</strong> をクリックします。</p>
<p>同じコードは共有 App.xaml.cs ファイルにも追加されますが、今回は Windows Phone アプリの条件付きコンパイル ブロック内に追加されています。</p></li>
</ol>

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。次の手順は、モバイル サービスで新しい TodoItem テーブルを作成することです。

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>モバイル サービスを使用するようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Azure でホストされているモバイル サービスをテストする

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p><a href="https://manage.windowsazure.com/" target="_blank">管理ポータル</a>で、<strong>[モバイル サービス]</strong> をクリックし、対象のモバイル サービスをクリックします。<p></li>
<li><p><strong>[データ]</strong> タブをクリックし、<strong>[参照]</strong> をクリックします。</p>
<p><strong>TodoItem</strong> テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。</p></li>
</ol>
これでチュートリアルは終了します。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次に、以下のその他のトピックのいずれかを読むことを検討してください。

* [認証の使用] <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services C# の使用方法の概念リファレンス](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>.NET で Mobile Services を使用する方法について説明します。
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[認証の使用]: ../mobile-services-windows-store-dotnet-get-started-users.md
[プッシュ通知の使用]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!--HONumber=54--> 