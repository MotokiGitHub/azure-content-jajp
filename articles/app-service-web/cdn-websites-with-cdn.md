<properties 
	pageTitle="Azure App Service で Azure CDN を使用する" 
	description="統合 Azure CDN エンドポイントからのコンテンツを提供する Web アプリを Azure App Service にデプロイする方法について説明するチュートリアル" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>


#<a name="intro"></a>Azure App Service で Azure CDN を使用する

[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) は、[Azure CDN](/services/cdn/) との統合が可能で、顧客の近くにあるサーバー ノードから Web アプリのコンテンツをグローバルに配信することにより、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) が備えるグローバル スケーリング機能を向上させることができます (現在のノードの場所を示す最新のリストについては、[こちら](http://msdn.microsoft.com/library/azure/gg680302.aspx)を参照してください)。静的な画像を配信するシナリオでは、この統合により、Azure App Service Web Apps のパフォーマンスが飛躍的に向上し、Web アプリのユーザー エクスペリエンスが世界中で大幅に改善します。

Web アプリと Azure CDN の統合には、次の利点があります。

- コンテンツのデプロイメント (画像、スクリプト、およびスタイルシート) を、Web アプリの[継続的なデプロイメント](web-sites-publish-source-control.md) プロセスの一部として統合
- Azure App Service の Web アプリの NuGet パッケージ (たとえば、jQuery バージョン、Bootstrap バージョン) を簡単にアップグレードできる 
- Web アプリケーションと CDN によって配信されるコンテンツを同じ Visual Studio インターフェイスから管理
- ASP.NET のバンドルと縮小を Azure CDN と統合できる。

## 学習内容 ##

Visual Studio の既定の ASP.NET MVC テンプレートを使用して Web アプリを Azure App Service にデプロイし、統合された Azure CDN からコンテンツ (たとえば、画像、コントローラー アクションの結果、既定の JavaScript ファイルおよび CSS ファイル) を配信するコードを追加します。さらに、CDN がオフラインになった場合に提供されるバンドルのフォールバック メカニズムを構成するコードを作成します。

## 前提条件 ##

このチュートリアルの前提条件は次のとおりです。

-	アクティブな [Microsoft Azure アカウント](/account/)
-	Visual Studio 2013 と [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。+ [無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)ことができます。- Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Web Apps など無料の Azure サービスをご利用になれます。+ [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。- MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。
>
> Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="deploy"></a>
## 統合された CDN エンドポイントで Azure に Web アプリをデプロイする ##

このセクションでは、Visual Studio 2013 の既定の ASP.NET MVC アプリケーション テンプレートを App Service にデプロイした後、これを新しい CDN エンドポイントと統合します。次の手順に従ってください。

1. Visual Studio 2013 で、メニュー バーから **[ファイル]、[新規]、[プロジェクト]、[Web]、[ASP.NET Web アプリケーション]** に移動し、新しい ASP.NET Web アプリケーションを作成します。クラウド サービスに名前を付けて、**[OK]** をクリックします。

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. **MVC** を選択し、**[サブスクリプションの管理]** をクリックします。

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. **[サインイン]** をクリックします。

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. サインイン ページで、Azure アカウントをアクティブ化する際に使用した Microsoft アカウントでサインインします。
7. サインインしたら、**[閉じる]** をクリックします。次に、**[OK]** をクリックして続行します。

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Azure で Web アプリを作成していない場合は、Visual Studio でアプリを作成できます。**[Microsoft Azure Websites を構成する]** ダイアログ ボックスで、サイト名が一意になっていることを確認します。次に、**[OK]** をクリックします

	<!--todo: need 2.5.1 screenshot-->![](media/cdn-websites-with-cdn/5-create-website.png)

9. ASP.NET アプリケーションが作成されたら、[Web 発行アクティビティ] ウィンドウで **[`<app name>` をこのサイトに今すぐ発行する]** をクリックして、そのアプリケーションを Azure に発行します。**[発行]** をクリックしてプロセスを完了します。

	<!--todo: need 2.5.1 screenshot-->![](media/cdn-websites-with-cdn/6-publish-website.png)

	発行が完了すると、発行済みの Web アプリがブラウザーに表示されます。

1. CDN エンドポイントを作成するは、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)にログインします。
2. **[新規]**、**[アプリ サービス]**、**[CDN]**、**[簡易作成]** の順にクリックします。**[http://*&lt;sitename>*.azurewebsites.net/]** を選択し、**[作成]** をクリックします。

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE]CDN エンドポイントが作成されると、その URL と統合先のオリジン ドメインが Azure ポータルに表示されます。ただし、新しい CDN エンドポイントの構成がすべての CDN ノードの場所に完全に反映されるまで少し時間がかかる場合があります。

3. Azure ポータルに戻り、**[CDN]** タブで、作成した CDN エンドポイントの名前をクリックします。

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. **[クエリ文字列の有効化]** をクリックし、CDN キャッシュ内のクエリ文字列を有効にします。これを有効にすると、同一のリンクが異なるクエリ文字列でアクセスされた場合は別のエントリとしてキャッシュされます。

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE]チュートリアルのこのセクションでのクエリ文字列の有効化は必須ではありませんが、ここでの変更がすべての CDN ノードに反映されるまで時間がかかるため、できる限り早めに有効にしておくと便利です。また、クエリ文字列非対応コンテンツで CDN キャッシュが停滞するのを防ぐためでもあります (CDN コンテンツの更新については後で説明します)。

2. 次に、CDN エンドポイントのアドレスをクリックします。エンドポイントの準備が完了している場合、Web アプリが表示されます。**HTTP 404** エラーが表示された場合は、CDN エンドポイントの準備ができていません。CDN の構成がすべてのエッジ ノードに反映されるまで、最大で 1 時間待機することが必要になる場合があります。

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. 次に、ASP.NET プロジェクトで **~/Content/bootstrap.css** ファイルにアクセスしてみます。ブラウザーのウィンドウで **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** に移動します。この設定では、次の URL を使用します。

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	この URL は、CDN エンドポイントの次のオリジン URL に対応します。

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	**http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** に移動すると、Azure の Web アプリから bootstrap.css をダウンロードするよう求められます。

	![](media/cdn-websites-with-cdn/12-file-access.png)

同様に、CDN エンドポイントから、**http://*&lt;serviceName>*.cloudapp.net/** のパブリックにアクセスできる任意の URL にアクセスできます。次に例を示します。

-	/Script パスの .js ファイル
-	/Content パスの任意のコンテンツ ファイル
-	任意のコントローラー/アクション 
-	CDN エンドポイントでクエリ文字列が有効になっている場合、クエリ文字列を含む任意の URL
-	すべてのコンテンツがパブリックの場合、Azure Web アプリ全体

Azure CDN から Azure Web アプリ全体を配信することが常に (または一般的に) 最適とは限りません。いくつかの注意点があります。

-	Azure CDN ではプライベートなコンテンツを配信できないため、この方法ではサイト全体をパブリックにする必要があります。
-	予定されたメンテナンスであろうとユーザー エラーであろうと、CDN エンドポイントが何かの理由でオフラインになると、顧客をオリジン URL **http://*&lt;sitename>*.azurewebsites.net/** にリダイレクトできる場合を除き、Web アプリ全体がオフラインになります。 
-	カスタムの Cache-Control 設定 (「[Azure Web アプリの静的ファイルのキャッシュ オプションを構成する](#caching)」を参照してください) を使用した場合でも、CDN エンドポイントによって高度に動的なコンテンツのパフォーマンスが向上するわけではありません。上に示すように CDN エンドポイントからホーム ページを読み込もうとした場合、非常に単純な既定のホーム ページを初めて読み込むときに 5 秒以上かかります。このページに毎分更新する必要がある動的コンテンツが含まれていたとしたら、クライアント エクスペリエンスはどうなるでしょうか。動的コンテンツを CDN エンドポイントから配信するにはキャッシュの有効期限が短く設定されている必要があります。これは、CDN エンドポイントで頻繁にキャッシュ ミスが発生することになります。その結果、Azure Web アプリのパフォーマンスが低下し、CDN の目的が果たせなくなります。

また、Azure Web アプリで Azure CDN から配信するコンテンツをケースバイケースで決定する方法もあります。CDN エンドポイントから個々のコンテンツ ファイルにアクセスする方法については既に説明しました。CDN エンドポイントを介して特定のコントローラー アクションを配信する方法については、「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」で説明します。

<a name="caching"></a>
## Azure Web アプリの静的ファイルのキャッシュ オプションを構成する ##

Azure CDN 統合を Azure Web アプリに組み込むと、CDN エンドポイントで静的コンテンツをどのようにキャッシュするかを指定できます。そのためには、ASP.NET プロジェクト (例: **cdnwebapp**) から *Web.config* を開き `<system.webServer>` に `<staticContent>` 要素を追加します。次の XML では、3 日間で有効期限が切れるキャッシュを追加しています。<pre class="prettyprint"> &lt;system.webServer&gt; <mark>&lt;staticContent&gt; &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt; &lt;/staticContent&gt;</mark> ... &lt;/system.webServer&gt; </pre>

この操作を実行すると、Azure Web アプリ内のすべての静的ファイルは、CDN キャッシュ内で同じ規則に従います。キャッシュ設定をより細かく制御するには、*Web.config* ファイルをフォルダーに追加し、そこに設定を追加します。たとえば、*Web.config* ファイルを *\Content* フォルダーに追加して、その内容を次の XML で置き換えます。

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

この設定は、*\Content* フォルダーのすべての静的ファイルを 15 日間キャッシュすることを指定しています。

`<clientCache>` 要素を構成する方法については、[クライアント キャッシュ &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache) に関するページを参照してください。

「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」では、CDN キャッシュ内のコントローラー アクションの結果に対してキャッシュ設定を構成する方法についても説明します。

<a name="controller"></a>
## Azure CDN を介してコントローラー アクションからコンテンツを配信する ##

Azure CDN と Web アプリを統合すると、Azure CDN を介してコントローラー アクションからコンテンツを比較的簡単に配信できます。この場合も、CDN を介して Azure Web アプリ全体を配信する場合は、すべてのコントローラー アクションが既に CDN から到達可能なため、この手順は不要です。ただし、「[統合された CDN エンドポイントで Azure に Web アプリをデプロイする](#deploy)」で指摘したとおり、この手順の代わりに、Azure CDN から配信するコントローラー アクションを選択できます。[Maarten Balliauw](https://twitter.com/maartenballiauw) 氏が、[Azure CDN による Web の遅延時間の短縮](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)に関する講演の中でおもしろい MemeGenerator コントローラーを使用した方法を紹介しています。ここではその方法を再現します。

Web アプリで次のような Chuck Norris の若いときの画像 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) による撮影) に基づいてミームを生成するとします。

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

ここで使用する `Index` アクションでは、画像内にこのようなジョークを指定できます。顧客がこのアクションに投稿するとミームが生成されます。題材が Chuck Norris のため、このページは全世界で受け入れられると予想されます。これは、Azure CDN で半動的コンテンツを配信するよい例です。

上記の手順を実行して、このコントローラー アクションを設定します。

1. *\Controllers* フォルダーに、*MemeGeneratorController.cs* という名前の新しい .cs ファイルを作成し、その内容を次のコードで置き換えます。強調表示されている箇所は、ファイル パスと CDN 名で置き換えてください。
	<pre class="prettyprint">
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.IO;
using System.Net;
using System.Web.Hosting;
using System.Web.Mvc;
using System.Web.UI;

namespace cdnwebapp.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

        public ActionResult Index()
        {
            return View();
        {

        [HttpPost, ActionName("Index")]
    	public ActionResult Index_Post(string top, string bottom)
        {
            var identifier = Guid.NewGuid().ToString();
            if (!Memes.ContainsKey(identifier))
            {
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            {

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        {

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            {

            if (Debugger.IsAttached) // デバッグ操作を維持
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            {
            else // Azure CDN のコンテンツを取得
            {
                return Redirect(string.Format("http://<mark>&lt;yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            {
        {

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

            using (Graphics graphics = Graphics.FromImage(bitmap))
            {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                {
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                {
            {

            MemoryStream ms = new MemoryStream();
            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
            return File(ms.ToArray(), "image/png");
        {

        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
        {
            // 実際のサイズを計算、必要に応じて縮小
            while (true)
            {
                size = g.MeasureString(text, font);

                // 収まる、バック アウト
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // より小さいフォントで試す (古いサイズの 90%)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            {
        {
    {
{
</pre>

2. 既定の `Index()` アクション内で右クリックし、**[ビューの追加]** を選択します。

	![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)

3.  次の既定の設定をそのまま受け入れ、**[追加]** をクリックします。

	![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)

4. 新しい *Views\MemeGenerator\Index.cshtml* を開き、その内容を、ジョークを送信するための次の単純な HTML で置き換えます。

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. もう一度 Azure Web アプリに発行し、ブラウザーで **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** に移動します。

フォームの値を `/MemeGenerator/Index` に送信すると、`Index_Post` アクション メソッドにより、`Show` アクション メソッドへのリンクとそれぞれの入力識別子が返されます。リンクをクリックすると、次のコードに到達します。<pre class="prettyprint"> [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)] public ActionResult Show(string id) { Tuple&lt;string, string&gt; data = null; if (!Memes.TryGetValue(id, out data)) { return new HttpStatusCodeResult(HttpStatusCode.NotFound); }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
} </pre>

ローカル デバッガーが接続されている場合は、ローカル リダイレクトにより通常のデバッグ エクスペリエンスが得られます。Azure Web アプリで実行されている場合は、次の場所にリダイレクトされます。

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

URL の書き換え規則が適用された後、CDN エンドポイントにキャッシュされる実際のファイルは次のファイルになります。

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

次に、`Generate` メソッドの `OutputCacheAttribute` 属性を使用して、Azure CDN に有効な、アクション結果をキャッシュする方法を指定できます。次のコードでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同様に、任意のキャッシュ オプションを使用し、Azure CDN を介して Azure Web アプリの任意のコントローラー アクションからコンテンツを配信できます。

次のセクションでは、バンドルされたスクリプト、縮小されたスクリプト、および CSS を Azure CDN 経由で提供する方法について説明します。

<a name="bundling"></a>
## ASP.NET のバンドルと縮小を Azure CDN と統合できる。 ##

スクリプトや CSS スタイルシートはあまり頻繁に変更されないため、Azure CDN キャッシュの有力な候補になります。Azure CDN を介して Web アプリ全体を配信することは、バンドルや縮小を Azure CDN と統合する方法として最も簡単です。ただし、「[Azure CDN エンドポイントを Azure Web アプリと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)」で説明した理由から、この方法を選択しない場合のために、ASP.NET のバンドルと縮小の開発者エクスペリエンスを維持しながらこれを実現する方法を紹介します。

-	優れたデバッグ モード エクスペリエンス
-	合理的なデプロイ
-	スクリプトや CSS のバージョンをアップグレードするためのクライアントの即時の更新
-	CDN エンドポイントのに障害に対するフォールバック メカニズム
-	最小限のコードの変更

「[Azure CDN エンドポイントを Azure Web アプリと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)」で作成した ASP.NET プロジェクトで、*App_Start\BundleConfig.cs* を開き、`bundles.Add()` メソッドの呼び出しを確認します。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

最初の `bundles.Add()` ステートメントは、スクリプト バンドルを仮想ディレクトリ `~/bundles/jquery` で追加します。次に、*Views\Shared_Layout.cshtml* を開いて、スクリプト バンドル タグがどのようにレンダリングされているかを確認します。次の Razor コードが見つかります。

    @Scripts.Render("~/bundles/jquery")

Azure Web アプリでこの Razor コードが実行されると、次のようなスクリプト バンドルのための `<script>` タグがレンダリングされます。

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

ただし、`F5` キーを押してこのコードを Visual Studio 内で実行した場合は、バンドル内の各スクリプト ファイルが個別にレンダリングされます (上記のケースでは、バンドルに含まれているスクリプト ファイルは 1 つだけです)。

    <script src="/Scripts/jquery-1.10.2.js"></script>

これにより、JavaScript コードを開発環境でデバッグできる一方で、運用環境での同時クライアント接続数 (バンドル) を削減し、ファイルのダウンロード パフォーマンス (縮小) を高めることができます。これは、Azure CDN 統合によって得られる優れた機能です。加えて、レンダリングされたバンドルには自動生成されたバージョン文字列が既に含まれます。そこで、この機能を複製することで、NuGet を介して jQuery バージョンを更新するたびにできる限り迅速にクライアント側にその更新が反映されるようにすることができます。

ASP.NET のバンドルおよび縮小を CDN エンドポイントと統合するには、次の手順に従います。

1. *App_Start\BundleConfig.cs* に戻り、CDN アドレスを指定する別の [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)を使用するように `bundles.Add()` メソッドを変更します。そのためには、`RegisterBundles` メソッドの定義を次のコードで置き換えます。  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // 開発、学習用に Modernizr の開発バージョンを使用。次に、運用の準備ができたら、
    // ビルド ツール (http://modernizr.com) を使って、必要なテストのみを選択。
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
{
</pre>`<yourCDNName>` は Azure CDN の名前で置き換えてください。

	このコードでは、`bundles.UseCdn = true` を設定し、巧妙に作成された CDN URL を各バンドルに追加しています。たとえば、コードの最初のコンストラクター

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	は、次のコードと同じです。

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	このコンストラクターは、ASP.NET のバンドルおよび縮小機能に対して、ローカルにデバッグされたときに個々のスクリプト ファイルをレンダリングする一方で、指定された CDN アドレスを使用して対象のスクリプトにアクセスすることを指定しています。ここで、この巧妙に作成された CDN URL の 2 つの重要な特性に注意してください。
	
	-	この CDN URL のオリジンは、実際には Web アプリケーション内のスクリプト バンドルの仮想ディレクトリである `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>` です。
	-	CDN コンストラクターを使用しているため、バンドルのための CDN スクリプト タグには、レンダリングされた URL 内の自動生成されたバージョン文字列が含まれません。Azure CDN でキャッシュ ミスを強制的に発生させるには、スクリプト バンドルを変更するたびに一意のバージョン文字列を手動で生成する必要があります。また、この一意のバージョン文字列は、バンドルがデプロイされた後の Azure CDN でのキャッシュ ヒットを最大化するために、デプロイの有効期間を通じて一定に保たれる必要があります。
	-	クエリ文字列 v=<W.X.Y.Z> は、ASP.NET プロジェクトの *Properties\AssemblyInfo.cs* から取得されます。Azure に発行するたびにアセンブリ バージョンを増分する操作を含むデプロイ ワークフローを導入できます。また、ワイルドカード文字 * を使って単にプロジェクトの *Properties\AssemblyInfo.cs* を変更して、ビルドするたびに自動的にバージョン文字列を増分するよう設定することもできます。次に例を示します。
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		デプロイの有効期間を通じて一意の文字列を生成する操作を合理化するための他の戦略も使用できます。

3. ASP.NET アプリケーションを再発行し、ホーム ページにアクセスします。
 
4. ページの HTML コードを表示します。Azure Web アプリに変更を再発行するたびに一意のバージョン文字列を付けて CDN URL がレンダリングされているのを確認できます。次に例を示します。
	<pre class="prettyprint">
...

&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. Visual Studio で、`F5` キーを押して ASP.NET アプリケーションをデバッグします。

6. ページの HTML コードを表示します。Visual Studio での一貫したデバッグ エクスペリエンスが得られるように個別にレンダリングされた各スクリプト ファイルを確認できます。
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...   
</pre>

<a name="fallback"></a>
## CDN URL のフォールバック メカニズム ##

何かの理由で Azure CDN エンドポイントに障害が発生した場合に備えて、JavaScript または Bootstrap を読み込むためのフォールバック オプションとしてオリジン Web サーバーにアクセスできるようにしておくと便利です。CDN が利用できないために Web アプリの画像が失われることは深刻な事態ですが、スクリプトやスタイルシートで提供される重要なページ機能が失われることは、さらに深刻な事態です。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) クラスには、CDN 障害に対するフォールバック メカニズムを構成するための [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) プロパティがあります。このプロパティを使用するには、次の手順に従います。

1. ASP.NET プロジェクトで、それぞれの [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)に CDN URL を追加した *App_Start\BundleConfig.cs* を開き、次の強調表示された変更を加えて、既定のバンドルにフォールバック メカニズムを追加します。  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // 開発、学習用に Modernizr の開発バージョンを使用。次に、運用の準備ができたら、
    // ビルド ツール (http://modernizr.com) を使って、必要なテストのみを選択。
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
{</pre>`CdnFallbackExpression` が null でない場合は、HTML にスクリプトが挿入されます。このスクリプトは、バンドルが正常に読み込まれたかどうかをテストして、正常に読み込まれていない場合はオリジン Web サーバーのバンドルに直接アクセスします。このプロパティは、それぞれの CDN バンドルが適切に読み込まれたかどうかをテストする JavaScript 式に設定する必要があります。各バンドルをテストするために必要な式は、コンテンツによって異なります。上記の既定のバンドルに対して、次のように定義されています。
	
	-	`window.jquery` は jquery-{version}.js に定義されています。
	-	`$.validator` は jquery.validate.js に定義されています。
	-	`window.Modernizr` は modernizer-{version}.js に定義されています。
	-	`$.fn.modal` は bootstrap.js に定義されています。
	
	`~/Cointent/css` バンドルには CdnFallbackExpression を設定していません。これは、期待される `<link>` タグではなくフォールバック CSS の `<script>` タグが挿入されるという [System.Web.Optimization のバグ](https://aspnetoptimization.codeplex.com/workitem/104)が存在するためです。
	
	ただし、[Ember Consulting Group](https://github.com/EmberConsultingGroup/StyleBundleFallback) から優れた[スタイル バンドルのフォールバック](https://github.com/EmberConsultingGroup)が提供されています。

2. CSS の対処法を使用するには、ASP.NET プロジェクトの *App_Start* フォルダーに *StyleBundleExtensions.cs* という名前の新しい .cs ファイルを作成し、その内容を [GitHub に公開されているコード](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)で置き換えます。

4. *App_Start\StyleFundleExtensions.cs* で、名前空間の名前を ASP.NET アプリケーションの名前空間 (例: **cdnwebapp**) に変更します。

3. `App_Start\BundleConfig.cs` に戻り、最後の `bundles.Add` ステートメントを、次の強調表示されたコードで置き換えます。
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
</pre>この新しいメソッドでは、同じアイデアを使用するスクリプトを HTML に挿入します。つまり、DOM を調べて CSS バンドルに定義されているクラス名、規則名、および規則値が一致するかどうかを確認し、一致しない場合はオリジン Web サーバーにフォールバックします。

4. もう一度 Azure Web アプリに発行し、ホーム ページにアクセスします。
5. ページの HTML コードを表示します。次のようなスクリプトが挿入されていることを確認できます。    
	<pre class="prettyprint">...

	&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        {
                    {
                {
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
</pre>CSS バンドルに対して挿入されたスクリプトには、次の行に `CdnFallbackExpression` プロパティのエラーの残りがまだ含まれている点に注意してください。

        }())||document.write('<script src="/Content/css"></script>');</script>

	ただし、(すぐ上の行の) || 式の最初の部分は常に true を返すため、document.write() 関数が実行されることはありません。

6. フォールバック スクリプトが動作するかどうかをテストするには、CDN エンドポイントのダッシュボードに戻り、**[エンドポイントを無効にする]** をクリックします。

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Azure Web アプリのブラウザー ウィンドウを更新します。これで、すべてのスクリプトとスタイルシートが正しく読み込まれていることを確認できます。

## 詳細情報 
- [Azure コンテンツ配信ネットワーク (CDN) の概要](../cdn-overview.md)
- [Web アプリケーションで Azure CDN からコンテンツを配信する](../cdn-serve-content-from-cdn-in-your-web-application.md)
- [クラウド サービスと Azure CDN との統合](../cdn-cloud-service-with-cdn.md)
- [ASP.NET のバンドルおよび縮小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Azure 用 CDN の使用](../cdn-how-to-use-cdn.md)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=62-->