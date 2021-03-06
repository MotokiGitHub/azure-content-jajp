<properties 
   pageTitle="Traffic Manager の監視"
   description="この記事は、Traffic Manager の監視について理解し、これを構成するために役立ちます。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Traffic Manager の監視について

Azure Traffic Manager は、クラウド サービスや Web サイトなどのエンドポイントを確実に利用できるように監視します。正常に監視を動作させるには、Traffic Manager プロファイルで指定したすべてのエンドポイントに対して、同じ方法で設定する必要があります。監視を構成した後、管理ポータルで、Traffic Manager により、エンドポイントやプロファイルの状態が表示されます。管理ポータルの Traffic Manager プロファイルの [構成] ページで、監視の設定を構成できます。次の設定を指定できます。

- **プロトコル** – HTTP または HTTPS を選択します。HTTPS 監視は、その証明書の存在のみをチェックして、SSL 証明書が有効であるかどうかを検証しないことに注意することが重要です。

- **ポート** – 要求に使用するポートを選択します。ポート– 要求に使用するポートを選択します。選択肢には、標準の HTTP ポートや HTTPS ポートがあります。

- **相対パスとファイル名**– 監視システムがアクセスを試行するパスとファイルの名前を指定します。スラッシュ「/」は、相対パスの有効なエントリであり、ファイルがルート ディレクトリ (既定値) にあることを示します。

## 正常性状態の監視について

Azure Traffic Manager は、管理ポータルで、プロファイルとエンドポイント サービスの正常性状態を表示します。プロファイルとエンドポイントの両方の [状態] 列には、最新のモニター状態が表示されます。この状態を使用すると、Traffic Manager の監視設定に従って、プロファイルの正常性を把握できます。プロファイルが正常な場合は、DNS クエリが、プロファイルのトラフィック ルーティング設定 (ラウンド ロビン、パフォーマンス、フェールオーバー) に基づいて、サービスに配信されます。Traffic Manager の監視システムが監視状態の変更を検出すると、管理ポータルで、状態エントリが更新されます。状態の変更を更新するまでに、最大 5 分かかることがあります。

### エンドポイント監視の状態

次の表に示すエンドポイント監視の状態は、エンドポイントの正常性プローブの結果と、プロファイルやエンドポイントの構成の組み合わせによって生じます。

|プロファイルの状態|エンドポイントの状態|エンドポイント監視の状態 (API およびポータル)|メモ|
|---|---|---|---|
|無効|有効|非アクティブ|無効なプロファイルは監視されません。ただし、無効なプロファイル内のエンドポイントの状態は管理できます。|
|&lt;いずれも&gt;|無効|無効|無効なプロファイルは監視されません。ただし、無効なプロファイル内のエンドポイントの状態は管理できます。|
|有効|有効|オンライン|エンドポイントは監視されており、正常です。|
|有効|有効|低下しています|エンドポイントは監視されており、異常な状態です。|
|有効|有効|エンドポイン トチェック中|エンドポイントを監視していますが、最初のプローブの結果をまだ受信していません。新しいエンドポイントをプロファイルに追加するか、エンドポイントまたはプロファイルを有効にしたばかりのときに、一時的にこの状態になります。|
|有効|有効|停止済み|基になるクラウド サービスまたは Web サイトが動作していません。|

### プロファイル モニターの状態

次の表に示すプロファイル モニターの状態は、エンドポイント モニター状態と、構成したプロファイル状態の組み合わせによって生じます。

