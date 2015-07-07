<properties
	pageTitle="Machine Learning Studio での簡単な実験の作成 | Microsoft Azure"
	description="機械学習に初めて取り組む方のためのチュートリアルです。Azure Machine Learning Studio で線形回帰モデルをトレーニングし、テストする単純な実験を作成する方法について説明しています。"
	keywords="experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="04/21/2015"
	ms.author="garye"/>

#Machine Learning のチュートリアル: Azure Machine Learning Studio で初めての実験を作成する

このチュートリアルでは、製造仕様と技術仕様などのさまざまな変数に基づいて自動車の価格を予測する線形回帰モデルを作成します。これを行うには、Microsoft Azure Machine Learning Studio を使用して、簡単な予測分析の実験を開発し、繰り返します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Machine Learning Studio の実験は、*モデルの作成*、*モデルのトレーニング*、*モデルのスコア付けとテスト*から成る、ドラッグ アンド ドロップに対応したコンポーネントで構成されます。実験では、データを取り込んで、そのデータに対してモデルをトレーニングし、新しいデータにモデルを適用する Machine Learning Studio モジュール形式の予測モデリング手法を用います。また、データを前処理して特徴を選択し、データをトレーニング セットとテスト セットに分割することもできます。さらに、モデルの品質を評価または相互検証したりするモジュールを追加することもできます。

