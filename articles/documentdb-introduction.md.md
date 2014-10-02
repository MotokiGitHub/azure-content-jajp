<properties title="Introduction to Microsoft Azure DocumentDB" pageTitle="Introduction to Microsoft Azure DocumentDB | Azure" description="Learn about Azure DocumentDB and its value to cloud and mobile applications. Also, learn about how it manages data, and how you can use it in application development." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Microsoft Azure DocumentDB の概要

この記事では、開発者、IT プロフェッショナル、およびビジネスの意思決定者向けに、Microsoft Azure DocumentDB の概要を示します。この記事では、次の項目について説明します。

-   Azure DocumentDB の定義とクラウドやモバイル アプリケーションにもたらす価値
-   Azure DocumentDB におけるデータの管理方法
-   Azure DocumentDB を使用したデータへのアクセスとアプリケーションの開発
-   Azure DocumentDB アプリケーションを初めて構築する場合に役立つ詳細情報

## Azure DocumentDB とは

最近のアプリケーションは膨大なデータを生成し、取り込み、そしてすばやく応答を返します。そうしたアプリケーションはきわめて速いペースで進化しており、その基盤となるデータ スキーマにも同じことがいえます。アプリケーション データ モデルと非構造化データ フィードを高速に反復処理する能力を保ちつつ、データを保存、処理するためのシンプルで高速、かつ柔軟性の高いソリューションが求められます。そのような状況の中で、スキーマが不要な NoSQL データベースを選ぶ開発者が増えてきました。しかし、スキーマ フリーのデータベースの多くは複雑なクエリやトランザクション処理に対応しておらず、そのことが高度なデータ管理を困難にしています。こういった機能を、スキーマ フリーのデータを扱う際にも利用できるようにするために、マイクロソフトは Azure DocumentDB を開発しました。

Microsoft Azure DocumentDB は、最近のモバイル アプリケーションと Web アプリケーション向けに設計されたドキュメント指向の NoSQL データベース サービスで、終始高速な読み取りと書き込み、スキーマの柔軟性、そして需要に応じてデータベースを容易にスケール アップ/ダウンできる能力を備えています。DocumentDB は、SQL の一種 (方言) を使用した複雑なアドホック クエリに対応し、明確に定義された一貫性レベルをサポートしています。また、JavaScript 言語を使ったプログラミングが可能となっており、ストアド プロシージャ、トリガー、UDF に馴染みのあるプログラミング モデルを使って複数ドキュメントにわたるトランザクション処理を実現することができます。

Azure DocumentDB は、JSON ドキュメントをネイティブ サポートしているため、アプリケーション スキーマを容易に反復処理することができます。JSON と JavaScript という広く普及した技術を積極的に採用することで、アプリケーションの型システムとデータベース スキーマ間のミスマッチをなくしています。JavaScript のネイティブ サポートには、開発者がデータベース トランザクションの中でデータベース エンジン内から直接アプリケーション ロジックを効率的に実行できるメリットもあります。

Azure DocumentDB が備えている主要な機能と利点は次のとおりです。

-   **馴染みのある SQL 構文を使用したアドホック クエリ:** 多種多様な JSON ドキュメントを DocumentDB に格納し、馴染みのある SQL 構文を使用して照会できます。DocumentDB は、同時実行性の高い、ロックを用いないログ構造のインデックス作成技術を利用して、すべてのドキュメント コンテンツのインデックスを自動的に作成します。そのため、スキーマのヒント、セカンダリ インデックス、ビューを指定せずに、豊富なリアルタイム クエリが可能となっています。

-   **データベース内で JavaScript を実行:** 標準の JavaScript を使用し、ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF) としてアプリケーション ロジックを表現することができるため、アプリケーション スキーマとデータベース スキーマ間のインピーダンス ミスマッチに悩まされることなく JSON データを扱うことができます。DocumentDB は、JavaScript アプリケーション ロジックを完全なトランザクションとしてデータベース エンジン内から直接実行できるようになっています。JavaScript が深いレベルで統合されているため、INSERT、REPLACE、DELETE、SELECT の操作を分離されたトランザクションとして JavaScript プログラム内から実行することができます。

