<properties 
	pageTitle="App Service 環境のネットワーク アーキテクチャの概要" 
	description="App Service 環境のネットワーク トポロジのアーキテクチャの概要" 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="stefsch"/>

# App Service 環境のネットワーク アーキテクチャの概要

## はじめに ##
App Service 環境は、常に[仮想ネットワーク][virtualnetwork]のサブネット内に作成され、App Service 環境で実行されるアプリは、同じ仮想ネットワーク トポロジ内に配置されたプライベート エンドポイントと通信できます。顧客が仮想ネットワーク インフラストラクチャの一部をロックダウンする場合があるため、App Service 環境で発生するネットワーク通信フローの種類を理解しておくことが重要です。

## 一般的なネットワーク フロー ##
 
App Service 環境は、常にパブリック仮想 IP アドレス (VIP) を持っています。すべての着信トラフィックは、そのパブリック VIP に到着します。トラフィックには、アプリの HTTP トラフィック、HTTPS トラフィック、その他の FTP のトラフィック、リモート デバッグ機能、および Azure 管理操作が含まれます。パブリック VIP で使用できるポート (必須およびオプションの両方) の完全な一覧については、[着信トラフィックの制御][controllinginboundtraffic]に関する記事の App Service 環境を参照してください。

次の図は、さまざまな着信および発信ネットワーク フローの概要を示しています。

![一般的なネットワーク フロー][GeneralNetworkFlows]

App Service 環境は、さまざまな顧客のプライベート エンドポイントと通信できます。たとえば、App Service 環境で実行されるアプリは、同じ仮想ネットワーク トポロジ内の IaaS 仮想マシンで実行されているデータベース サーバーに接続できます。

App Service 環境は、App Service 環境の管理と運用を行うために必要な SQL DB と Azure Storage リソースとも通信します。Azure Storage 環境が通信する SQL と Storage リソースの一部は、App Service 環境と同じリージョン内に配置されますが、それ以外のリソースは、リモート Azure リージョンに配置されます。その結果、App Service 環境が正常に機能するためには、インターネットへの発信接続が常に必要です。

App Service 環境はサブネットにデプロイされるため、ネットワーク セキュリティ グループを使用してサブネットへの着信トラフィックを制御できます。App Service 環境への着信トラフィックを制御する方法の詳細については、次の[記事][controllinginboundtraffic]を参照してください。

App Service 環境からの発信インターネット接続を許可する方法の詳細については、[ExpressRoute][ExpressRoute]の操作に関する記事を参照してください。記事で説明されている方法は、サイト間接続を操作する場合と強制トンネリングを使用する場合にも適用されます。

## 発信ネットワーク アドレス ##
App Service 環境で発信呼び出しを行うと、IP アドレスが常に発信呼び出しに関連付けられます。使用される IP アドレスは、呼び出し先のエンドポイントが仮想ネットワーク トポロジの内部にあるか外部にあるかによって異なります。

呼び出し先のエンドポイントが仮想ネットワーク トポロジの**外部**にある場合、使用される発信アドレス (発信 NAT アドレス) は、App Service 環境のパブリック VIP になります。このアドレスは、App Service 環境用のポータル ユーザー インターフェイスで確認できます (注: UX は保留中)。

このアドレスは、App Service 環境でアプリを作成した後、アプリのアドレスに対して *nslookup* を実行することでも判別できます。結果の IP アドレスは、パブリック VIP であり、App Service 環境の発信 NAT アドレスでもあります。

呼び出し先のエンドポイントが仮想ネットワーク トポロジの**内部**にある場合、呼び出し元のアプリの発信アドレスは、アプリを実行している個々のコンピューティング リソースの内部 IP アドレスになります。ただし、仮想ネットワークの内部 IP アドレスとアプリのマッピングは固定されていません。アプリは複数のコンピューティング リソースの間を移動でき、App Service 環境内で使用できるコンピューティング リソースのプールは、スケーリング操作によって変更される可能性があります。

ただし、App Service 環境は常にサブネット内にあるため、アプリを実行するコンピューティング リソースの内部 IP アドレスは、常にサブネットの CIDR 内であることが保証されます。その結果、きめ細かく調整された ACL またはネットワーク セキュリティ グループを使用して、仮想ネットワーク内の他のエンドポイントへのアクセスを保護する場合は、App Service 環境が含まれているサブネット範囲へのアクセスが許可される必要があります。

次の図に、これらの概念の詳細を示します。

![発信ネットワーク アドレス][OutboundNetworkAddresses]

上の図の説明です。

- App Service 環境のパブリック VIP は 192.23.1.2 であるため、それは "インターネット" エンドポイントを呼び出す際に使用される発信 IP アドレスです。
- App Service 環境が含まれるサブネットの CIDR 範囲は 10.0.1.0/26 です。同じ仮想ネットワーク インフラストラクチャ内の他のエンドポイントは、アプリからの呼び出しが、このアドレスの範囲内のどこかから発信されているとみなします。

## App Service 環境間の呼び出し ##
同じバーチャル ネットワーク内で、複数の App Service 環境をデプロイし、App Service 環境同士で発信呼び出しを行う場合、より複雑なシナリオとなるでしょう。このような交差したタイプの App Service 環境間呼び出しは、”インターネット” 呼び出しとしても扱われます。

さきほどの 192.23.1.2 という発信 IP アドレスを使用する App Service 環境を例に挙げます。App Service 環境で実行中のアプリケーションが、同じバーチャル ネットワークに存在する第 2 の App Service 環境で実行中のアプリケーションに発信呼び出しを行う場合、第 2 の App Service 環境で着信する発信呼び出しは 192.23.1.2(第 1 の App Service 環境のサブネット アドレス範囲ではない) から送信されているように表示されます。

異なる App Service 環境間での呼び出しは ”インターネット” 呼び出しとして扱われるものの、各 App Service 環境が同じ Azure リージョンに位置している場合は、ネットワーク トラフィックは同じリージョンの Azure ネットワーク内にとどまり、物理的にパブリック インターネット上に流出することはありません。そのため、第 2 の App Service 環境のサブネット上にネットワーク セキュリティ グループを設定することができ、192.23.1.2 からの受信呼び出しのみを許可することで、App Service 環境間での安全な通信を確保します。

## その他のリンクおよび情報 ##
App Service 環境で使用される着信ポートと、ネットワーク セキュリティ グループを使用した着信トラフィック制御の詳細については、[ここ][controllinginboundtraffic]を参照してください。

App Service 環境への発信インターネット アクセスを許可するためにユーザーが定義したルートの使用の詳細については、この[記事][ExpressRoute]を参照してください。


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png

<!---HONumber=July15_HO5-->