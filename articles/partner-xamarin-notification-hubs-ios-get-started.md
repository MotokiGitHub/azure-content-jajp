<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="作業の開始" pageTitle="通知ハブの使用 (Xamarin iOS アプリケーション)" metaKeywords="" description="Azure 通知ハブを使用して Xamarin iOS アプリケーションにプッシュ通知を送信する方法について説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブの使用" authors="" />

# 通知ハブの使用
<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Apple プッシュ通知サービス (APN) を使用してプッシュ通知を受信する空の iOS ストア アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [証明書の署名要求を生成する]
2. [アプリケーションを登録し、プッシュ通知を有効にする]
3. [アプリケーションのプロビジョニング プロファイルを作成する]
4. [通知ハブを構成する]
5. [通知ハブにアプリケーションを接続する]
6. [バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。<!-- Be sure to follow along with the next tutorial to learn how to use notification hubs to address specific users and groups of devices. -->このチュートリアルの前提条件は次のとおりです。

+ [XCode 5.0][Install Xcode]
+ iOS 5.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ
+ [Xamarin.iOS]
+ [Azure モバイル サービス コンポーネント]

   <div class="dev-callout"><b>注</b>
   <p>プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。</p>
   </div>

このチュートリアルを完了することは、iOS アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>メモ</strong><p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service]」を参照してください。



<h2><a name="certificates"></a><span class="short-header">CSR ファイルの生成</span>証明書の署名要求ファイルを生成する</h2>

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. Utilities フォルダーから [Keychain Access] ツールを実行します。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority...]** をクリックします。

  	![][5]

3. **[User Email Address]** を選択し、**[Common Name]** と **[CA Email Address]** の値を入力します。**[Saved to disk]** が選択されていることを確認し、**[Continue]** をクリックします。

  	![][6]

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

  	![][7]
  
  	指定選択した場所に CSR ファイルが保存されます。既定の場所はデスクトップです。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

<h2><a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションをプッシュ通知に登録する</h2>

モバイル サービスから iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリケーションを登録します。

   	![][105] 

2. **[Description]** にアプリケーションの名前を入力し、**[Bundle Identifier]** に値を入力します。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。

   	![][106]

   	![][107] 

   	![][108]
   

	これで、アプリケーション ID が生成され、情報のサブミットが求められます。**[Submit]** をクリックします。
   
   	![][109] 
   
	**[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。
   
   	![][110]

	<div class="dev-callout"><b>注</b>
	<p><strong>[Bundle Identifier]</strong> に <b>MobileServices.Quickstart</b> 以外の値を指定する場合は、Xcode プロジェクトでバンドル ID の値も更新する必要があります。</p>
	 </div>

3. 作成したアプリケーション ID を見つけ、その行をクリックします。

   	![][111]
   
	アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。
   
   	![][112] 
   
   	![][113]

4. **[Edit]** をクリックし、画面の下部までスクロールして、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

   	![][114] 

	これで、[Add iOS Certificate] アシスタントが表示されます。
   
   	![][115] 

	<div class="dev-callout"><b>注</b>
	<p>このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。</p>
	</div>

5. **[Choose File]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[Generate]** をクリックします。

  	![][116]
  
6. ポータルで証明書が作成されたら **[Download]** をクリックし、**[Done]** をクリックします。
 
  	![][118]

  	![][119]  
  
   	これによって、署名証明書がダウンロードされ、コンピューターの **Downloads** フォルダーに保存されます。

  	![][9] 

    <div class="dev-callout"><b>注</b>
	<p>既定では、ダウンロードした開発証明書ファイルの名前は <strong>aps_development.cer</strong> になっています。</p>
    </div>

7. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。

	下図のように、新しい証明書が Keychain にインストールされます。

   	![][10]

	> [WACOM.NOTE]
	> 証明書の名前は異なることがありますが、名前の前に <strong>Apple Development iOS Push Notification Services:</strong> が付けられます。

	後で、この証明書を使用して .p12 ファイルを生成し、それを通知ハブにアップロードして APNS によるプッシュ通知を有効にします。

<h2><a name="profile"></a><span class="short-header">アプリケーションのプロビジョニング</span>アプリケーションのプロビジョニング プロファイルを作成する</h2>
 
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **[+]** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisioning Profile** ウィザードが表示されます。

   	![][120]

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

   	![][121]

3. 次に、**[App ID]** の一覧でモバイル サービスのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

   	![][122]

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。
  
   	![][123]

5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

   	![][124]

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

   	![][125]
   
   	![][126]
	
  	新しいプロビジョニング プロファイルが作成されます。

7. Xcode で、Organizer を開いて [Devices] ビューを選択し、左ウィンドウの **[Library]** セクションにある **[Provisioning Profiles]** を選択して、前の手順で作成したプロビジョニング プロファイルをインポートします。

8. 左側で、デバイスを選択し、プロビジョニング ファイルをもう一度インポートします。

9. Keychain Access で、新しい証明書を右クリックし、**[Export]** をクリックします。証明書の名前を入力して **[.p12]** 形式を選択し、**[Save]** をクリックします。

   	![][18]

  	エクスポートした証明書のファイル名と場所を書き留めます。

これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書を通知ハブにアップロードする必要があります。

<h2><a name="configure-hub"></a><span class="short-header">通知ハブを構成する</span>通知ハブを構成する</h2>

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[サービス バス]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][27]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][28]

