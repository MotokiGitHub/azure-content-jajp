<properties
	pageTitle="機械学習を強化するためのデータを準備するタスク | Microsoft Azure"
	description="データの前処理とクリーンアップを行って機械学習の準備を整えます。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="bradsev" />


# 機械学習を強化するためのデータを準備するタスク

## はじめに
データの前処理とクリーニングは、通常は、機械学習でデータセットを効果的に使用する前に行う必要がある重要なタスクです。未加工のデータは、多くの場合、ノイズが多く、信頼性が低く、値が欠落している可能性もあります。このようなデータを使用してモデリングを行うと、誤解を招く結果が生成されることがあります。これらのタスクは、Cortana Analytics Process (CAP) の一部です。通常、必要な前処理の検出と計画に使用されるデータセットの初期の探察の後に行われます。CAP プロセスの詳細については、「[Cortana Analytics Process](cortana-analytics-process.md)」の手順を参照してください。

前処理とクリーニングのタスクは、データ探索タスクと同様に、データの格納場所と形式に応じて、多様な環境 (SQL、Hive、Azure Machine Learning Studio など)、多様なツール、多様な言語 (R、Python など) で実行できます。CAP には反復的な性質があるので、プロセスのワークフロー内のさまざまな手順でタスクを実行できます。

この記事では、多様なデータ処理の概念と、データを Azure Machine Learning に取り込む前または後に実行できるさまざまなタスクを紹介します。

