<properties
   pageTitle="データ ウェアハウスのワークロード"
   description="SQL Data Warehouse の柔軟性により、Data Warehouse ユニット (DWU) のスライディング スケールを使用してコンピューティング機能を拡大、縮小、または一時停止できます。この記事では、データ ウェアハウスのメトリックと、それらが DWU とどのように関連するのかを説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk"/>

# データ ウェアハウスのワークロード
データ ウェアハウスのワークロードは、データ ウェアハウスに対して発生するすべての操作を指します。データ ウェアハウスのワークロードには、ウェアハウスへのデータの読み込み、データ ウェアハウスでの分析の実行とレポート作成、データ ウェアハウスでのデータの管理、およびデータ ウェアハウスからのデータのエクスポートというプロセス全体が含まれます。これらのコンポーネントの深度や幅は、多くの場合データ ウェアハウスの成熟度レベルと比例します。


## データ ウェアハウスの新機能
データ ウェアハウスは、1 つ以上のデータ ソースから読み込まれたデータの集合体であり、レポート作成やデータ分析などのビジネス インテリジェンス タスクを実行するために使用されます。

データ ウェアハウスにはクエリ機能があり、これによって多数の行や広範囲に及ぶデータがスキャンされ、分析やレポート作成用に比較的容量の大きい結果が返されます。また、データ ウェアハウスには、比較的大規模なデータ読み込みが行われる一方で、小規模なトランザクション レベルの挿入、更新、削除が行われるという特徴もあります。

- データ ウェアハウスでは、多数の行または広範囲に及ぶデータをスキャンする必要のあるクエリが最適化されるようにデータが保存されている場合に、最高のパフォーマンスが実現されます。この種のスキャンは、行ではなく列によってデータが保存および検索される場合に最も適切に機能します。 

>[AZURE.NOTE]メモリ内 columnstore インデックスは列ストレージを使用し、最大 5 倍まで圧縮率を高め、レポート作成と分析クエリに関しては従来のバイナリ ツリーと比較して 10 倍のクエリ パフォーマンスが得られます。私たちは columnstore インデックスをデータ ウェアハウス内の大規模なデータを保存およびスキャンするための標準であると見なしています。

- データ ウェアハウスには、オンライン トランザクション処理 (OLTP) を最適化するシステムとは異なる要件があります。OLTP システムでは、多数の挿入、更新、および削除操作が行われます。これらの操作では、テーブル内の特定の行がシークされます。テーブルのシークは、データが行ごとに保存されている場合に最適に機能します。バイナリ ツリーまたは btree 検索と呼ばれる分割統治手法を使用して、データを保存してすばやく検索できます。


## データの読み込み
データの読み込みは、データ ウェアハウスのワークロードで大きな部分を占めます。多くの場合、企業は顧客がビジネス トランザクションを生成しているときに、1 日を通して変更を追跡する負荷の高い OLTP システムを使用しています。定期的 (多くの場合、メンテナンス期間中の夜間) に、トランザクションはデータ ウェアハウスに移動またはコピーされます。データがデータ ウェアハウスに置かれると、アナリストは分析を実行し、データに関するビジネス決定を行うことができます。

- 従来から、この読み込みプロセスは、ETL (抽出 (Extract)、変換 (Transform)、および読み込み (Load)) と呼ばれています。通常、データはデータ ウェアハウス内の他のデータと整合するように変換される必要があります。以前は、企業は専用の ETL サーバーを使用して変換を実行していました。現在はこのように高速で膨大な量の並列処理機能を使用して、最初に SQL Data Warehouse にデータを読み込んでから、変換を実行できるようになりました。このプロセスは ELT (抽出 (Extract)、読み込み (Load)、および変換 (Transform)) と呼ばれ、データ ウェアハウス ワークロードの新しい標準になりつつあります。

> [AZURE 注記] SQL Server CTP2 を使用して、OLTP テーブルでリアルタイムの分析を実行できるようになりました。これによってデータ ウェアハウスでデータを保存および分析する必要がなくなったというわけではありませんが、リアルタイムで分析を実行できるようになりました。
 
### レポート作成と分析のクエリ
レポート作成と分析のクエリは条件の数に基づいて小規模、中規模、および大規模に分類されることはよくありますが、通常は時間に基づいています。ほとんどのデータ ウェアハウスには、高速で実行されるクエリと長時間実行されるクエリが混合されたワークロードがあります。いずれの場合でも、この混合状況を判別して、その頻度 (時間単位、日単位、月末、四半期末など) を判別することが重要です。混合されたクエリ ワークロードと同時実行性が組み合わされることで、データ ウェアハウスに対する適切な容量計画を行うことができることを理解する必要があります。

- データ ウェアハウスに容量を追加するために長いリード タイムが必要な場合は特に、容量計画は混合されたクエリ ワークロードに対して複雑なタスクになる可能性があります。SQL Data Warehouse では、いつでもコンピューティング容量を増減でき、ストレージやコンピューティング容量を個別にサイズ調整できるため、容量計画を急いで立てる必要はありません。

### データ管理
近い将来にディスク スペースが不足する可能性があることを認識している場合は特に、データ管理が重要になります。通常、データ ウェアハウスではデータを意味のある範囲に分割し、それらをテーブル内のパーティションとして保存します。SQL Server ベースのすべての製品では、テーブルの内外にパーティションを移動できます。このパーティション切り替えにより、古いデータをコストがあまりかからないストレージに移動し、最新のデータをオンライン ストレージで使用できるように設定できます。

- columnstore インデックスは、パーティション分割されたテーブルをサポートします。columnstore インデックスの場合、パーティション分割されたテーブルはデータ管理およびアーカイブに使用されます。行ごとに保存されたテーブルの場合、クエリのパフォーマンスに関してパーティションはより重大な役目を果たします。  
 
- PolyBase はデータ管理に関して重要な役割を果たします。PolyBase を使用して、古いデータを Hadoop または Azure BLOB ストレージにアーカイブすることもできます。データはオンラインのままであるため、これによって多数のオプションが提供されます。Hadoop からデータを取得する方が時間がかかる可能性はありますが、取得時間がかかってもストレージ コストが安い方が重要である場合もあります。
 
### データのエクスポート
データをレポートや分析に使用できるようにする方法の 1 つとして、データ ウェアハウスから、レポートや分析の実行専用のサーバーにデータを送信する方法があります。これらのサーバーは、データ マートと呼ばれます。たとえば、レポート データを事前処理して、それをデータ ウェアハウスから世界中の多数のサーバーにエクスポートし、顧客やアナリストが幅広く使用できるようにすることができます。

- レポートを生成するために、読み取り専用のレポート サーバーに毎日のデータのスナップショットを取り込むことができます。これにより、顧客により広い帯域幅を提供しながら、データ ウェアハウスに必要なコンピューティング リソースを削減することができます。データ マートを使用すると、セキュリティの面からデータ ウェアハウスへのアクセス権を持つユーザーの数を減らすことができます。
- 分析に関しては、データ ウェアハウスに分析キューブを作成し、データ ウェアハウスに対して分析を実行するか、データを事前処理して、将来の分析用にそれを分析サーバーにエクスポートすることができます。 

## 次のステップ
データ ウェアハウスの作成を開始するには、[開発の概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-development.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=July15_HO1-->