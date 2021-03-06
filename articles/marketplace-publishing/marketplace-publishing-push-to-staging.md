<properties
   pageTitle="Preparing and testing your offer for deployment to the Azure Marketplace (プランを準備およびテストして Azure Marketplace にデプロイする) | Microsoft Azure"
   description="マーケティング コンテンツの指定、価格プランの構成、Azure Marketplace へのデプロイ前のプランのテストに関する詳細な手順を説明します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# マーケティング コンテンツを使用したプラン作成の実行
発行プロセスのこのステップでは、Azure Marketplace に特定のマーケティング コンテンツや、プランまたは SKU に関する詳細情報 (製品の説明、会社のロゴ、料金プラン、工場の詳細、プランまたは SKU をステージングにプッシュするのに必要なその他の情報など) を提供する必要があります。この情報は、Microsoft Azure ポータルでマーケティング コンテンツとして使用されます。このプロセスは、[発行ポータル][link-pubportal]で開始します。

## 手順 1.Marketplace のマーケティング コンテンツを指定する
**既定で英語が使用され、この言語のみがサポートされています**。フィールドへのすべての情報は英語で入力してください。ステージングにプッシュするまでは、すべての情報をいつでも編集することができます。

  1. 発行ポータル ([https://publish.windowsazure.com](https://publish.windowsazure.com)) に移動します。
  2. 左側のメニューで、**[マーケティング]** タブをクリックします。
  3. メイン パネルで、**[英語 (米国)]** をクリックします。

### 詳細
1. プランについて、その概要、説明などを入力します。
2.	必要な要件 (発行ポータルに記載された) を満たしたイメージを各サイズあたり 1 つ PNG 形式でアップロードします。

  ![図](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *プランのタイトル、説明、およびロゴ*

### リンク
左のバーの [リンク] タブで、お客様に役立つ情報があればそのリンクを入力します。各リンクの名前と URL を入力します。

![図](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### サンプル イメージ (省略可能)
> [AZURE.NOTE]サンプル イメージを含めるステップは、省略可能です。ステージングにプッシュするための要件を満たすために、他のマーケティング コンテンツを実行することもできます。

左側のメニューの **[サンプル イメージ]** タブで、**[新しいイメージのアップロード]** をクリックして新しいイメージをアップロードします。既存のイメージがある場合、それを置き換えるには、**[イメージの置換]** をクリックします。

![図](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### プラン
![図](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### 法的情報
[法的情報] タブで、ポリシー/利用条件へのリンクを指定します。大きい [利用条件] ボックスに利用条件を入力するか、貼り付けます。

> [AZURE.IMPORTANT]ステージングにプッシュするために、イメージを含め、すべてのフィールドが入力されている必要があります。


## 手順 2.: 価格を設定する
### 価格モデル
|価格モデル |説明 |
|---------------|------------------------------------------|
|基本| 月額固定料金 (購入時に支払い) 例: 10 ドル/月|
|消費量 (別名: 使用量、メーター) | 従量課金。プランの発行者が定義します。超過料金はシートやユーザーごとなどの方法で定義できません。これは、比例配分を行うためのユーザーまたは容量の分割の概念を採用していないためです。使用量は、時間単位でパートナーが報告します。お客様は前払いではなく、月額プランなどの月ごとの請求サイクルで支払います。 |
|無料試用版 | お客様は、期間限定で無料で試用し、その後、標準料金を支払うことを選択できます。 |
|Free レベル | プランは常時無料です |
| プランの移行 (変換またはアップグレード/ダウングレードとも呼ばれます) | ユーザーが現在のプランから別の許容されるプランに移行するという概念。パートナーが定義します。 |

**使用できる価格モデル (プランの種類別)**

> [AZURE.IMPORTANT]特定の価格モデルを使用できるかどうかは、プランの種類によって異なります。次の表を参照してください。

| | 基本のみ | 消費量のみ | 基本 + 消費量 |
|---|---|---|---|
| Virtual Machine イメージ | なし | あり | なし|
| 開発者サービス | あり | あり | あり |
| データ サービス | あり | なし | なし |

### 2\.1.VM 料金を設定する
> [AZURE.NOTE]BYOL は、仮想マシンでのみサポートされています。

1.	**[料金]** タブに、サポートされたすべての市場が表示されます。適切なものを 1 つ選択し、価格フィールドに移ります。
2.	発行ポータルで提供されるリンクに、SKU の価格を決めるのに役立つ価格情報が表示されます。
3.	SKU が BYOL の場合、"外部でライセンスされた (BYOL)" SKU の可用性のチェックボックスをオンにします。
4.	SKU が時間単位の場合、ソフトウェアの価格を入力します。価格設定なしの SKU は購入または使用できません。

  >[AZURE.NOTE]BYOL だけでなく時間あたりの SKU もある場合は、両方の前提条件が満たされていることを確認します (BYOL チェックボックスと時間あたりの価格の値)。

5.	価格ウィザードが開き、これにより、その他の国の価格設定 (指定した市場以外からの購入を許可するように選択した場合) を含む、価格設定を完了できます。
6.	一部の国は ISV レミット対象です。ISV レミット対象の国で販売を行うには、SKU に税金を課し、徴収できる必要があります。また、その国の政府に対する税金を計算および支払いできる必要があります。マイクロソフトでは、法的ガイダンスや税金に関するガイダンスを提供していません。"販売先" の国の詳細については、このドキュメントの「概要」のプランの "販売先" の国に関するセクションを参照してください。

### 2\.2.開発者サービスの価格を設定する
基本と使用量を組み合わせたプランにすることができます。ここで、"基本料金" は月額、"超過料金" は従量課金になります (詳細については、以下を参照してください)。

**例**: Contoso 開発者サービスのプラン

| プラン | 料金 | 内容 | 移行パス |
|-------|------|-------|-------|
|無料|0 ドル/月|基本的な機能|他のすべてのプランに移行可能|
|ブロンズ|10 ドル/月|基本的な機能のほか、機能 X の 1,000 のクォータ|ブロンズ プラス、シルバー、ゴールドの各プランに移行可能|
|ブロンズ プラス|無料試用期間: 0 ドル/月 + 0 ドル/メーター 01 |基本的な機能のほか、機能 X の 10,000 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。|シルバー プラスとゴールドの各プランに移行可能|
|ブロンズ プラス| 有料の期間 (別名: 無料試用版の有効期限切れ): 10 ドル/月 + 0.05 ドル/メーター 01|基本的な機能のほか、機能 X の 10,000 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。|シルバー プラスとゴールドの各プランに移行可能|
|シルバー|0\.15 ドル/メーター 01|お客様は、メーター 01 経由で従量課金で支払います (機能 X 用)|ブロンズ、ゴールドの各プランに移行可能|
|シルバー プラス|20 ドル/月 + 0.15 ドル/メーター 01 + 0.01 ドル/メーター 02|基本的な機能のほか、機能 X の 10,000 および機能 Y の 100 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。機能 Y のクォータが使用されると、お客様は メーター 02 経由の従量課金で支払うことができます。|ブロンズ プラス、ゴールドの各プランに移行可能|
|ゴールド|1000 ドル/月|機能 X の 10,000 のクォータ、機能 Y の 1,000 のクォータ、機能 Z は無制限に使用できます|Free を除くすべてのプランに移行可能|

## 手順 3.: サポート情報を入力する
ここでの情報の一部は認定ステップでも完了できます。以下のステップで示すように、情報を追加または編集することもできます。連絡先の詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。サポートURL は、エンド ユーザーに提供されます。

1.	発行ポータルの左側にある [サポート] に移動します。
2.	[エンジニアリング連絡先] に情報を入力します。
3.	[カスタマー サポート] に情報を入力します。電子メール サポートのみを指定する場合は、ダミーの電話番号を入力すると、指定した電子メールが代わりに使用されます。
4.	サポート URL を入力します。

## 手順 4.: Azure Marketplace カテゴリを選択する
**[カテゴリ]** タブに、一連の入力した選択内容が表示されます。該当するカテゴリを最大 5 つまで選択できます。

## マーケティングの表示例
以下は、発行ポータルのプラン マーケティングの詳細が [Azure Marketplace Web サイト](http://azure.microsoft.com/marketplace)や [Azure プレビュー ポータル Marketplace ギャラリー](https://ms.portal.azure.com)でどのように使用されるかの詳細なビューです。

### Azure.com marketplace Web サイトの場合
![図](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![図](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure.com Marketplace Web サイト上のプランの一覧*

![図](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure.com Marketplace Web サイト上のプランの説明の詳細*

![図](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure.com Marketplace Web サイト上のプランの説明の価格詳細*

### Azure プレビュー ポータルの Marketplace ギャラリーの場合
![図](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Azure プレビュー ポータルの Marketplace ギャラリー内のプランの一覧*

![図](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

![図](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Azure プレビュー ポータル Marketplace ギャラリー内のプランの説明の詳細*

## 次のステップ
Marketplace コンテンツが読み込まれたので、「**手順 3: プランをステージングでテストする**」で先に進みましょう。ただし、手順はプランの種類によって異なるため、以下の一覧から適切なプランの種類を選択する必要があります。

||仮想マシン イメージ |開発者サービス | データ サービス | ソリューション テンプレート |
|----|----|----|----|----|
| **手順 3.プランをステージングにプッシュする** | [VM のプランをステージングでテストする](marketplace-publishing-vm-image-test-in-staging.md) | 開発者サービスのプランをステージングでテストする | データ サービスのプランをステージングでテストする | [ソリューション テンプレートをステージングでテストする](marketplace-publishing-solution-template-test-in-staging.md) |

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=Nov15_HO3-->