Azure Machine Learning Studio 内で実行されるデータ探索と前処理の例については、[Azure ML Studio のデータの前処理](http://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)に関するビデオを参照してください。


## なぜデータの前処理とクリーニングを行うのか?

実世界のデータはさまざまなソースやプロセスから収集されるため、不規則なデータや破損されたデータが含まれていて、データセットの品質が損なわれることがあります。よく発生するデータ品質の問題には、次のようなものがあります。

* **不完全**: データの属性や値が欠落しています。
* **ノイズ**: データに誤りがあるレコードや外れ値が含まれています。
* **矛盾**: データに競合するレコードや食い違いが含まれています。

質の良いデータは、高品質の予測モデルの前提です。"ゴミを入れればゴミが出てくる" (ナンセンスなデータからはナンセンスな結果しか出てこない) を回避し、データの品質、ひいてはモデルのパフォーマンスを向上させるには、データの正常性スクリーニングを実施してデータの問題点を早期に発見し、それに対処するための前処理とクリーニングの手順を決定する必要があります。

## よく実行されるデータの正常性スクリーニングには何があるか?

全般的なデータの品質を確認するには、次の事項をチェックします。

* **レコード**の数。
* **属性** (または**特徴**) の数。
* 属性の**データ型** (ノミナル、順序、連続)。
* **欠落値**の数。
* データの**形式の一貫性**。 
	* データが TSV または CSV の場合は、列の区切り記号と行の区切り記号によって常に正しく列と行が分離されていることを確認します。 
	* データが HTML または XML 形式の場合は、それぞれの標準に基づいてデータが整形式かどうかを確認します。 
	* また、部分的に構造化されたデータやまったく構造化されていないデータから構造化された情報を抽出するためにも解析が必要になります。
* **一貫性のないデータ レコード**。値の範囲が許容されていることを確認します。たとえば、学生の GPA (成績) を格納しているデータであれば、GPA が、たとえば 0 ～ 4 の範囲で指定されていることを確認します。

データに関する問題が見つかったら、多くの場合、次のような**処理ステップ**が必要になります。欠落値のクリーニング、データの正規化、データの離散化、データの配置に影響する可能性がある埋め込み文字を削除/置換するテキスト処理、共通のフィールドにデータ型が混在している場合の処理、その他。

**Azure Machine Learning は、形式の整った表形式のデータを取り込みます**。データが既に表形式である場合は、データの前処理を ML Studio 内で Azure Machine Learning を使用して直接実行できます。データが表形式でない場合は (たとえば、XML)、データを表形式に変換するための解析が必要になることがあります。

## データの前処理の主要なタスクにはどのようなものがあるか?

* **データのクリーニング**: 欠落値を入力し、ノイズ データや外れ値を検出して削除します。
* **データの変換**:データを正規化して、次元やノイズを少なくします。
* **データの削減**: データのレコードまたは属性をサンプリングして、データを処理しやすくします。
* **データの離散化**: 連続値の属性をカテゴリ別の属性に変換して、機械学習の特定の方式で使いやすくします。
* **テキストのクリーニング**: データの不整合を引き起こす可能性のある埋め込み文字を削除します。たとえば、タブ区切りのデータ ファイル内の埋め込みのタブや、レコードを中断する可能性のある改行などです。

以下のセクションで、いくつかのデータの前処理ステップについて説明します。

## 欠落値をどのように処理するか?

欠落値に対処するには、問題を扱いやすくするため、まず欠落値が発生している原因を識別することをお勧めします。欠落値を処理するための一般的な方法には、次のようなものがあります。

* **削除**: 欠落値があるレコードを削除します。
* **ダミーへの置き換え**:欠落値をダミーの値に置き換えます。カテゴリ別の値なら _不明_、数値なら 0 など。
* **平均値への置き換え**: 欠落しているデータが数値の場合は、欠落値を平均値に置き換えます。
* **頻繁項目への置き換え**: 欠落しているデータがカテゴリ別の値の場合は、欠落値を最も頻繁に発生する項目で置き換えます
* **回帰値への置き換え**: 回帰メソッドを使用して、欠落値を回帰値に置き換えます。  

## データをどのように正規化するか?

データの正規化では、指定した範囲に数値をスケーリングしなおします。よく使用されるデータの正規化方法は、次のとおりです。

* **最小値と最大値による正規化**: データを特定の範囲、たとえば 0 ～ 1の間に線形変換します。最小値が 0 に、最大値が 1 にスケーリングされます。
* **Z スコアによる正規化**: 平均と標準偏差に基づいてデータをスケーリングします。データと平均の差を標準偏差で除算します。
* **小数点スケーリング**: 属性値の小数点を移動してデータをスケーリングします。  

## データをどのように離散化するか?

データを離散化するには、連続値を名義属性または区間に変換します。たとえば、次のような方法があります。

* **幅が均等なビン分割**: 属性のすべての可能な値の範囲を同じサイズの N 個のグループに分割し、ビンに入る値にビン番号を割り当てます。
* **高さが均等なビン分割**: 属性のすべての可能な値を同じ個数ずつ N 個のグループに分割し、ビンに入る値にビン番号を割り当てます。  

## データをどのように縮小するか? 

データを処理しやすくするためにデータのサイズを縮小するには、さまざまな方法があります。データのサイズと領域に応じて、次の方法を適用できます。

* **レコードのサンプリング**: データ レコードをサンプリングし、代表的なサブセットのみをデータから選択します。
* **属性のサンプリング**: 最も重要な属性のサブセットのみをデータから選択します。  
* **集計**: データをグループに分割し、各グループの数値を保存します。たとえば、レストラン チェーンの過去 20 年間の毎日の売上の数値を毎月の売上に集計することにより、データのサイズを小さくします。  

## テキスト データをどのようにクリーニングするか?

**表形式データ内のテキスト フィールド**には、列の配置やレコードの境界に影響を与える文字が含まれていることがあります。たとえば、タブ区切りファイルの項目に埋め込みタブが含まれていると、列の不整合が発生します。また、改行文字が含まれているとレコードの行が中断されます。テキストの読み取り/書き込み時にテキスト エンコードの処理を誤ると、情報の損失や読み取り不能な文字 (null など) の間違った挿入が発生し、テキスト解析に影響が出る可能性があります。テキスト フィールドが適切に配置されるようにクリーニングし、構造化されていない、または部分的に構造化されたテキスト データから構造化されたデータを抽出するには、注意深い解析と編集が必要になることがあります。

**データを探索**すると、データの初期ビューが提供されます。このステップ中にデータの問題点を明らかにすることができ、それらの問題点に対処するための処置を適用できます。その場合は、問題の原因は何か、その問題点はどのようにして発生したのかと考えながら対処することが重要です。そのように作業を進めていくことは、問題点を解決するのに必要なデータ処理手順を決定するためにも役立ちます。また、データから引き出そうとしている洞察の種類によっても、データの処理作業の優先順位が変わります。

## 参照

>*Data Mining: Concepts and Techniques*、第 3 版、Morgan Kaufmann 発行、2011 年、Jiawei Han、Micheline Kamber、Jian Pei 共著
 

<!---HONumber=Oct15_HO4-->