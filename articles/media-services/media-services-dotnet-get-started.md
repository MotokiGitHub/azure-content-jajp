<properties 
	pageTitle="Media Services SDK for .NET- Azure の概要" 
	description="このチュートリアルでは、Azure メディア サービスと .NET を使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順を紹介します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="juliako"/>


# クイック スタート:Media Services SDK for .NET を使用したビデオ オン デマンド配信 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料評価版サイト</a>をご覧ください

このチュートリアルでは、Azure Media Services (AMS) SDK for .NET を使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順について説明します。 


メディア サービスの基本的なワークフローを示し、メディア サービス開発に必要となる一般的なプログラミング オブジェクトとタスクについても紹介します。このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、ダウンロードして、ストリーミングやプログレッシブ ダウンロードを実行できます。  

## 前提条件
Media Services SDK for .NET で開発を行うには、次の前提条件を満たす必要があります。

- オペレーティング システム:Windows 7、Windows 2008 R2、Windows 8 かそれ以降。
- .NET Framework 4.5 か .NET Framework 4.0
- Visual Studio 2013、Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate、Express のいずれか)。


このクイック スタートでは、次のタスクが表示されます。

1.  ポータルを使用した Media Services アカウントの作成
2.  ストリーミング エンドポイントの構成 (ポータル使用)
3.  Visual Studio プロジェクトの作成と構成
5.  Media Services アカウントへの接続
1.  新しいアセットの作成とビデオ ファイルのアップロード
1.  一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする
1.  エンコードされたアセットの配信ポリシーの構成
1.  アセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する  
1.  コンテンツの再生 


##ポータルを使用した Media Services アカウントの作成