Machine Learning Studio を開くには、[https://studio.azureml.net/Home](https://studio.azureml.net/Home) のリンクをクリックします。Machine Learning Studio の概要の詳細については、[Microsoft Azure Machine Learning Studio ホーム](https://studio.azureml.net/)をご覧ください。

Machine Learning Studio に関する全般的な情報については、「[Machine Learning Studio とは](machine-learning-what-is-ml-studio.md)」をご覧ください。


##実験を作成する 5 つの手順

このチュートリアルでは、Machine Learning Studio で 5 つの基本的なステップに従い、モデルを作成、トレーニング、スコア付けするための実験を構築します。

- モデルの作成
	- [手順 1: データを取得する]
	- [手順 2: データを前処理する]
	- [手順 3: 特徴を定義する]
- モデルをトレーニングする
	- [手順 4: 学習アルゴリズムを選択して、適用する]
- モデルのスコア付けとテストを実行する
	- [手順 5: 新しい自動車の価格を予測する]

[手順 1: データを取得する]: #step-1-get-data
[手順 2: データを前処理する]: #step-2-preprocess-data
[手順 3: 特徴を定義する]: #step-3-define-features
[手順 4: 学習アルゴリズムを選択して、適用する]: #step-4-choose-and-apply-a-learning-algorithm
[手順 5: 新しい自動車の価格を予測する]: #step-5-predict-new-automobile-prices


## 手順 1: データを取得する

Machine Learning Studio には、多数のサンプル データセットが含まれており、多様なソースからデータをインポートできます。この例では、含まれているサンプル データセット **自動車価格データ (未加工)** を使用します。このデータセットには、製造仕様、モデル仕様、技術仕様などの情報を含む、多くの個別の自動車のエントリが含まれています。

1. Machine Learning Studio ウィンドウの下部にある **[+新規]** をクリックして新しい実験を開始し、**[実験]** を選択してから「空の実験」を選択します。キャンバスの上部にある既定の実験名を選択して、**自動車価格の予測**など、わかりやすい名前に変更します。

2. 実験キャンバスの左側には、データセットとモジュールのパレットがあります。このパレットの上部にある検索ボックスに「**自動車**」と入力し、**自動車価格データ (未加工)** というラベルが付いたデータセットを検索します。

	![Palette search][screen1a]

3. 実験キャンバスにデータセットをドラッグします。

	![Dataset][screen1]

このデータの外観を確認するには、自動車データセットの下部にある出力ポートをクリックし、**[視覚化]** を選択します。データセットの変数が列として表示され、自動車の各インスタンスが行として表示されます。最も右側の列 (列 26 の "価格") が、予想の対象となる変数です。

![Dataset visualization][screen1b]

右上隅の "**x**" をクリックして、視覚化ウィンドウを閉じます。

## 手順 2: データを前処理する

通常、データセットには、分析前にある程度の前処理が必要です。不足値のさまざまな行の列に存在に気付く場合があります。モデルがデータを正しく分析できるように、これらの不足値をクリーニングする必要があります。この例では、値が見つからない行をすべて削除します。また、見つからない値の大部分は、**正規化された損失**列にあります。したがって、モデルからこの列も一緒に除外します。

> [AZURE.TIP]見つからない値を入力データから整理することが、ほとんどのモジュールを使用するための前提条件となっています。

まず、**正規化された損失**列を削除してから、見つからないデータがある列をすべて削除します。

1. モジュールのパレットの上部にある検索ボックスに「**プロジェクト列**」と入力して、[プロジェクト列][project-columns]モジュールを検索します、次に、実験キャンバスにドラッグして、**自動車価格データ (生データ)** データセットの出力ポートに接続します。このモジュールを使用すると、モデルに含める、またはモデルから除外するデータの列を選択できます。

2. ["プロジェクト列"][project-columns] モジュールを選択し、プロパティ ウィンドウの **[列セレクターの起動]** をクリックします。

	- フィルターのドロップダウン リスト **[が次の値で始まる]** で、**[すべての列]** が選択されていることを確認します。これにより、[プロジェクト列][project-columns]では、(これから除外する列を除き) すべての列がフィルターを通過します。
	- 次の行で、**[除外]** と **[列名]** を選択し、テキスト ボックスの内側をクリックします。列の一覧が表示されます。**正規化された損失**を選ぶと、テキスト ボックスに追加されます。
	- チェック マーク (OK) ボタンをクリックして、列セレクターを閉じます。

    ![Select columns][screen3]

	**プロジェクト列**のプロパティ ウィンドウに、**正規化された損失**以外のすべての列がデータセットからフィルターされることが示されます。

    ![Project Columns properties][screen4]

    > [AZURE.TIP]モジュールをダブルクリックして、テキストを入力すると、モジュールにコメントを追加できます。これで、実験でモジュールがどのような処理をするのかがひとめでわかります。この例では、[プロジェクト列][project-columns]モジュールをダブルクリックして、「正規化された損失を除外する」というコメントを入力します。

3. [見つからないデータのクリーンアップ][clean-missing-data]モジュールを実験キャンバスにドラッグして、[プロジェクト列][project-columns]モジュールに接続します。**プロパティ** ウィンドウで、**[クリーニング モード]** の下にある **[行全体を削除]** を選択し、値が見つからない列を削除してデータを整理します。モジュールをダブルクリックして、「値が見つからない行を削除する」というコメントを入力します。

	![見つからないデータのクリーンアップ プロパティ][screen4a]

4. 実験キャンバスの下にある **[実行]** をクリックして、実験を実行します。

実験が終了したら、すべてのモジュールに緑色のチェック マークが付けられ、正常に完了したことがわかります。右上隅にも **[実行が完了しました]** というステータスが表示されます。

![First experiment run][screen5]

ここまで、実験で実行したことは、データのクリーンアップだけです。整理したデータセットを表示するには、[見つからないデータのクリーンアップ][clean-missing-data] モジュール ("整理したデータセット") の左側の出力ポートをクリックし、**[視覚化]** を選択します。**正規化された損失**列が、もう含まれていないことと、見つからない値がなくなったことが確認できます。

データが整理できたので、予測モデルで使用する特徴を指定する準備が整いました。

## 手順 3: 特徴を定義する

機械学習において*特徴*とは、関心のある項目を個別に測定できるプロパティです。この例のデータセットでは、各行が 1 つの車種を表し、各列がその車種の特徴に対応します。予測モデルの作成に役立つ特徴を見つけるには、解決する問題に関する実験と知識が必要です。一部の特徴は、他よりも対象を予測することに優れています。また、特徴によっては、たとえば市内での燃費と高速道路での燃費のように、他の特徴との強い相関がある場合があります。したがって、このような特徴はモデルに新しい情報をそれほど多く追加しないので、削除できます。

ここでは、データセット内の特徴のサブセットを使用するモデルを構築します。この手順に戻って別の特徴を選択し、もう一度実験を実行して、より優れた結果が得られるかどうかを確認できます。最初に推測で、["プロジェクト列"][project-columns] モジュールを使用して、以下の特徴 (列) を選択します。モデルをトレーニングするために、予測する *price* の値を含める必要があることに注意してください。

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. 別の[プロジェクト列][project-columns]モジュールを実験キャンバスにドラッグして、[見つからないデータのクリーンアップ][clean-missing-data]モジュールの左側の出力ポートに接続します。モジュールをダブルクリックして、「予測対象の特徴を選択する」と入力します。

2. **プロパティ** ウィンドウで、**[列セレクターの起動]** をクリックします。

3. 列セレクターで、**[が次の値で始まる]** で **[列なし]** を選択し、フィルター行で **[含む]** と **[列名]** を選択します。列名の一覧を入力します。これにより、モジュールでは、指定する列のみがフィルターされます。

	> [AZURE.TIP]実験を実行したので、データに対する列の定義が、[見つからないデータのクリーンアップ][clean-missing-data]モジュールを介して元のデータセットから渡されました。[プロジェクト列][project-columns]を[見つからないデータのクリーンアップ][clean-missing-data]に接続すると、[プロジェクト列][project-columns]モジュールによって、データ内の列の定義が認識されるようになります。**[列名]** ボックスをクリックすると、列の一覧が表示され、一覧に追加する列を複数選択できます。

4. チェック マーク (OK) ボタンをクリックします。

![Select columns][screen6]

これで、次の手順の学習アルゴリズムで使用するデータセットが作成されます。後でこの手順に戻り、別の特徴を選択して再度実行することができます。

## 手順 4: 学習アルゴリズムを選択して、適用する

データが準備できると、分析モデルの構築に必要なのは、トレーニングとテストになります。このデータを使用してモデルのトレーニングとテストを行い、予測できる価格がどれがけ近いかを確認します。

*分類*と*回帰*は、2 種類の管理された機械学習の手法です。分類は、色 (赤、青、または緑) のような定義された一連の値から予測するために使用します。回帰は、人の年齢のような連続した一連の値から予測するために使用します。

ここでは、自動車の価格を予測するため、任意の値にすることができます。よって、回帰モデルを使用します。この例では、簡単な*線形回帰*モデルをトレーニングし、次の手順でこのモデルをテストします。

1. このデータをトレーニング セットとテスト セットに分割すると、トレーニング用とテスト用の両方に使用できます。["分割"][split] モジュールを選択して実験キャンバスにドラッグし、最後の ["プロジェクト列"][project-columns] モジュールの出力に接続します。**[最初の出力データセットにおける列の割合]** を 0.75 に設定します。このようにして、データの 75% をモデルのトレーニングに使用し、25% をテスト用に保持しておきます。

	> [AZURE.TIP]**ランダム シード** パラメーターを変更して、トレーニング用とテスト用に異なるランダムなサンプルを作成できます。このパラメーターでは、擬似乱数ジェネレーターによる生成を制御できます。

2. 実験を実行します。これで、[プロジェクト列][project-columns]モジュールと[分割][split]モジュールにより、次に追加するモジュールに列定義を渡すことができます。

3. 学習アルゴリズムを選ぶには、キャンバスの左側にあるモジュールのパレットの **[Machine Learning]** カテゴリを展開してから、**[モデルを初期化]** を展開します。これにより、機械学習アルゴリズムの初期化に使用できるモジュールのカテゴリが複数表示されます。

	この実験例では、**[回帰]** カテゴリで[線形回帰][linear-regression]モジュールを選択し (パレットの検索ボックスで「線形回帰」と入力してもモジュールを見つけることができます)、実験キャンバスにドラッグします。

4. ["モデルのトレーニング"][train-model] モジュールを見つけて、実験にドラッグします。左側の入力ポートを[線形回帰][linear-regression]モジュールの出力に接続します。右側の入力ポートを[分割][split]モジュールのトレーニング データの出力 (左側のポート) に接続します。

5. モデルの[トレーニング モジュール][train-model]を選択して、**[プロパティ]** ウィンドウの **[起動列セレクター]** をクリックし、**価格**列を選択します。これが、作成しているモデルで予測する値です。

	![Select "price" column][screen7]

6. 実験を実行します。

結果として、トレーニングされた回帰モデルが作成され、新しいサンプルにスコアを付けて予測するのに使用できます。

![機械学習アルゴリズムの適用][screen8]

## 手順 5: 新しい自動車の価格を予測する

データの 75% を使用してモデルをトレーニングしたので、残りの 25% のデータにスコアを付け、モデルの機能の効果を確認します。

1. [モデルのスコア付け][score-model]モジュールを検索して実験キャンバスにドラッグし、左側の入力ポートを[モデルのトレーニング][train-model] モジュールの出力に接続します。右側の入力ポートを[分割][split]モジュールのテスト データの出力 (右側のポート) に接続します。  

	![Score Model module][screen8a]

2. 実験を実行して、[モデルのスコア付け][score-model]モジュールの出力を表示します (出力ポートをクリックして、**[視覚化]** を選択します)。出力に、予測された価格の値と、テスト データから既知の値が表示されます。

3. 最後に、結果の品質をテストするために、モデルの[評価モジュール][evaluate-model]を選択して実験キャンバスにドラッグし、左側の入力ポートを[モデルのスコア付け][score-model] モジュールの出力に接続します。(モデルの[評価モジュール][evaluate-model]は、2 つのモデルの比較に使用できるため、2 つの入力ポートがあります。)

4. 実験を実行します。

[モデルの評価][evaluate-model]モジュールの出力を表示するには、出力ポートをクリックして、**[視覚化]** を選択します。作成したモデルに対して、以下の統計値が表示されます。

- **平均絶対誤差** (MAE): 絶対誤差の平均です (*誤差*とは、予測された値と実際の値との差です)。
- **二乗平均平方根誤差** (RMSE): テスト データセットに対して実行した予測の二乗誤差平均の平方根です。
- **相対絶対誤差**: 実際の値とすべての実際の値の平均との絶対差を基準にした絶対誤差の平均です。
- **相対二乗誤差**: 実際の値とすべての実際の値の平均との二乗差を基準にした二乗誤差の平均です。
- **決定係数**: **R-2 乗値**ともいいます。どの程度モデルが高い精度でデータと適合するかを示す統計指標です。

エラーの各統計情報を小さくすることをお勧めします。小さい値は、予測が実際の値をより厳密に照合することを示します。**決定係数**では、値が 1 (1.0) に近づくほど、予測の精度が高くなります。

![Evaluation results][screen9]

最終的な実験の表示は次のようになります。

![機械学習チュートリアル: 予測モデリング手法を使った線形回帰実験を作成します。][screen10]

## 次の手順

初めての機械学習チュートリアルはこれで終わりです。実験がセットアップされたら、反復によってモデルの改善を試みることができます。たとえば、予測で使用する特徴を変更できます。または、[線形回帰][linear-regression]アルゴリズムのプロパティを変更するか、別のアルゴリズムを一緒に試すことができます。[モデルの評価][evaluate-model]モジュールを使用すれば、複数の機械学習アルゴリズムを同時に実験に追加して、(2 つ同時に) 比較することもできます。

> [AZURE.TIP]実験キャンバスの下にある **[名前を付けて保存]** ボタンを使用して、繰り返す実験をコピーできます。キャンバスの下にある **[実行履歴を表示]** をクリックすると、繰り返す実験をすべて表示できます。詳細については、「[Azure Machine Learning Studio での実験イテレーションの管理][runhistory]」をご覧ください。

[runhistory]: machine-learning-manage-experiment-iterations.md

作成したモデルが期待どおりであれば、Web サービスとして発行し、新しいデータを使用した自動車価格の予測に使用できます。詳細については、「[Azure Machine Learning Web サービスを発行する][publish]」をご覧ください。

[publish]: machine-learning-publish-a-machine-learning-web-service.md

予測モデリング手法によってモデルの作成、トレーニング、スコア付け、公開を行う方法を解説した詳しいチュートリアルについては、「[Azure Machine Learning を使用した予測ソリューションの開発][walkthrough]」を参照してください。

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=58_postMigration-->