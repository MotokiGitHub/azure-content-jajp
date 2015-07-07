<properties 
	pageTitle="" 
	description="Visual Studio の Azure Mobile Services プロジェクトの変更点について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

### <span id="whathappened">プロジェクトの変更点</id>

##### リファレンスの追加

Azure Mobile Service のライブラリが **MobileServices.js** ファイルの形でプロジェクトに追加されました。
  
##### Mobile Services 用の接続文字列の値 

`services\mobileServices\settings` フォルダー内に新しい JavaScript (.js) ファイルが生成されました。このファイルには、選択したモバイル サービスのアプリケーション URL とアプリケーション キーを格納した **MobileServiceClient** が含まれています。

<!--HONumber=54--> 