<properties
	pageTitle="Application Insights による利用状況分析"
	description="Application Insights による利用状況分析の概要"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/06/2015"
	ms.author="awills"/>

# Application Insights による利用状況分析

ユーザーによるアプリケーションの使用状況を把握することで、ユーザーにとって最も重要なシナリオに重点を置いて開発作業を行うことができ、実現するのが簡単またはより困難であるとユーザーが捉えている目標を理解することができます。

Application Insights を使用すると、アプリケーションの利用状況を明確に把握できるため、ユーザー エクスペリエンスの向上やビジネス目標の達成に役立ちます。

Application Insights は、スタンドアロン アプリ (iOS、Android Windows) でも、Web アプリ (.NET または J2EE でホストされた) でも機能します。

## アプリケーションの正常性と利用状況の監視

使用を開始するには、[Microsoft Azure](https://azure.com) のサブスクリプションが必要です (無料評価版を利用できます。この試用期間が終了しても、サービスの無料の層は引き続きご利用いただけます)。

[Azure ポータル](https://portal.azure.com)で、Application Insights のリソースを作成します。アプリに関する使用状況とパフォーマンス データはここに表示されます。

![Azure で、[新規]、[開発者向けサービス]、[Application Insights] の順にクリックします。](./media/app-insights-overview-usage/01-create.png)

**アプリがデバイス アプリの場合**、Application Insights SDK をプロジェクトに追加します。実際の手順は、[IDE とプラットフォーム](app-insights-platforms.md)によって異なります。Windows アプリの場合、Visual Studio でプロジェクトを右クリックし、[Application Insights の追加] を選択します。

**Web アプリの場合、**クイック スタート ブレードを開き、Web ページに追加するコード スニペットを取得します。このスニペットを使用して再発行します。

![クイック スタートを開き、[Web ページを監視するコードを取得する] をクリックします。マスター Web ページの先頭にスクリプトをコピーします。](./media/app-insights-overview-usage/02-monitor-web-page.png)

また、[ASP.NET](app-insights-start-monitoring-app-health-usage.md) または [J2EE](app-insights-java-get-started.md) サーバー コードに Application Insights を追加して、クライアントとサーバーの両方からテレメトリを組み合わせることもできます。


### プロジェクトを実行し、最初の結果を確認します。

数分間、デバッグ モードでプロジェクトを実行してから [Azure ポータル](http://portal.azure.com)に移動し、Application Insights でプロジェクト リソースを参照します。

![Azure で、[参照]、[Application Insights] の順にクリックして、目的のプロジェクトを選択します。](./media/app-insights-overview-usage/00-start.png)

アプリを発行してさらに多くのテレメトリを取得し、このアプリを使ってユーザーが何を行っているのかを確認します。


## すぐに利用できる分析

[ページ ビュー] タイルをクリックして、利用状況の詳細を表示します。

![Azure で、[参照]、[Application Insights]、プロジェクトの順に選択し、[ページ ビュー] タイルまで下へスクロールします](./media/app-insights-overview-usage/01-overview.png)

グラフ上の空白部分にマウス ポインターを移動すると、特定のポイントにおける数が表示されます。それ以外の場合は、特定の期間にわたって集計された値 (平均、合計、期間における一意のユーザーの数など) が表示されます。

Web アプリケーションでは、ユーザーは Cookie を使用してカウントされます。複数のブラウザーを使用しているユーザー、Cookie を消去しているユーザー、またはプライバシー機能を使用しているユーザーは、複数回カウントされます。

Web セッションは、非アクティブな状態が 30 分続いた後でカウントされます。電話やその他のデバイス上のセッションは、数秒にわたってアプリが中断されるとカウントされます。

任意のグラフをクリックすると、より詳細な情報が表示されます。次に例を示します。

![概要ブレードで、[セッション] グラフをクリックします](./media/app-insights-overview-usage/02-sessions.png)

(この例は Web サイトの例ですが、デバイスで実行されるアプリの場合も同じようなグラフが表示されます)。

前の週と比較して、変化していることがあるかどうかを調べることができます。

![1 つのメトリックを表示するグラフを選択し、[前の週] をオンにします](./media/app-insights-overview-usage/021-prior.png)

2 つのメトリック (たとえば、ユーザーと新しいユーザー) を比較することもできます。

![グラフを選択します。メトリックを検索し、オンまたはオフにします。](./media/app-insights-overview-usage/031-dual.png)

プロパティ (ブラウザー、オペレーティング システム、市区町村など) に基づいてデータをグループ化 (分割) できます。

![1 つのメトリックを表示するグラフを選択します。[グループ] をオンにし、プロパティを選択します](./media/app-insights-overview-usage/03-browsers.png)


## ページ利用状況

[ページ ビュー] タイルをクリックすると、最も人気のあるページの内訳が表示されます。


![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-overview-usage/05-games.png)

上の例はゲーム Web サイトの情報を示しています。From it we can instantly see:

* Usage hasn't improved in the past week.Maybe we should think about search engine optimization?
* Many fewer people see the games pages than the Home page.Why doesn't our Home page attract people to play games?
* 'Crossword' is the most popular game.このゲームに優先的に新しいアイデアと改良を加える必要があります。

## カスタムの追跡

それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。This allows the user to switch quickly between one game and another, or even have several games on one page.

But you'd still like Application Insights to log the number of times each game is opened, in exactly the same way as when they were on separate web pages.これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

	telemetryClient.trackPageView(game.Name);

## カスタム イベント

テレメトリは、アプリケーションの利用状況を把握するためにさまざまな方法で使用できます。ただし、ページ ビューとメッセージを一緒にしたくない場合があります。その場合は、カスタム イベントを使用します。カスタム イベントは、デバイスのアプリ、Web ページ、または Web サーバーから送信できます。

(JavaScript)

    telemetryClient.trackEvent("GameEnd");

(C#)

    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");

(VB)

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


最もよく使用されるカスタム イベントは、概要ブレードに示されます。

![概要ブレードで、下へスクロールし、[カスタム イベント] をクリックします。](./media/app-insights-overview-usage/04-events.png)

テーブルの先頭をクリックすると、イベントの合計数が表示されます。イベント名などのさまざまな属性によって、グラフを分割することができます。

![1 つのメトリックのみを示すグラフを選択します。[グループ] をオンにします。プロパティを選択します。使用できないプロパティもあります。](./media/app-insights-overview-usage/06-eventsSegment.png)

タイムラインには、変化を他のメトリックやイベントに関連付けることができるという便利な機能があります。For example, at times when more games are played, you'd expect to see a rise in abandoned games as well.しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## 特定のイベントのドリルダウン

一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

In this example, we coded a custom event "NoGame" that is called if the user logs out without actually starting a game.Why would a user do that? Maybe if we drill into some specific occurrences, we'll get a clue.

アプリから受信したカスタム イベントは、概要ブレードに名前別に表示されます。


![概要ブレードで、いずれかのカスタム イベントの種類をクリックします。](./media/app-insights-overview-usage/07-clickEvent.png)

目的のイベントをクリックし、最近の特定の発生箇所を選択します。


![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-overview-usage/08-searchEvents.png)

ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。


![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-overview-usage/09-relatedTelemetry.png)

例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。

このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。


![](./media/app-insights-overview-usage/10-filter.png)

ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。Maybe we should consider developing a user story that makes it easier to do that.(加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

## プロパティを使用してデータをフィルター処理、検索、および分割する
イベントには任意のタグと数値をアタッチできます。


クライアント側の JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );

C# at server

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);

サーバー側の VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

次のようにページ ビューを同じ方法でプロパティに結び付けます。

クライアント側の JavaScript

    appInsights.trackPageView("Win",
        {Game: currentGame.Name},
        {Score: currentGame.Score});

診断検索で、イベントのそれぞれの発生箇所をクリックしてプロパティを表示します。


![イベントの一覧で、イベントを開き、[...] をクリックしてその他のプロパティを表示します](./media/app-insights-overview-usage/11-details.png)

[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。


![[検索] ボックスに値を入力します](./media/app-insights-overview-usage/12-searchEvents.png)


## A | B テスト

ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。Measure the success of each, and then move to a unified version.

For this technique, you attach distinct tags to all the telemetry that is sent by each version of your app.You can do that by defining properties in the active TelemetryContext.These default properties are added to every telemetry message that the application sends - not just your custom messages, but the standard telemetry as well.

Application Insights ポータルでは、タグに基づいてデータをフィルター選択およびグループ化 (分割) して、異なるバージョンを比較できます。

サーバー側の C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");

サーバー側の VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")

個々のテレメトリは、既定値を無視することができます。

You can set up a universal initializer so that all new TelemetryClients automatically use your context.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

アプリ初期化子 (Global.asax.cs など) 内:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }


## ビルド、評価、学習

分析は、単に問題の解決に役立つだけのものではなく、開発サイクルの重要な部分を構成します。Here are some tips:

* Determine the key metric of your application.Do you want as many users as possible, or would you prefer a small set of very happy users? Do you want to maximize visits or sales?
* Plan to measure each story.When you sketch a new user story or feature, or plan to update an existing one, always think about how you will measure the success of the change.Before coding starts, ask "What effect will this have on our metrics, if it works? Should we track any new events?" And of course, when the feature is live, make sure you look at the analytics and act on the results.
* 他のメトリックを重要なメトリックに関連付ける。たとえば、"お気に入り" 機能を追加した場合は、ユーザーがお気に入りを追加する頻度を確認することになるでしょう。しかし、ユーザーがお気に入りに再びアクセスする頻度を知る方が、おそらくより興味深い結果が得られるでしょう。And, most importantly, do customers who use favorites ultimately buy more of your product?
* Canary testing.Set up a feature switch that allows you to make a new feature visible only to some users.Use Application Insights to see whether the new feature is being used in the way you envisaged.Make adjustments, then release it to a wider audience.
* Talk to your users! 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。

## 詳細情報

* [Application Insights を使用した検出、トリアージ、診断](app-insights-detect-triage-diagnose.md)
* [さまざまなプラットフォームで Application Insights を使ってみる](app-insights-detect-triage-diagnose.md)


## ビデオ

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[start]: app-insights-overview.md
 

<!---HONumber=Oct15_HO3-->