-   **一貫性レベルをチューニング可能:** 明確に定義された 4 つの一貫性レベルの中からいずれかのレベルを選択し、一貫性とパフォーマンス間のトレードオフを最適な形で行うことができます。DocumentDB では、クエリと読み取り操作に関して、Strong、Bounded-Staleness、Session、Eventual の 4 種類の一貫性レベルが利用できます。きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、待機時間の最適なトレードオフを検討することができます。

-   **完全管理:** データベースやコンピューター リソースを管理する手間がかかりません。Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、複雑なデータ層のアップグレードを手作業で行う必要はありません。すべてのデータベースは自動的にバックアップされ、局地的障害から保護されます。DocumentDB アカウントは簡単に追加し、必要に応じて容量をプロビジョニングすることができます。データベースの運用と管理に煩わされることなく、アプリケーションに専念することが可能です。

-   **スループットとストレージのスケールを柔軟に調整:** アプリケーションのニーズに応じて DocumentDB を容易にスケール アップしたりスケール ダウンしたりすることができます。SSD を使用したストレージとスループットの単位が細かく規定されており、スケール調整は、それらを予約することによって行います。アプリケーションの成長に合わせて容量単位を追加購入すれば、確実なパフォーマンスと共に、DocumentDB を臨機応変に拡張することができます。

-   **設計に込められたオープンな環境:** 既存のスキルやツールをそのまま活かすことができます。DocumentDB に対するプログラミングは、シンプルで親しみやすく、新しいツールを導入する必要がないうえ、JSON や JavaScript のカスタム拡張機能への縛りもありません。CRUD、クエリ、JavaScript 処理を含め、データベースのすべての機能には、単純な RESTful HTTP インターフェイスでアクセスすることができます。DocumentDB は、既にあるフォーマット、言語、標準を積極的に採用すると共に、それを基盤として価値の高いデータベース機能を提供しています。

Azure DocumentDB を使用すると、クエリ検索やトランザクション処理を必要とする多様なデータセットを格納することができます。たとえば、双方向の Web アプリケーションやモバイル アプリケーションのユーザー データ、アプリケーションの JSON データの保存、検索、処理に利用することが可能です。1 つのデータベースに JSON ドキュメントをいくつでも保存できることから、DocumentDB は、インターネット スケールで動作するアプリケーションに最適です。

## Azure DocumentDB のリソース

Azure DocumentDB では、明確に定義されたデータベース リソースによってデータが管理されます。これらのリソースは、高可用性を確保するためにレプリケートされ、論理 URI によって一意にアドレス指定されます。DocumentDB のすべてのリソースには、HTTP ベースのシンプルで RESTful なプログラミング モデルを適用することができます。

DocumentDB データベース アカウントは、Azure DocumentDB にアクセスできる一意の名前空間です。データベース アカウントを作成するには、事前に Azure サブスクリプション (多様な Azure サービスにアクセスするためのプラン) が必要です。

Azure DocumentDB 内のリソースはいずれも、JSON ドキュメントとしてモデル化されて保存されます。リソースはアイテム (メタデータを含んだ JSON ドキュメント) およびフィード (アイテムのコレクション) として管理されます。一連のアイテムがそれぞれのフィード内に格納されます。

以下の図は、Azure DocumentDB のリソース間の関係を示したものです。

![][]

データベース アカウントは、一連のデータベースから成ります。それぞれのデータベースには、複数のコレクションが含まれており、それぞれのコレクションに、ストアド プロシージャ、トリガー、UDF のほか、ドキュメントおよび関連する添付ファイルが含まれています。また、データベースにはユーザーが関連付けられ、それぞれのユーザーには、他のさまざまなコレクション、ストアド プロシージャ、トリガー、UDF、ドキュメント、添付ファイルにアクセスするための一連のアクセス許可が関連付けられます。データベース、ユーザー、アクセス許可、コレクションが、既知のスキーマを持ったシステム定義のリソースであるのに対し、ドキュメント、ストアド プロシージャ、トリガー、UDF、添付ファイルは、ユーザーが自由に定義できる JSON コンテンツを格納します。

