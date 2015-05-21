<properties 
	pageTitle="App Service 環境からバックエンド リソースへの安全な接続" 
	description="App Service 環境からバックエンド リソースに安全に接続する方法について説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# App Service 環境からバックエンド リソースへの安全な接続 #

## 概要 ##
App Service 環境は常に地域内の[仮想ネットワーク][virtualnetwork]のサブネットに作成されるため、仮想ネットワーク経由でのみ、App Service 環境から他のバックエンド リソースへの送信接続を行うことができます。

たとえば、ポート 1433 がロックされている仮想マシンのクラスターで実行されている SQL Server がある場合があります。このエンドポイントは、同じ仮想ネットワークの他のリソースからのアクセスを許可する目的のみで使用されることがあります。

別の例として、機密性の高いエンドポイントが内部設置型で実行されていて、[サイト対サイト][SiteToSite]の接続または [Azure ExpressRoute][ExpressRoute] 接続を使用して Azure に接続している場合があります。その結果、サイト対サイトまたは ExpressRoute トンネルに接続されている仮想ネットワーク内のリソースのみが内部設置型のエンドポイントにアクセスできるようになります。

これらのすべてのシナリオで、App Service 環境で実行中のアプリが、さまざまなサーバーとリソースに安全に接続できます。App Service 環境で実行されているアプリから同じ仮想ネットワーク内の \(または同じ仮想ネットワークに接続されている\) プライベート エンドポイントへの送信トラフィックは、仮想ネットワーク経由でのみ行われます。プライベート エンドポイントへの送信トラフィックがパブリック インターネット経由で送信されることはありません。


## SQL Server への接続
SQL Server の構成には一般的に、ポート 1433 でリッスンしているエンドポイントがあります。

![SQL Server エンドポイント][SqlServerEndpoint]

このエンドポイントへのトラフィックを制限する方法は 2 つあります。


- [ネットワーク アクセス制御リスト][NetworkAccessControlLists] \(ネットワーク ACL\)

- [ネットワーク セキュリティ グループ][NetworkSecurityGroups]


## ネットワーク ACL でのアクセスの制限

ポート 1433 は、ネットワーク アクセス制御リストを使用して保護できます。次の例は、仮想ネットワーク内から送信されたクライアント アドレスをホワイトリストに登録し、その他のすべてのクライアントへのアクセスをブロックします。

![ネットワーク アクセス制御リストの例][NetworkAccessControlListExample]

SQL Server と同じ仮想ネットワークの App Service 環境で実行されているすべてのアプリケーションが、SQL Server 仮想マシンの **VNet 内部** IP アドレスを使用して SQL Server インスタンスに接続できるようになります。

次の例の接続文字列は、プライベート IP アドレスを使用して SQL Server を参照します。

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

仮想マシンにはパブリック エンドポイントもありますが、ネットワーク ACL によって、パブリック IP アドレスを使用した接続の試行は拒否されます。

## ネットワーク セキュリティ グループを使用したアクセスの制限
アクセスを保護する方法にはほかに、ネットワーク セキュリティ グループを使用する方法があります。ネットワーク セキュリティ グループは、個々の仮想マシン、または仮想マシンを含むサブネットに適用できます。

まず、ネットワーク セキュリティ グループを作成する必要があります。

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

VNet 間の内部トラフィックのみにアクセスを制限すると、ネットワーク セキュリティ グループをごく単純に使用できます。ネットワーク セキュリティ グループの既定の規則では、同じ仮想ネットワーク内の他のネットワーク クライアントからのアクセスのみが許可されます。

その結果、SQL Server へのアクセスのロックは、SQL Server を実行している仮想マシンまたはその仮想マシンを含むサブネットに対する既定の規則を使用してネットワーク セキュリティ グループを適用するくらい単純になります。

次の例では、ネットワーク セキュリティ グループを、このグループを含むサブネットに適用します。

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
最終的に、VNet 内部のアクセスを許可し、外部アクセスをブロックするセキュリティの規則の組み合わせになります。

![既定のネットワーク セキュリティ グループ][DefaultNetworkSecurityRules]


## 使用の開始

App Service 環境の使用を開始するには、「[App Service 環境の概要][IntroToAppServiceEnvironment]」を参照してください。

App Service 環境への着信トラフィックを制御する方法の詳細については、[App Service 環境への着信トラフィックの制御][ControlInboundASE]に関するページを参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service][AzureAppService] に関するページを参照してください。

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
<!--HONumber=52-->