4. 前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

   	![][29]

5. 上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   	![][210]

6. 上部にある **[構成]** タブを選択し、Apple 通知設定の **[アップロード]** をクリックします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

   	![][211]

7. 上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][212]

これで、通知ハブが APN と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

<h2><a name="connecting-app"></a><span class="short-header">アプリケーションを接続する</span>通知ハブにアプリケーションを接続する</h2>

1. Xamarin Studio で、新しい iOS プロジェクトを作成し、**[Single View Application]** テンプレートを選択します。

   	![][31]

2. ここでは、Azure モバイル サービス コンポーネントに参照を追加する必要があります。[Solution] ビューで、プロジェクトの **[Components]** フォルダーを右クリックし、**[Get More Components]** を選択します。**Azure モバイル サービス** コンポーネントを検索し、そのコンポーネントをプロジェクトに追加します。

3. **AppDelegate.cs** で、次の using ステートメントを追加します。

		using WindowsAzure.Messaging;

4. **SBNotificationHub** のインスタンスを宣言します。

		private SBNotificationHub Hub { get; set; }
		
5. 次の変数を使用して **Constants.cs** クラスを作成します。

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
	
	
6. **AppDelegate.cs** で、**FinishedLaunching()** を更新して次の内容に合わせます。

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // NOTE: Don't call the base implementation on a Model class
            // see http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

7. **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // NOTE: Don't call the base implementation on a Model class
            // see http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null) 
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                } 

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
        
9. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                //NOTE: If you're using the simple alert by just specifying "  aps:{alert:"alert msg here"}  "
                //this will work fine.  But if you're using a complex alert with Localization keys, etc., 
                //your "alert" object from the aps dictionary will be another NSDictionary... Basically the 
                //json gets dumped right into a NSDictionary, so keep that in mind
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }           
            }
        }

    <div class="dev-callout"><b>注</b>
	<p>ネットワーク接続がないなどの状況に対処するために、**FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。</p>
    </div>

	
10. デバイスでアプリケーションを実行します。

<h2><a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する</h2>

通知は、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1. Visual C# の新しいコンソール アプリケーションを作成します。

   	![][213]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure のサービス バスに参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus。これを入力したら、Enter キーを押します。

2. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

3. `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. 次に、`Main` メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 キーを押してアプリケーションを実行します。デバイスでアラートを受信します。Wi-fi を使用している場合、接続が機能していることを確認します。

Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

モバイル サービスを使用して通知を送信するには、「[モバイル サービスの使用]」に従った後、次の手順を実行します。

1. [Azure 管理ポータル] をクリックします。

2. 上部にある **[スケジューラ]** タブを選択します。

   	![][215]

3. スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

   	![][216]

4. ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5. スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。**[保存]** をクリックします。

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. 下部のバーにある **[一度だけ実行する]** をクリックします。デバイスでアラートを受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Azure 通知ハブの概要]」と「[方法: Azure 通知ハブ (iOS アプリ)]」を参照してください。

<!-- Anchors. -->
[証明書の署名要求を生成する]: #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]: #register
[アプリケーションのプロビジョニング プロファイルを作成する]: #profile
[通知ハブを構成する]: #configure-hub
[通知ハブにアプリケーションを接続する]: #connecting-app
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[モバイル サービス iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[Azure 管理ポータル]: https://manage.windowsazure.com/
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (iOS アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
[Xcode のインストール]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet

[ローカルおよびプッシュ通知プログラミング ガイド]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure モバイル サービス コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
