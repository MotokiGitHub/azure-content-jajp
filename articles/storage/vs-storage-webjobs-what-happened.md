<properties
	pageTitle="Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス) | Microsoft Azure"
	description="Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後の、Azure Web ジョブ プロジェクトの変更点について説明します。" 
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="tarcher"/>

# Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)


## リファレンスの追加

Visual Studio プロジェクトで Azure Storage の NuGet パッケージが追加または更新されました。このパッケージは、次の .NET 参照を追加します。

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Azure Storage の接続文字列の追加
選択したストレージ アカウントの接続文字列とキーを使用して、プロジェクトの App.config ファイル内の **AzureWebJobsStorage** エントリと **AzureWebJobsDashboard** エントリが更新されました。

詳細については、[Azure WebJobs の推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)に関するページをご覧ください。

<!---HONumber=Nov15_HO3-->