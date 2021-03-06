<properties
   pageTitle="Azure Marketplace 向けプランを作成するための技術以外の前提条件 | Microsoft Azure"
   description="他のユーザーが購入できるプランを作成して、Azure Marketplace にデプロイするための要件を理解します。"
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
  ms.author="hascipio; v-divte"/>

# Azure Marketplace 向けプラン作成の一般的な前提条件
プランの作成およびデプロイ プロセスに必要な、ビジネス処理中心の一般的な前提条件を理解します。

## Microsoft 販売者として登録されていることを確認します。
Microsoft で販売者アカウントを登録する方法の詳細な手順については、[Account Creation & Registration (アカウントの作成および登録)](marketplace-publishing-accounts-creation-registration.md) を参照してください。

- **既に登録されている**場合は、アカウントの所有者、または登録に使用された資格情報を確認します。
- ご自身が**発行アカウントの所有者ではない**場合は、アカウント所有者に、自分の Microsoft アカウントを [[ポータルの発行](https://publish.windowsazure.com) ] -> [ **発行元**] タブ -> [**管理者**] リンクより共同管理者として追加してもらいます。
- Azure 発行プロセスの関係者が、このアドレスへのリンクを含む電子メールを受信するようにします。電子メールは、発行プロセスを完了するために、監視および応答する必要があります。
- 1 人のユーザーのみに関連付けられたアカウントを使用するのは避けてください。そのユーザーが会社を退職した場合、SKU に関する情報へのアクセスや発行機能に影響することがあります。

> [AZURE.IMPORTANT]**無料プラン (または、個人のライセンス) のみを公開する予定であれば、会社の税金や銀行情報を入力する必要はありません。**

> プラン作成を開始するには、会社の登録を完了する必要があります。ただし、会社が販売者ダッシュ ボードで税金と銀行情報を入力している途中でも、開発者は[発行ポータル](https://publish.windowsazure.com)で仮想マシン イメージの作成を開始し、それらのイメージの認定取得、および Azure ステージング環境でのテストを実行できます。販売者アカウントの承認が必要になるのは、プランを Marketplace に発行する最後の手順のみです。

> 販売者の登録作業で問題が発生した場合は、次の手順でサポート チケットにログインしてください。 1. [サポート](http://go.microsoft.com/fwlink?LinkId=272975)に問い合わせる 2. **[Seller Dashboard registration and your account]** を選択する。 3. **[Registering for a developer account]** を選択する。 4.連絡**方法**を選択する


## Azure の「従量課金制」サブスクリプションを取得する
これは、VM イメージを作成して、それらのイメージを [Azure Marketplace](http://azure.microsoft.com/marketplace) に渡すために使用するサブスクリプションです。既存のサブスクリプションがない場合は、ここでサインアップしてください。https://account.windowsazure.com/signup?offer=ms-azr-0003p

## 「販売元」の国
> [AZURE.WARNING]Microsoft Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの「販売元」の国からのものである必要があります。この制限は、支払いおよび課税上の理由から生じます。「販売元」の国の範囲は今後拡大される予定です。完全な一覧を参照するには、[Microsoft Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)の**セクション 1b** を参照してください

## 次のステップ
次は、プランの種類ごとの技術的な前提条件です。Azure Marketplace 向けに作成するプランの種類に合わせて、記事へのリンクをクリックします。

| Virtual Machine イメージ | 開発者サービス | データ サービス | ソリューション テンプレート |
|-----|-----|-----|-----|
| [VM Technical Pre-requisites (VM の技術的な前提条件)](marketplace-publishing-vm-image-creation-prerequisites.md) | 開発者サービスの技術的な前提条件 | データ サービスの技術的な前提条件 | [Solution Template Technical Pre-requisites (ソリューション テンプレートの技術的な前提条件)](marketplace-publishing-solution-template-creation-prerequisites.md) |

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->