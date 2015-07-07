<properties 
	pageTitle="タグを使用した Azure リソースの整理" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="micflan"/>


# タグを使用した Azure リソースの整理

Azure ポータルとその基になるリソース マネージャーを使うと、リソースを整理したり、ユーザーに合わせて操作方法をカスタマイズしたりできます。

Azure クラシック ポータルでは、サブスクリプションがリソースを分類およびグループ化する唯一の方法になります。Azure ポータルでは、関連するエンティティをグループ化する方法として、[リソース グループが採用されていました](./resource-group-portal.md)。この機能は、[ロールベースのアクセスを導入](./role-based-access-control-configure.md)した際に、より価値が高まりました。現在は、同じ目的で、キーと値のペアを使用してリソースをタグ付けすることにより、リソースをさらに分類し、異なるリソース グループ間、およびポータル内の異なるサブスクリプション間にわたってリソースを表示することができます。

チーム、プロジェクト、または環境に基づいてリソースをグループ化すれば、必要なときに表示したい情報だけにフォーカスすることができます。


## Azure ポータルでのタグ

ポータルでリソースやリソース グループをタグ付けするのは簡単です。[参照] ハブを使用してタグを付けるリソースまたはリソース グループに移動し、ブレードの上部にある [概要] セクションの [タグ] 部分をクリックします。

![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/resource-group-using-tags/rgblade.png)

既に適用されているタグの一覧のブレードが開きます。これが最初のタグの場合、一覧は空になっています。タグを追加するには、名前と値を指定し、Enter キーを押すだけです。いくつかのタグを追加すると、既存のタグの名前と値に基づくオートコンプリート オプションが機能します。これにより、リソース間で一貫性のある分類を適用でき、スペル ミスのような一般的なミスを回避することができます。

![名前と値のペアを持つタグ リソース](./media/resource-group-using-tags/tag-resources.png)

ここで、それぞれのタグをクリックして、同じタグを持つすべてのリソースの一覧を表示できます。これが最初のタグの場合、当然ですがその一覧はあまり興味深いものにはなりません。次に、PowerShell を使ってすべてのリソースにすばやくタグを付ける方法について説明します。


## PowerShell を使用したタグ付け

最初に、[最新の Azure PowerShell モジュール](./install-configure-powershell.md)を入手します。Azure PowerShell モジュールを初めて使用する場合は、参考として[このドキュメント](./install-configure-powershell.md)をお読みください。この記事では、既にアカウントを追加し、タグを付けるリソースを含むサブスクリプションを選択しているものと想定しています。

タグ付けは、[リソース マネージャー](http://msdn.microsoft.com/library/azure/dn790568.aspx)で使用可能なリソースおよびリソース グループに対してのみ行うことができます。そこで、次の作業として、リソース マネージャーを使用するための操作を行います。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。

    Switch-AzureMode AzureResourceManager

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには、それぞれ `Get-AzureResource` または `Get-AzureResourceGroup` を使用してリソースまたはリソース グループを取得します。それでは、リソース グループから始めましょう。

![PowerShell で Get-AzureResourceGroup を使用したタグの取得](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。リソース グループにタグを付けるには、`Set-AzureResourceGroup` を使用して、タグ名と値を指定するだけです。

![PowerShell で Get-AzureResourceGroup を使用したタグの設定](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたいすべてのタグを含む配列を使用する必要があります。特定のタグを削除するには、削除するタグが含まれない配列を保存します。

リソースの場合も手順は同じですが、`Get-AzureResource` コマンドレットと `Set-AzureResource` コマンドレットを使う点が異なります。特定のタグが付けられたリソースまたはリソース グループを取得するには、`Get-AzureResource` コマンドレットまたは `Get-AzureResourceGroup` コマンドレットに `-Tag` パラメーターを使用します。

![PowerShell で Get-AzureResource と Get-AzureResourceGroup を使用したタグ付きリソースとリソース グループの取得](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## リソース マネージャーを使用したタグ付け

ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST AP](http://msdn.microsoft.com/library/azure/dn790568.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## 分類の管理

前半の部分で、一貫性を確保しミスを回避するうえでオートコンプリート機能が有用であることに触れました。オートコンプリート機能では、サブスクリプションに設定された使用可能なタグの分類に基づいて値が設定されます。リソースまたはリソース グループにタグを追加すると、タグはサブスクリプション全体の分類に自動的に追加されます。ただし、目的の名前と値を含む分類を事前に設定して、後でリソースをタグ付けするときにそれらが使用されるようにすることもできます。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、`Get-AzureTag` コマンドレットを使用します。

![PowerShell での Get-AzureTag](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグです。これらのタグは単に無視して、変更しないでください。

分類に新しいタグを追加するには、`New-AzureTag` コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、`Remove-AzureTag` コマンドレットを使用して分類から削除します。

ポータル内のタグの分類を表示するには、[参照] ハブを使用してすべての項目を表示し、[タグ] を選択します。

![参照ハブによるタグの検索](./media/resource-group-using-tags/browse-tags.png)

最も重要なタグをスタート画面にピン留めしておくとすぐに利用できるため便利です。どうぞお楽しみください。

![スタート画面にタグをピン留めする](./media/resource-group-using-tags/pin-tags.png)

## 次のステップ
Getting Started (概要)

- [Azure リソース マネージャーの概要](./resource-group-overview.md)  
- [Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)
- [Azure リソース 管理での Azure CLI for Mac, Linux, and Windows の使用](./xplat-cli-azure-resource-manager.md)  
- [Azure ポータルを使用した Azure リソースの管理](./resource-group-portal.md)  
  
アプリケーションの作成と展開
  
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)  
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)  
- [Azure でのリソース グループのデプロイのトラブルシューティング](./resource-group-deploy-debug.md)  
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)  
- [高度なテンプレートの操作](./resource-group-advanced-template.md)  
  
アクセスの管理と監査
  
- [リソースへのアクセスの管理と監査](./resource-group-rbac.md)  
- [Azure リソース マネージャーでのサービス プリンシパルの認証](./resource-group-authenticate-service-principal.md)  
- [Azure クラシック ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=58_postMigration-->