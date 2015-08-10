<properties
   pageTitle="Azure Data Catalog の用語集"
   description="Azure Data Catalog の用語集"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Azure Data Catalog の用語集

## カタログ

Azure Data Catalog は、データ ソースとデータ資産を登録できるクラウドベースのメタデータ リポジトリです。カタログは、データ ソースから抽出された構造メタデータとユーザーが追加した記述メタデータの一元的な保存場所です。

## データ ソース

データ ソースは、データ資産を管理するシステムまたはコンテナーです。たとえば、SQL Server データベース、Oracle データベース、SQL Server Analysis Services データベース (表形式または多次元)、SQL Server Reporting Services サーバーなどです。

## データ資産

データ資産は、カタログに登録することができるデータ ソース内のオブジェクトです。たとえば、SQL Server のテーブルとビュー、Oracle のテーブルとビュー、SQL Server Analysis Services のメジャー、次元と KPI、SQL Server Reporting Services のレポートなどです。

## データ資産の場所

カタログには、データ ソースまたはデータ資産の場所が格納されています。クライアント アプリケーションから、この場所を使用してソースに接続できます。場所の形式と詳細は、データ ソースの種類によって変わります。たとえば、SQL Server テーブルは、サーバー名、データベース名、スキーマ名、オブジェクト名という 4 つの名前で識別できます。SQL Server Reporting Services のレポートは、URL で識別できます。

## 構造メタデータ

構造メタデータは、データ ソースから抽出され、データ資産の構造を説明するメタデータです。資産の場所、オブジェクトの名前と種類、その他の種類に固有に特性などが含まれます。たとえば、テーブルとビューの構造メタデータには、オブジェクトの列の名前とデータ型が含まれます。

## 記述メタデータ

記述メタデータは、データ資産の目的または意図を説明するメタデータです。通常、記述メタデータは、カタログ ユーザーが Azure Data Catalog ポータルを使用して追加しますが、登録時にデータ ソースから抽出することもできます。Azure Data Catalog 登録ツールで、SQL Server Analysis Services と SQL Server Reporting Services の場合は Description プロパティから、SQL Server データベースの場合は [ms\_description 拡張プロパティ](https://technet.microsoft.com/library/ms190243.aspx)から記述を抽出します (これらのプロパティに値が設定されている場合)。

## プレビュー

Azure Data Catalog のプレビューは、最大 20 レコードのスナップショットです。登録時にデータ ソースから抽出し、データ資産メタデータと共にカタログに格納できます。プレビューを使用すると、データ資産の機能と目的を理解しやすくなります。言い換えると、列名とデータ型だけではなく、サンプル データも確認できるので、わかりやすくなります。テーブルとビューのプレビューのみがサポートされています。登録時にユーザーが明示的にプレビューを選択する必要があります。

## ユーザーの観点

Azure Data Catalog では、登録されているデータ資産を説明するメタデータを指定できます。各ユーザーは、データとその使用方法について独自の観点を持っています。たとえば、サーバーを担当する管理者は、サービス レベル アグリーメント (SLA) またはバックアップの詳細情報を指定し、データ スチュワードは、データがサポートするビジネス プロセスに関するドキュメントのリンクを指定し、アナリストは、他の分析に最も関連性が高く、データを検索して理解する必要があるユーザーにとって最も価値がある用語で記述を指定することができます。

このような各観点は本質的に価値があり、各ユーザーは Azure Data Catalog を使用して、自分にとって意味がある情報を指定できます。また、すべてのユーザーは、その情報を使用して、データとその用途を理解することができます。

## エキスパート

エキスパートとは、データ資産について知識がある "専門家" の観点を持つ人物と認められたユーザーです。すべてのユーザーは、自分または他のユーザーを資産のエキスパートとして追加することができます。エキスパートに登録された場合でも、Azure Data Catalog に追加の特権は付与されませんが、観点を簡単に特定できるようになります。たとえば、資産の記述メタデータを確認するときに役に立ちます。

## 所有者

所有者は、Azure Data Catalog のデータ資産を管理する追加の特権を持つユーザーです。ユーザーは、登録したデータ資産の所有権を得ることができます。また、所有者は他のユーザーを共同所有者として追加することができます。
> [AZURE.NOTE]所有権と管理は、Azure Data Catalog の Standard Edition でのみ使用できます。
 
## 登録

登録は、データ資産メタデータをデータ ソースから抽出し、Azure Data Catalog サービスにコピーする操作です。登録されたデータ資産は、注釈を付けて検索することができます。

## 関連項目

- [Azure Data Catalog とは何ですか](data-catalog-what-is-data-catalog.md) - この記事では、Azure Data Catalog サービス、もたらされる価値、およびサポートしているシナリオの概要について説明します。

- [GAzure Data Catalog の概要](data-catalog-get-started.md) - この記事は、データ ソースの検出に Azure Data Catalog を使用する方法を示す総合的なチュートリアルです。

<!---HONumber=July15_HO5-->