|(構成された)プロファイルの状態|エンドポイント監視の状態|プロファイル監視の状態 (API およびポータル)|メモ|
|---|---|---|---|
|無効|&lt;いずれも&gt;または、プロファイルでエンドポイントが定義されていません。|無効|エンドポイントは監視されていません。|
|有効|少なくとも 1 つのエンドポイントの状態が「低下」です。|低下しています|これは顧客のアクションが必要であることを示しています。|
|有効|少なくとも 1 つのエンドポイントの状態が「オンライン」です。「低下」の状態にあるエンドポイントが ありません"。|オンライン|サービスはトラフィックを受け入れており、顧客の操作は必要ありません|
|有効|少なくとも 1 つのエンドポイントの状態が、「エンドポイント チェック中」です。「オンライン」または『低下」のエンドポイントはありません。|エンドポイント チェック中|切り替え状態これは、通常、プロファイルを有効にしたばかりで、エンドポイントの正常性がプローブされている場合に発生します。|
|有効|プロファイルで定義されているすべてのエンドポイントの状態が「無効」または「停止」のいずれかであるか、プロファイルにエンドポイントが定義されていません。|非アクティブ|アクティブなエンドポイントはありませんが、プロファイルは引き続き有効です。|

## 監視のしくみ

1 つのクラウド サービスを使用した監視プロセスを示すタイムラインの例を以下に示します。このシナリオを次に示します。

- クラウド サービスは使用可能であり、この Traffic Manager プロファイルだけを通じてトラフィックを受信しています。

- クラウド サービスは、使用できなくなります。

- クラウド サービスは、DNS 存続時間 (TTL) よりもはるかに長い時間にわたって使用できない状態が続きます。

- クラウド サービスが、再度使用可能になります。

- クラウド サービスは、この Traffic Manager プロファイルだけを使用して、トラフィックの受信を再開します。

![Traffic Manager の監視シーケンス](./media/traffic-manager-monitoring/IC697947.jpg)

**図 1** -監視シーケンスの例図の番号は、次の番号付きの説明に対応しています。

1. **GET** – 監視設定で指定したパスとファイルで、Traffic Manager の監視システムにより GET が実行されます。
2. **200 OK** – 監視システムは、10 秒以内に、HTTP 200 OK メッセージの応答を予期します。この応答を受信すると、クラウド サービスが使用可能であると想定されます。 

>[AZURE.NOTE]応答メッセージが 200 OK の場合にのみ、Traffic Manager は、エンドポイントがオンラインであると見なします。200 以外の応答を受信すると、Traffic Manager は、エンドポイントが使用できないと想定し、このチェックを失敗としてカウントします。失敗したチェックのトラブルシューティングについての詳細は [ Azure Traffic Managerでの機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md) を参照してください。

3. **30 秒間隔のチェック** – このチェックは 30 秒ごとに実行されます。
4. **クラウド サービスが使用不可** – クラウド サービスが、使用できなくなりました。Traffic Manager は、次のモニター チェックまで可用性を認識しません。
5. **監視対象ファイルへのアクセス試行 (4 回の試行)** – 監視システムは GET を実行していますが、10 秒以内に応答が返されません。監視対象ファイルへのアクセス試行 (4 回の試行)監視システムは GET を実行していますが、10 秒以内に応答が返されません。次に、監視システムは、30 秒間隔で、さらに 3 回試行します。つまり、監視システムでサービスが使用できなくなったことを検出するまでに、最大で約 1.5 分かかります。3 回の試行のいずれかが成功した場合、試行の回数はリセットされます。図には表示されていませんが、200 OK メッセージが GET の後 10 秒を過ぎてから返された場合でも、監視システムはこのチェックを失敗としてカウントします。
6. **機能低下として指定** – 4 回連続して失敗した後に、監視システムは、使用できないクラウド サービスを「機能低下」として指定します。 