## Azure DocumentDB に対する開発

Azure DocumentDB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。さらに、いくつかの主要な言語のプログラミング ライブラリも用意されています。アドレスのキャッシュ、例外管理、自動再試行などに伴う細部の処理がライブラリ側で行われるため、Azure DocumentDB の操作が多くの点で単純化されます。ライブラリは、次の言語およびプラットフォーム用が現在提供されており、その他についても準備中です。

-   [.NET][]
-   [Node.js][]
-   [JavaScript][]
-   [Python][]

Azure DocumentDB には、作成、読み取り、更新、削除という基本的な操作以外にも、JSON ドキュメントを検索するための多彩な SQL クエリ インターフェイスが備わっているほか、JavaScript のアプリケーション ロジックをサーバー側でトランザクション実行する機能がサポートされています。クエリとスクリプトの実行インターフェイスは、REST API に加え、あらゆるプラットフォーム ライブラリから利用できます。

### SQL クエリ

Azure DocumentDB では、JavaScript の型システムにマッチした SQL 言語と、豊富な階層クエリに対応した式を使用してドキュメントを照会することができます。DocumentDB のクエリ言語は、JSON ドキュメントを照会するための、シンプルでありながら強力なインターフェイスとなっています。この言語は、ANSI SQL の文法のサブセットをサポートしたうえで、深いレベルで JavaScript のオブジェクト、配列、オブジェクト生成、関数呼び出しとの統合が図られています。DocumentDB のクエリ モデルでは、スキーマやインデックスのヒントを開発者が明示的に指定する必要がありません。

カスタム アプリケーション ロジックへの対応は、ユーザー定義関数 (UDF) を Azure DocumentDB に登録して SQL クエリの中で参照するという、文法の拡張によって実現できます。これらの UDF は JavaScript プログラムで記述し、データベース内から実行します。

.NET の開発者にとっては、.NET SDK に含まれている LINQ クエリ プロバイダーが提供される点も見逃せません。

### トランザクションと JavaScript の実行

Azure DocumentDB では、アプリケーション ロジックを JavaScript だけで、名前付きのプログラムとして記述できます。これらのプログラムは、コレクションに登録され、特定のコレクション内のドキュメントに対してデータベース操作を発行できるようになっています。アプリケーションの JavaScript は、トリガー、ストアド プロシージャ、ユーザー定義関数 (UDF) のいずれかとして登録することができます。トリガーとストアド プロシージャは、ドキュメントの作成、読み取り、更新、削除を実行できます。これに対し、UDF はクエリの一部として実行され、コレクションに対する書き込みアクセス権はありません。

DocumentDB における JavaScript は、T-SQL の後継として、リレーショナル データベース システムによって裏付けられた概念に沿って実行がモデル化されています。すべての JavaScript ロジックは、スナップショット分離機能を使用し、現在参加している ACID トランザクション内で実行されます。その実行中に JavaScript で例外がスローされた場合、トランザクション全体が中止されます。

## 次のステップ

Azure DocumentDB を導入するには、次のリソースを参照してください。

-   [DocumentDB のリソース モデルと概念][]
-   [DocumentDB リソースとの対話][]
-   [How to create a DocumentDB account (DocumentDB アカウントの作成方法)][]
-   [Get started with a DocumentDB account (DocumentDB アカウントの概要)][]

  []: ./media/documentdb-introduction/intro.png
  [.NET]: http://go.microsoft.com/fwlink/?LinkID=402989
  [Node.js]: http://go.microsoft.com/fwlink/?LinkID=402990
  [JavaScript]: http://go.microsoft.com/fwlink/?LinkID=402991
  [Python]: http://go.microsoft.com/fwlink/?LinkID=402992
  [DocumentDB のリソース モデルと概念]: /documentation/articles/documentdb-resources/
  [DocumentDB リソースとの対話]: /documentation/articles/documentdb-interactions-with-resources/
  [How to create a DocumentDB account (DocumentDB アカウントの作成方法)]: /documentation/articles/documentdb-create-account/
  [Get started with a DocumentDB account (DocumentDB アカウントの概要)]: /documentation/articles/documentdb-get-started/