1. [管理ポータル][]で、**[新規]**、**[Media Services]**、**[簡易作成]** の順にクリックします。
   
	![Media Services の簡易作成](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. **[名前]** ボックスに新しいアカウントの名前を入力します。Media Services アカウント名は、使用できる文字が小文字または数字だけで、空白を含めることはできず、長さは 3 ～ 24 文字です。 

3. **[リージョン]** ボックスで、Media Services アカウントのメタデータ レコードを保存するリージョンを選択します。ドロップダウン リストには利用可能な Media Services リージョンだけが表示されます。 

4. **[ストレージ アカウント]** ボックスで、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。新しいストレージ アカウントは同じリージョンに作成されます。 

5. 新しいストレージ アカウントを作成した場合は、**[新しいストレージ アカウント名]** ボックスにストレージ アカウントの名前を入力します。ストレージ アカウントの命名規則は、Media Services アカウントと同じです。

6. フォームの下部にある **[簡易作成]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	アカウントの作成に成功すると、ステータスが [アクティブ] に変化します。 
	
	ページ下部に **[キーの管理]** ボタンが表示されます。このボタンをクリックすると、Media Services のアカウント名とプライマリ キーとセカンダリ キーがダイアログに表示されます。Media Services アカウントにプログラムからアクセスするには、アカウント名とプライマリ キーの情報が必要です。 

	
![Media Services](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	アカウント名をダブルクリックすると、既定で [クイック スタート] ページが表示されます。このページでは、ポータルの別のページでも実行できる管理タスクをいくつか実行できます。たとえば、ビデオ ファイルのアップロードは、このページから実行したり、[コンテンツ] ページから実行したりできます。

	 
##ストリーミング エンドポイントの構成 (ポータル使用)

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure メディア サービスの代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services は、次のアダプティブ ビットレート ストリーミング テクノロジをサポートします。HTTP Live Streaming (HLS)、スムーズ ストリーミング、MPEG DASH、および HDS (Adobe Primetime/Access のライセンス所有者のみ対象)。 

メディア サービスには動的パッケージ化機能があり、アダプティブ ビットレート MP4 やスムーズ ストリーミングでエンコードされたコンテンツを、メディア サービスでサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信できます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- 中間 (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルやアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする (エンコーディングの手順は後述)。  
- コンテンツ配信元となる**ストリーミング エンドポイント**のオンデマンド ストリーミング ユニットを少なくとも 1 つ取得する。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。メディア サービスがクライアントからの要求に応じて適切な応答を構築して返します。 

オンデマンド ストリーミング占有ユニットの数を変更するには、以下の手順を実行します。

1. [管理ポータル](https://manage.windowsazure.com/)で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。

2. [ストリーミング エンドポイント] ページを選択します。次に、変更するストリーミング エンドポイントをクリックします。

3. ストリーミング ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

	![[スケール] ページ](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. [保存] ボタンを押して、変更を保存します。

	新しいユニットの割り当ては完了するまでに約 20 分かかります。 

	 
>[AZURE.NOTE] 現在のところ、ストリーミング占有ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。
>
> コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Media Services の料金詳細](http://go.microsoft.com/fwlink/?LinkId=275107)」に関するページをご覧ください。



##Visual Studio プロジェクトの作成と構成

1. Visual Studio 2013、Visual Studio 2012 か Visual Studio 2010 SP1 で、C# の新しいコンソール アプリケーションを作成します。**[名前]**、**[場所]**、**[ソリューション名]** を入力し、[OK] をクリックします。 

2. Nuget パッケージ [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) を使用して **Azure Media Services .NET SDK Extensions** をインストールします。Media Services .NET SDK Extensions は、コードを簡素化し、メディア サービスによる開発を容易にする一連の拡張メソッドとヘルパー機能です。このパッケージをインストールすると、**Media Services .NET SDK** が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
 
3. System.Configuration アセンブリへの参照を追加します。このアセンブリには、構成ファイル (App.config など) にアクセスするための System.Configuration.ConfigurationManager クラスが含まれています。 

4. App.config ファイルを開き (既定で追加されていない場合はファイルをプロジェクトに追加してください)、ファイルに  *appSettings* セクションを追加します。Azure メディア サービスのアカウント名とアカウント キーの値を設定します。次の例をご覧ください。アカウント名とキー情報を取得するには、Azure の管理ポータルを開いて メディア サービスのアカウントを選択し、**[キーの管理]** をクリックします。


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Program.cs ファイルの先頭にある既存のステートメントを次のコードで上書きします。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. プロジェクト ディレクトリに新しいフォルダーを作成し、エンコード、ストリーミング、プログレッシブ ダウンロードの対象となる .mp4 ファイルか .wmv ファイルをコピーします。この例では、"C:\VideoFiles" というパスを使用します。 

##Media Services アカウントへの接続

メディア サービスを .NET で使用するとき、メディア サービスに関連したプログラミング タスクの大半、たとえば、各種オブジェクト (アセット、アセット ファイル、ジョブ、アクセス ポリシー、ロケーターなど) の作成、更新、アクセス、削除の作業で、**CloudMediaContext** クラスが必要となります。 
 
既定の Program クラスを次のコードで上書きします。このコードは、App.config ファイルから接続値を読み取り、CloudMediaContext オブジェクトを作成してメディア サービスに接続する方法を示しています。メディア サービスへの接続の詳細については、「[Media Services SDK for .NET を使用した メディア サービスへの接続](http://msdn.microsoft.com/library/azure/jj129571.aspx)」をご覧ください。

**Main** 関数で呼び出すメソッドは、この後、定義していきます。
	
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new 
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##新しいアセットの作成とビデオ ファイルのアップロード

メディア サービスで、デジタル ファイルをアセットにアップロードし (取り込み) ます。**Asset** エンティティには、ビデオ、オーディオ、画像、縮小表示のコレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。アセット内のこれらのファイルを**アセット ファイル**といいます。

以下に定義した **UploadFile** メソッドは、(.NET SDK Extensions で定義されている) **CreateFromFile** を呼び出します。**CreateFromFile** によって、指定されたソース ファイルのアップロード先となる新しいアセットが作成されます。 

**CreateFromFile** メソッドの **AssetCreationOptions** 引数には、次のいずれかのアセット作成オプションを指定できます。
 
- **None**: 暗号化は使用されません。これが既定値です。このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。
プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。 
- **StorageEncrypted**: ローカルで AES-256 ビット暗号化を使用し、平文のコンテンツを暗号化したうえで、それを Azure Storage にアップロードします。アップロードされたデータは、暗号化された状態で保存されます。StorageEncrypted で保護されたアセットは、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力アセットとして再びアップロードできます。StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
- **CommonEncryption**: 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
- **EnvelopeEncrypted**: AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

**CreateFromFile** メソッドには、ファイルのアップロードの進行状況をレポートするためのコールバックも指定できます。

以下の例では、アセットのオプションに **None** を指定しています。

次のメソッドを Program クラスに追加します。

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}


##一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする

Media Services に取り込んだアセットには、メディアのエンコード、再パッケージ化、透かしの追加などをクライアントへの配信前に適用することができます。高いパフォーマンスと可用性を確保するために、これらの作業は、複数のバックグラウンド ロール インスタンスに対してスケジューリングされて実行されます。これらの作業は "ジョブ" と呼ばれ、それぞれのジョブは、アセット ファイルに対して実際の処理を行うアトミックなタスクから成ります。 

冒頭で述べたように、Azure メディア サービスの代表的な用途の 1 つは、クライアントに対するアダプティブ ビットレート ストリーミング配信です。Media Services では、一連のアダプティブ ビットレート MP4 ファイルを次の形式のいずれかに動的にパッケージできます。HTTP Live Streaming (HLS)、スムーズ ストリーミング、MPEG DASH、および HDS (Adobe Primetime/Access のライセンス所有者のみ対象)。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- 中間 (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする。  
- コンテンツ配信元となるストリーミング エンドポイントのオンデマンド ストリーミング ユニットを少なくとも 1 つ取得する。 

以下のコードは、エンコーディング ジョブの送信方法を示したものです。このジョブには、**Azure メディア エンコーダー**を使用して mezzanine ファイルを一連のアダプティブ ビットレート MP4 にトランスコードするよう指定するタスクが 1 つ存在します。このコードは、ジョブを送信してその完了を待機します。 

ジョブが完了したら、アセットをストリーミングしたり、トランスコードによって作成された MP4 ファイルをプログレッシブにダウンロードしたりできます。
MP4 ファイルをプログレッシブにダウンロードするためにオンデマンド ストリーミング ユニットを取得する必要はありません。 


次のメソッドを Program クラスに追加します。

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // Submit the job and wait until it is completed.
	    job.Submit();
	
	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;
	
	    Console.WriteLine("Transcoding job finished.");
	
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
	    return outputAsset;
	}

##エンコードされたアセットの配信ポリシーの構成

Media Services のコンテンツを配信するための作業の一環として、アセットの配信ポリシーを構成します。たとえば、アセットの配信にどのようなプロトコルを使用できるか (MPEG DASH、HLS、HDS、スムーズ ストリーミング、またはそのすべてなど) や、アセットを動的に暗号化するかどうか、またどのように暗号化するか (エンベロープ、Common Encryption など) は、アセット配信ポリシーで構成することになります。 

次の **ConfigureClearAssetDeliveryPolicy** メソッドでは、動的暗号化を "なし" と指定し、さらに、MPEG DASH、HLS、スムーズ ストリーミングのいずれかのプロトコルでストリームを配信することを指定する、アセットの配信ポリシーが作成されます。 
  
次のメソッドを Program クラスに追加します。

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }


##アセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する

アセットをストリーミングかダウンロードするにはまず、ロケーターを作成してアセットを "発行" する必要があります。アセットに含まれているファイルには、ロケーターを通じてアクセスできます。Media Services では、2 種類のロケーターがサポートされています。メディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) に使用する OnDemandOrigin ロケーターと、メディア ファイルのダウンロードに使用する Access Signature (SAS) ロケーターです。

ロケーターを作成したら、対象ファイルのストリーミングやダウンロードに使用する URL を作成します。 


スムーズ ストリーミングの ストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS の ストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH のストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

ファイルをダウンロードするための SAS URL の形式は次のとおりです。

	{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK Extensions には、発行済みのアセットの URL を正しい形式で取得できるヘルパー メソッドが用意されています。
 
以下のコードでは、.NET SDK Extensions を使用してロケーターを作成し、ストリーミング URL と プログレッシブ ダウンロード URL を取得しています。また、ローカル フォルダーにファイルをダウンロードする方法もこのコードで確認できます。

次のメソッドを Program クラスに追加します。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming, 
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming, 
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get progressive download URLs for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the progressive download URLs.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##コンテンツの再生  

前のセクションで定義したプログラムを実行すると、コンソール ウィンドウに次のような URL が表示されます。

アダプティブ ストリーミングの URL:

スムーズ ストリーミング

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

プログレッシブ ダウンロードの URL (オーディオとビデオ):    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


スムーズ ストリーミングをテストするには、[http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) または [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) を使用してください。

MPEG DASH をテストするには、[http://dashif.org](http://dashif.org/reference/players/javascript/) を使用してください。

HLS をテストするには、 iOS デバイスや Safari デバイス、あるいは「[3ivx HLS Player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614)」を使用します。 


プログレッシブ ダウンロードをテストするには、ブラウザー (IE、Chrome、Safari など) に URL を貼り付けます。


###その他のリソース
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Azure Media Services 101 - 今すぐビデオをオンラインに)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Azure Media Services 102 - 動的パッケージ化機能とモバイル デバイス)</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理ポータル]: http://manage.windowsazure.com/



<!--HONumber=52--> 