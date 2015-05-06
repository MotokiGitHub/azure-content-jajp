﻿<properties 
	pageTitle="プッシュ通知の使用 (Windows ストア) | モバイル デベロッパー センター" 
	description="Azure Mobile Services と Notification Hubs を使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

このトピックでは、Azure Mobile Services を使用して Windows アプリケーションにプッシュ通知を送信する方法について説明します。 
このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[AZURE.NOTE]このトピックでは Windows ストア プロジェクトで Mobile Services を使用して、プッシュ通知を手動で構成する方法について説明します。Visual Studio 2013 を使用して同じプッシュ通知を Windows ストア プロジェクトに追加できます。詳細については、このチュートリアルの[ユニバーサル Windows アプリ バージョン](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push/)を参照してください。 

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを WNS に登録し、Mobile Services を構成する](#register)
2. [アプリケーションを更新して通知に登録する](#update-app)
3. [サーバー スクリプトを更新してプッシュ通知を送信する](#update-scripts)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。 

>[AZURE.NOTE]このトピックは Windows Phone ストア 8.1 アプリをサポートします。プッシュ通知を Windows Phone 8 アプリまたは Windows Phone Silverlight 8.1 アプリに追加するには、こちらのバージョンの「[Mobile Services アプリへのプッシュ通知の追加](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push)」を参照してください。

##<a id="register"></a>アプリケーションを WNS に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

これで、WNS および Notification Hubs と連携するようにモバイル サービスとアプリケーションが構成されました。次に、ユニバーサル Windows ストア アプリを更新して通知に登録します。

##<a id="update-app"></a>アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の  `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. **App** クラスに次のメソッドを追加します。 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI がプッシュ通知に登録されます。
    
4. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

	これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。 

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6. (省略可能) 管理ポータルで生成されたクイック スタート プロジェクトを使用していない場合は、Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][2]

   	これにより、アプリケーションでトースト通知の使用が有効になります。ダウンロードしたクイック スタート プロジェクトでは、トースト通知があらかじめ有効になっています。

##<a id="update-scripts"></a>サーバー スクリプトを更新してプッシュ通知を送信する

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

+ [認証されたユーザーへのプッシュ通知の送信]
	<br/>タグを使用してモバイル サービスから認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [通知ハブを使用したニュース速報の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [通知ハブを使用したローカライズ ニュース速報の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Notification Hubs とは]
  <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs のデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Notification Hubs ソリューションのトラブルシューティングおよびデバッグのガイダンスについて説明します。 

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[Notification Hubs とは]: /ja-jp/documentation/articles/notification-hubs-overview/
[通知ハブを使用したニュース速報の送信]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[通知ハブを使用したローカライズ ニュース速報の送信]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/



<!--HONumber=42-->