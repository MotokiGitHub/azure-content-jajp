<properties 
	pageTitle="BizTalk Services のダッシュボード、監視、スケール | Azure" 
	description="BizTalk Services の [管理ポータル] タブのコントロールについて説明し、パフォーマンスを監視します。[ダッシュボード]、[監視]、[スケール]、[構成]、[ハイブリッド接続] タブがあります。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ

Azure の管理ポータルを最初に開くと、自動的に **[すべてのアイテム]** タブが表示されます。**[すべてのアイテム]** タブ内の列は並べ替えることができます。BizTalk サービスを表示するには、**[すべてのアイテム]** タブで BizTalk サービスを選択するか、**[BizTalk サービス]** タブを選択してから BizTalk サービス名を選択します。

新しいウィンドウが開き、次のタブが表示されます。このトピックでは、これらのタブについて説明します。

- ![Quick Start][QuickStart]  [クイック スタート](#QuickStart)

- [Dashboard](#Dashboard)

- [監視](#Monitor)

- [スケール](#Scale)

- [構成](#Configure)

- [Hybrid Connections](#HybridConnections)


##<a name="QuickStart"></a>クイック スタート (![Quick Start][QuickStart])
BizTalk サービスのエディションによっては、ここに示したオプションの一部が表示されないこともあります。 
<table border="1">
    <tr>
        <td><strong>ツールの入手</strong></td>

        <td>BizTalk サービス SDK をダウンロードし、Visual Studio プロジェクト テンプレートを内部設置型の開発用コンピューターにインストールします。これらのテンプレートは、BizTalk サービスにデプロイされる <strong>BizTalk サービス</strong> (ブリッジ) および <strong>BizTalk サービス アーティファクト</strong> (変換) Visual Studio プロジェクトを作成します。
        <br/><br/>
作業を開始する手順については、		<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Azure BizTalk Services SDK を使用して開始する方法に関するページ</a>および <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Azure BizTalk Services SDK のインストールに関するページ</a>を参照してください。
        </td>
    </tr>

    <tr>
        <td><strong>パートナー契約の作成</strong></td>

        <td>Azure でホストされている Azure BizTalk サービス ポータルを開きます。ここで、パートナーを追加し、X12 契約、AS2 契約、および EDIFACT EDI 契約を作成します。
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk サービス ポータル上の EDI メッセージングのコンポーネントの構成に関するページ</a>で、作業を開始する手順を説明しています。
        </td>
    </tr>

<tr>
        <td><strong>BizTalk サービスの詳細</strong></td>
        <td>Azure BizTalk サービスの詳細については、<a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">学習センター</a>を参照してください。</td>
</tr>
</table>


一番下にあるタスク バーには、次のオプションがあります。

<table border="1">

<tr>
アプリケーションのデプロイの<td><strong>管理</strong></td>
<td>Azure BizTalk サービス ポータルが開きます。BizTalk サービス ポータルは、パートナーの追加や X12 契約、AS2 契約および EDIFACT 契約の作成などの EDI 構成を行うためのスタート地点です。
<br/><br/>
これは、<strong>[クイック スタート]</strong> タブの <strong>[パートナー契約の作成]</strong> と同じです。
<br/><br/>
BizTalk サービス ポータルに関する詳細情報については、<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk サービス ポータル上の EDI メッセージングのコンポーネントの構成に関するページ</a>を参照してください。</td>
</tr>

<tr>
Access Control 名前空間の<td><strong>接続情報</strong></td>
<td>[接続情報] を選択すると、[Access Control 名前空間]、[既定の発行者]、および [既定のキー] が表示されます。この値はコピーできます。
<br/><br/>
Access Control 管理ポータルを開くこともできます。この Access Control 管理ポータルは、左のナビゲーション ウィンドウにある <strong>[Active Directory]</strong> オプションを使用するのと同じです。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 名前空間の管理に関するページ</a>では、Access Control 管理ポータルについて詳細に説明されています。</td>
</tr>

<tr>
ストレージ アカウントの<td><strong>キーの同期</strong></td>
<td>ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。これらの暗号キーは、ストレージ アカウントへのアクセスを制御します。BizTalk サービスは自動的にプライマリ キーを使用します。<strong>キーの同期</strong>により、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キー間を切り替えることができます。
<br/><br/>
たとえば、BizTalk サービスでストレージ アカウント用に新しいプライマリ キーを使用することができます。そのためには、次の手順に従います。
<br/><br/>
<ol>
<li>BizTalk サービスを選択し、<strong>[キーの同期]</strong> を選択します。セカンダリ キーを選択します。これを行うと、BizTalk サービスはセカンダリ キーの使用を開始します。</li>
<li>Azure の管理ポータルで、ストレージ アカウントを選択してプライマリ キーを再生成します。BizTalk サービスはセカンダリ キーを使用していることに注意してください。</li>
<li>BizTalk サービスを選択し、<strong>[キーの同期]</strong> を選択します。プライマリ キーを選択します。これは、再生成した新しいプライマリ キーです。</li>
<li>Azure 管理ポータルで、ストレージ アカウントを選択してセカンダリ キーを再生成します。</li>
</ol>
<br/>
このプロセスを "キーのロールオーバー" といいます。その目的は、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キーの間で切り替えを行うことです。</td>
</tr>

<tr>
アプリケーションの<td><strong>削除</strong></td>
<td>[削除] を選択すると、この BizTalk サービスと、この BizTalk サービスにデプロイされたすべてのアイテムが削除されます。</td>
</tr>
</table>


##<a name="Dashboard"></a>Dashboard
BizTalk サービスのエディションによっては、ここに示したオプションの一部が表示されないこともあります。 

BizTalk サービス名を選択すると、[ダッシュボード] タブが表示されます。ダッシュボードには、次の情報が表示されます。

##### 使用状況の概要: 使用されているハイブリッド接続の数が表示されます。
データ使用量 (GB 単位) も表示されます。 

##### メトリック グラフ: 所定のパフォーマンス メトリックの一覧が表示されます。
これらのメトリックは、BizTalk サービスの正常性状態に関するリアルタイムの値を提供します。**[相対]** と **[絶対]** のどちらの値を表示するかを指定することや、グラフに表示するメトリックの時間範囲を **[間隔]** で指定することもできます。 

これらのパフォーマンス メトリックについては、このトピックの「[使用可能なメトリック](#Metrics)」を参照してください。


#####概要: BizTalk サービスのプロパティが一覧表示されます。

<table border="1">

<tr>
<td><strong>トラッキング データベース資格情報の更新</strong></td>
<td>トラッキング データベースにログインするためのユーザー名とパスワードを変更します。</td>
</tr>
<tr>
<td><strong>SSL 証明書の更新</strong></td>
<td>BizTalk サービスで使用される SSL 証明書を別のものに変更できます。<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk サービスを作成する</a>ときに自己署名 SSL 証明書が自動的に作成されます。</td>
</tr>
<tr>
<td><strong>証明書のダウンロード</strong></td>
<td>BizTalk サービスで使用される SSL 証明書をローカル コンピューターにダウンロードできます。</td>
</tr>
<tr>
<td><strong>ステータス</strong></td>
<td>BizTalk サービスの現在の状態が表示されます。「<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk サービス: サービスの状態のチャート</a>」を参照してください。 </td>
</tr>
<tr>
<td><strong>サービス URL</strong></td>
<td>この BizTalk サービスの URL です。これは、BizTalk サービスを作成するときに入力された<strong>ドメイン URL</strong> と同じです。</td>
</tr>
<tr>
<td><strong>パブリック仮想 IP (VIP) アドレス</strong></td>
<td>この BizTalk サービスに割り当てられた IP アドレスです。これはすべての入力エンドポイントで使用され、送信トラフィックのソース アドレスとなります。この IP アドレスは、プロビジョニングされる限り BizTalk サービスに属します。BizTalk サービスを削除すると、IP アドレスは別の BizTalk サービスに割り当てられます。</td>
</tr>
<tr>
<td><strong>ACS 名前空間</strong></td>
<td>BizTalk サービスで認証を行います。</td>
</tr>
<tr>
<td><strong>エディション</strong></td>
<td>BizTalk サービスの作成時に入力されたエディションが表示されます。</td>
</tr>
<tr>
<td><strong>場所</strong></td>
<td>BizTalk サービスをホストするリージョンが表示されます。</td>
</tr>
<tr>
<td><strong>作成日時</strong></td>
<td>BizTalk サービスが作成された日時が表示されます。</td>
</tr>
<tr>
<td><strong>トラッキング データベース</strong></td>
<td>BizTalk サービスで使用される追跡テーブルを格納する Azure SQL データベース名です。 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要件の説明の項</a>  に、トラッキング データベースの詳しい説明があります。</td>
</tr>
<tr>
<td><strong>ストレージの監視/アーカイブ</strong></td>
<td>BizTalk サービスの監視出力を格納する Azure ストレージ アカウント名です。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要件の説明の項</a>に、ストレージ アカウントの詳しい説明があります。</td>
</tr>
<tr>
<td><strong>サブスクリプション名</strong></td>
<td>この BizTalk サービスをホストしているサブスクリプションの名前が表示されます。サブスクリプションは、Azure の管理ポータルへのアクセスを管理します。</td>
</tr>
<tr>
<td><strong>サブスクリプション ID</strong></td>
<td>サブスクリプションが作成されると、サブスクリプション ID は自動的に生成されます。REST API を使用するときは、サブスクリプション ID の入力が必要になる場合があります。</td>
</tr>
</table>

「[Create a BizTalk Service using Azure management portal (Azure の管理ポータルを使用した BizTalk サービスの作成)](http://go.microsoft.com/fwlink/p/?LinkID=302280)」に、BizTalk サービスの作成手順が記載されています。


#####タスク バーの [管理]、[接続情報]、[キーの同期]、[削除]:

<table border="1">

<tr>
アプリケーションのデプロイの<td><strong>管理</strong></td>
<td>Azure BizTalk サービス ポータルが開きます。BizTalk サービス ポータルは、パートナーの追加や X12 契約、AS2 契約および EDIFACT 契約の作成などの EDI 構成を行うためのスタート地点です。
<br/><br/>
これは、<strong>[クイック スタート]</strong> タブの <strong>[パートナー契約の作成]</strong> と同じです。
<br/><br/>
BizTalk サービス ポータルに関する詳細情報については、<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk サービス ポータル上の EDI メッセージングのコンポーネントの構成に関するページ</a>を参照してください。</td>
</tr>
<tr>
Access Control 名前空間の<td><strong>接続情報</strong></td>
<td>[Access Control 名前空間]、[既定の発行者]、[既定のキー] が表示されます。これらの値はコピー可能です。
<br/><br/>
Access Control 管理ポータルを開くこともできます。この Access Control 管理ポータルは、左のナビゲーション ウィンドウにある [Active Directory] オプションを使用するのと同じです。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 名前空間の管理に関するページ</a>では、Access Control 管理ポータルについて詳細に説明されています。</td>
</tr>
<tr>
ストレージ アカウントの<td><strong>キーの同期</strong></td>
<td>ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。これらの暗号キーは、ストレージ アカウントへのアクセスを制御します。BizTalk サービスは自動的にプライマリ キーを使用します。<strong>キーの同期</strong>により、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キー間を切り替えることができます。
<br/><br/>
たとえば、BizTalk サービスでストレージ アカウント用に新しいプライマリ キーを使用することができます。そのためには、次の手順に従います。
<br/><br/>
<ol>
<li>BizTalk サービスを選択し、<strong>[キーの同期]</strong> を選択します。セカンダリ キーを選択します。これを行うと、BizTalk サービスはセカンダリ キーの使用を開始します。</li>
<li>Azure の管理ポータルで、ストレージ アカウントを選択してプライマリ キーを再生成します。BizTalk サービスはセカンダリ キーを使用していることに注意してください。</li>
<li>BizTalk サービスを選択し、<strong>[キーの同期]</strong> を選択します。プライマリ キーを選択します。これは、再生成した新しいプライマリ キーです。</li>
<li>Azure 管理ポータルで、ストレージ アカウントを選択してセカンダリ キーを再生成します。</li>
</ol>
<br/>
このプロセスを "キーのロールオーバー" といいます。その目的は、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キーの間で切り替えを行うことです。</td>
</tr>

<tr>
アプリケーションの<td><strong>削除</strong></td>
<td>この BizTalk サービスと、この BizTalk サービスにデプロイされたすべてのアイテムが削除されます。</td>
</tr>
</table>


##<a name="Monitor"></a>監視
無料エディションには適用されません。

BizTalk サービス名を選択すると、[監視] タブが選択可能になり、次の情報が表示されます。

##### メトリック グラフ: 選択されたパフォーマンス メトリックが表示されます。
これらのメトリックは、BizTalk サービスの正常性状態に関するリアルタイムの値を提供します。表示されるパフォーマンス メトリックを選択します。最大で 6 つのパフォーマンス メトリックを同時に表示できます。 

**[相対]** と **[絶対]** のどちらの値を表示するかを指定することや、表示するメトリックの時間範囲を **[間隔]** で指定することもできます。 

##### グラフ内のメトリックを削除または表示するには:
1. **[監視]** タブを選択します。
2. タスク バーで **[メトリックの追加]** を選択します。
<br/>
![Select Add Metrics][AddMetrics]
3. 表示したいパフォーマンス メトリックのチェック ボックスをオンにします。
4. チェックマークを選択して、**[監視]** タブに戻ります。
5. グラフにメトリック値を表示するメトリックの横にある円を選択します。
<br/>
たとえば、**[CPU 使用率]** メトリックが淡色表示されているとき、グラフにその出力は表示されません。
<br/>
![CPU Usage metric is grayed out][GrayedMetric]
<br/>
淡色表示された円をクリックすると、**[CPU 使用率]** メトリックが有効になり、グラフ内で表示されます。
<br/>
![CPU Usage metric is enabled][EnabledMetric]

6. グラフとリストからメトリックを削除するには、タスク バーの **[メトリックの削除]** を選択します。メトリックを再びリストに追加するには、タスク バーの **[メトリックの追加]** を選択し、そのメトリックのチェック ボックスをオンにしてから、チェックマークを選択して **[監視]** タブに戻ります。淡色表示されている円を選択すると、そのメトリックが有効な状態になります。

##<a name="Metrics"></a>使用可能なメトリック
次のパフォーマンス カウンター/メトリックを使用できます。

<table border="1">

<tr>
<td><strong>ラウンドトリップ遅延</strong></td>
<td>メッセージ 1 件の処理にかかる時間 (そのメッセージが受信された時点から、BizTalk サービスによる処理が完了するまでの時間) の、すべてのブリッジの平均値がミリ秒 (ms) 単位で表示されます。正常に処理されたメッセージのみがカウントされます。<br/><br/>
次のイベントが発生した場合は、タイムスタンプが作成されます。
<ul>
<li>メッセージがゲートウェイに入る</li>
<li>メッセージが送信先にルーティングされる</li>
<li>送信先の応答が受信される</li>
<li>送信先の確認応答がゲートウェイに送信される</li>
</ul>
<br/>
このメトリックは、次の計算結果を表示します。
<br/><br/>
[ゲートウェイに送信された送信先の確認応答] - [ゲートウェイに入ったメッセージ]</td>
</tr>
<tr>
<td><strong>ソースのエラー</strong></td>
<td>BizTalk サービスがソース エンドポイントからメッセージを取り出すときにエラーとなったメッセージの合計数が表示されます。</td>
</tr>
<tr>
<td><strong>CPU 使用率</strong></td>
<td>すべてのロール インスタンスの平均処理時間の割合を表示します。</td>
</tr>
<tr>
<td><strong>処理の遅延時間</strong></td>
<td>メッセージ 1 件の処理にかかる時間 (BizTalk サービスによる処理が完了するまでの時間から送信先で費やした時間を差し引いた値) の、すべてのブリッジの平均値がミリ秒 (ms) 単位で表示されます。正常に処理されたメッセージのみがカウントされます。<br/><br/>
次の各イベントが発生した場合は、タイムスタンプが作成されます。

<ul>
<li>メッセージがゲートウェイに入る</li>
<li>メッセージが送信先にルーティングされる</li>
<li>送信先の応答が受信される</li>
<li>送信先の確認応答がゲートウェイに送信される</li>
</ul>
<br/>このメトリックは、次の計算結果を表示します。<br/><br/>
[ゲートウェイに送信された送信先確認応答] - [ゲートウェイに入ったメッセージ] - [受信された送信先の応答] + [送信先にルーティングされたメッセージ]</td>
</tr>
<tr>
<td><strong>プロセス内のエラー</strong></td>
<td>BizTalk サービスによる処理が完了する前にエラーとなったメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。</td>
</tr>
<tr>
<td><strong>送信されたメッセージ</strong></td>
<td>BizTalk サービスによって送信されたメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。このメトリックは、パイプラインから送信されたメッセージがルートの送信先に到達したときに 1 増えます。このメトリックは、メッセージが正常に処理されたことを示すものではありません。<br/><br/>
要求 - 応答シナリオでは、ルート送信先からパイプラインに受信確認が返送されたときにこのメトリックに 1 が加算されます。</td>
</tr>
<tr>
<td><strong>受信したメッセージ</strong></td>
<td>BizTalk サービスによって受信されたメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。このメトリックは、パイプラインによって新しいメッセージが受信されたときに 1 増えます。</td>
</tr>
<tr>
<td><strong>処理中のメッセージ</strong></td>
<td>BizTalk サービスによる処理中のメッセージの合計数を所定の時間範囲内で集計したものが表示されます。</td>
</tr>
<tr>
<td><strong>処理されたメッセージ</strong></td>
<td>BizTalk サービスによる処理が正常に完了したメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。このメトリックは、パイプラインによってメッセージが正常に受信され、送信先に正常にルーティングされたときに 1 増えます。</td>
</tr>
</table>


##<a name="Scale"></a>スケール
[スケール] タブで、BizTalk サービスで使用されるユニット数を追加または削減できます。既定では、1 つのユニットが構成されています。ユニットを追加して BizTalk サービスをスケールできます。スケールを増やすと、スループットが増えます。また、デプロイされるブリッジ、契約、LOB 接続、処理能力などのリソース量も増えます。たとえば、ユニットを 1 から 2 に増やすとします。この場合、2 倍のブリッジ数をデプロイでき、契約数、LOB 接続数、処理能力をそれぞれ 2 倍にできます。

一部の BizTalk エディションではスケール オプションが提供されません。この場合、許容されるユニットは 1 つです。お使いのエディションでスケールできるユニット数については、「[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)」を参照してください。

ユニット数を増やすと、料金に影響する可能性があります。ユニットを増やした場合は、**[保存]** を選択したときに、料金に影響があるかどうかを示すメッセージが表示されます。[OK] をクリックして続行します。ユニット数を増やすと、BizTalk サービスのステータスがアクティブから更新中に代わります。更新中のステータスで、BizTalk サービスの実行が続行されます。

「[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)」に、"ユニット" の定義の説明があります。


##<a name="Configure"></a>構成
ハイブリッド接続には適用されません。

[バックアップ ステータス] を [なし] または [自動] に設定します。[なし] に設定すると、バックアップが自動的には作成されなくなります。[自動] に設定するときは、管理者がバックアップの場所、バックアップの頻度、バックアップ ファイルの保持期間を構成します。 

「[BizTalk サービス: バックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)」に、詳しい説明があります。 


##<a name="HybridConnections"></a>Hybrid Connections
"ハイブリッド接続" を使用すると、Azure のアプリケーション (たとえば Websites や Mobile Services) と、静的 TCP ポートを使用する内部設置型リソース (たとえば SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービス) とを接続できます。ハイブリッド接続の管理は、BizTalk Services で、Azure の管理ポータルを使用して行います。

Azure Websites でハイブリッド接続を作成するには、「[Connect an Azure website to an on-premises resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)](http://go.microsoft.com/fwlink/p/?LinkId=397538)」を参照してください。

Azure Mobile Services でハイブリッド接続を使用するには、[Azure Mobile Services とハイブリッド接続に関するページ](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)を参照してください。

Azure BizTalk Services でハイブリッド接続を作成または管理するには、「[Hybrid Connections Overview (ハイブリッド接続の概要)](http://go.microsoft.com/fwlink/p/?LinkID=397274)」を参照してください。



## 次へ
ここでは、各種のタブについて学びました。Azure BizTalk サービスの機能についてさらに学習できます。

- [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [BizTalk サービス: バックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>

## 関連項目
- [Hybrid Connections](http://go.microsoft.com/fwlink/p/?LinkID=397274)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: BizTalk サービスの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

<!--HONumber=46--> 
 