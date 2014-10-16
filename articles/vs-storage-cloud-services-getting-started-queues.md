<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Azure Storage の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blobs" class="current">BLOB</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Queues">キュー</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tables">テーブル</a></div>

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。詳細については、「[How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)][How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)]」を参照してください。

クラウド サービス プロジェクト内で、プログラムを使用してキューにアクセスするには、次のタスクを実行する必要があります。

1.  Microsoft.WindowsAzure.Storage.dll アセンブリを取得します。それには NuGet を使用します。ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] をクリックします。"WindowsAzure.Storage" をオンライン検索し、[インストール] をクリックして Azure Storage のパッケージと依存関係をインストールします。このアセンブリへの参照をプロジェクトに追加します。
2.  プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

###### ストレージ接続文字列を取得する

キューを使用した操作を行うには、キューを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。クラウド サービス プロジェクトの場合、**CloudConfigurationManager** 型を使用すると、次のコード例に示すように、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include][vs-storage-getting-started-queues-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [BLOB]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blobs"
  [キュー]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues "Queues"
  [テーブル]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tables"
  [How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md