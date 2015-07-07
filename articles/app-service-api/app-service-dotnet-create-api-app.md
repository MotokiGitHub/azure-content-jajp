<properties
	pageTitle="Azure App Service での ASP.NET API アプリの作成"
	description="Visual Studio 2013 を使用して、Azure App Service で ASP.NET API アプリを作成する方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Azure App Service での ASP.NET API アプリの作成

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 概要

このチュートリアルでは、Visual Studio 2013 テンプレートを使用して ASP.NET Web API プロジェクトを作成する方法について説明します。このテンプレートでは、プロジェクトを [Azure App Service](../app-service/app-service-value-prop-what-is.md) で [API アプリ](app-service-api-apps-why-best-platform.md)としてクラウドにデプロイできるように構成します。API アプリとしてデプロイできるように既存の Web API プロジェクトを構成する方法については、「[API アプリとしての Web API プロジェクトの構成](app-service-dotnet-create-api-app-visual-studio.md)」を参照してください。

このシリーズの続きのチュートリアルで、このチュートリアルで作成する API アプリ プロジェクトの[デプロイ](app-service-dotnet-deploy-api-app.md)と[デバッグ](../app-service-dotnet-remotely-debug-api-app.md)の方法について説明します。

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

このチュートリアルでは、Azure SDK for .NET 2.5.1 以降のバージョンが必要です。

## API アプリ プロジェクトの作成

1. Visual Studio 2013 を開きます。

2. **[ファイル] メニューの [新しいプロジェクト]** をクリックします。

3. **[ASP.NET Web アプリケーション]** テンプレートを選択します。

4. **[Application Insights をプロジェクトに追加]** チェック ボックスがオフになっていることを確認します。

4. プロジェクトに「*ContactsList*」という名前を付けます。

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. **[OK]** をクリックします。

6. **[新しい ASP.NET プロジェクト]** ダイアログで、**[Azure API アプリ]** プロジェクト テンプレートを選択します。

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. **[OK]** をクリックしてプロジェクトを生成します。

Visual Studio によって、API アプリとしてデプロイされるように構成された Web API プロジェクトが作成されます。

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 次のステップ

これで API アプリをデプロイする準備ができました。[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルの手順に従って実際にデプロイできます。
 

<!---HONumber=62-->