7. **クラウド サービスへのトラフィックの減少** – 使用できないクラウド サービスに対して、引き続きトラフィックが生じることがあります。サービスが使用できないため、クライアントではエラーが発生します。クライアントとセカンダリ DNS サーバーは、使用できないクラウド サービスの IP アドレスの DNS レコードをキャッシュします。これらは、会社のドメインの DNS 名をサービスの IP アドレスに引き続き解決します。また、セカンダリ DNS サーバーは、使用できないサービスの DNS 情報を引き続き配信することがあります。クライアントとセカンダリ DNS サーバーが更新されるため、使用できないサービスの IP アドレスへのトラフィックは遅くなります。監視システムは、30 秒間隔でチェックの実行を続けます。この例では、サービスは応答せず、引き続き使用できません。
8. **クラウド サービスへのトラフィックの停止** – この時点までに、ほとんどの DNS サーバーおよびクライアントは更新され、使用できないサービスへのトラフィックは停止します。トラフィックが完全に停止するまでの最長時間は、TTL の時間によって変わります。DNS の TTL の既定値は、300 秒 (5 分) です。この値を使用して、クライアントは、5 分後にサービスの使用を停止します。監視システムは、引き続き 30 秒間隔でチェックを実行しますが、クラウド サービスは応答しません。
9. **クラウド サービスがオンラインに戻り、トラフィックを受信する** – サービスが使用可能になりますが、監視システムがチェックを実行するまで、Traffic Manager で認識されません。
10. **サービスへのトラフィックの再開** - Traffic Manager は、GET を送信し、10 秒未満で 200 OK メッセージを受け取ります。次に、Traffic Manager は、更新が要求されたときに、DNS サーバーに対するクラウド サービスの DNS 名の配信を開始します。その結果、サービスへのトラフィックの送信が再び開始されます。

## 入れ子になったプロファイルに対する子と親のエンドポイントの状態

次の表では、入れ子になったプロファイルに対する子と親のプロファイルおよび minChildEndpoints 設定に対する Traffic Manager の監視の動作について説明します。詳細については、「[Traffic Managerについて](traffic-manager-overview.md)」をご覧ください。

|子プロファイル モニターの状態|親エンドポイント監視の状態|メモ|
|---|---|---|
|無効 ユーザーによりプロファイルが無効とされているためです。|停止済み|親エンドポイントの状態は停止で、無効ではありません。無効な状態は、親プロファイルでエンドポイントを無効にしたことを示すために予約されています。|
|低下 少なくとも 1 つの子のエンドポイントが「低下」の状態です。|オンラインの状態は、子のプロファイルでのオンラインエンドポイントの数が少なくとも minChildEndpointsである場合。エンドポイント チェック中の状態は、子プロファイルでのオンラインまたはエンドポイント チェック中のエンドポイントの数が少なくともminChildEndpointsの場合。どちらでもない場合は、低下の状態となります。|minChildEndopointsの設定が高すぎるときは、トラフィックはエンドポイント チェック中の状態にあるエンドポイントにルーティングされます。親エンドポイントは常に低下の状態にセットされます。|
|オンライン。少なくとも 1 つの子がオンラインの状態で、いずれも機能低下状態ではありません。|上記と同じです。||
|エンドポイント チェック中。少なくとも 1 つがエンドポイント チェック中です。いずれもオンラインまたは機能低下状態ではありません。|上記と同じです。||
||非アクティブ。すべてのエンドポイントが無効または停止のいずれかであるか、プロファイルにエンドポイントがありません。|停止|||停止済み||

## 特定のパスとファイル名の監視を構成するには

1. プロファイルに追加する予定の各エンドポイントに、同じ名前のファイルを作成します。
2. エンドポイントごとに、Web ブラウザーを使用して、ファイルへのアクセスをテストします。URL は、特定のエンドポイントのドメイン名 (クラウド サービスまたは Web サイト)、ファイルのパス、ファイル名で構成されます。 
3. 管理ポータルの**監視の設定** の **相対パスとファイル名** フィールドで、パスとファイル名を指定します。
4. 構成の変更を完了したら、クリックして、ページの下部にある**保存** をクリックします。

## 関連項目

[Traffic Manager について](traffic-manager-overview.md)

[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-load-balancing-methods.md)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Azure Traffic Manager での機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)
 

<!---HONumber=Oct15_HO3-->