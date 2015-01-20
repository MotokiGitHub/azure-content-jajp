﻿       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [変更内容](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">プロジェクトの変更点</span>

#####リファレンスの追加

プロジェクトに Azure Mobile Services の NuGet パッケージが追加されました。その結果、次の .NET 参照がプロジェクトに追加されました。

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Mobile Services 用の接続文字列の値

選択したモバイル サービスのアプリケーション URL とアプリケーション キーを使用して、App.xaml.cs ファイル内に **MobileServiceClient** オブジェクトが作成されました。 

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)