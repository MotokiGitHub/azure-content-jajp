<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Puppet と Azure Virtual Machines について

Puppet Enterprise は、インフラストラクチャの構築、デプロイ、管理を行う自動化ソフトウェアです。このソフトウェアを使用すると、検出、プロビジョニング、OS とアプリケーションの構成管理、オーケストレーション、レポートなどを実施して、IT インフラストラクチャのライフサイクルを管理できます。

Puppet はクライアント サーバー システムです。Puppet マスターと Puppet Enterprise エージェントは、いずれも Azure を介してインストールできます。

-   Puppet マスターは、Ubuntu サーバーにインストールされた構成済みのイメージとして利用できます。既存のサーバーに Puppet Enterprise をインストールすることもできますが、イメージを使用して開始する方法が最も簡単です。エージェントを設定するには、サーバーについての情報が必要です。
-   Puppet Enterprise エージェントは仮想マシン拡張機能として利用でき、仮想マシンの作成時にインストールできるほか、既存の仮想マシンにインストールすることも可能です。

手順については、「[Microsoft Windows and Azure (Microsoft Windows と Azure)][Microsoft Windows and Azure (Microsoft Windows と Azure)]」から、『Getting Started Guide (ファースト ステップ ガイド)』をダウンロードしてください。

## その他のリソース

[New Integrations with Microsoft Azure and Visual Studio (Microsoft Azure および Visual Studio との新しい統合)][New Integrations with Microsoft Azure and Visual Studio (Microsoft Azure および Visual Studio との新しい統合)]

[Windows Server が実行されている仮想マシンにログオンする方法][Windows Server が実行されている仮想マシンにログオンする方法]

[Linux を実行する仮想マシンにログオンする方法][Linux を実行する仮想マシンにログオンする方法]

[拡張機能の管理][拡張機能の管理]

<!--Link references-->

  [Microsoft Windows and Azure (Microsoft Windows と Azure)]: http://puppetlabs.com/solutions/microsoft
  [New Integrations with Microsoft Azure and Visual Studio (Microsoft Azure および Visual Studio との新しい統合)]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
  [Linux を実行する仮想マシンにログオンする方法]: ../virtual-machines-linux-how-to-log-on
  [拡張機能の管理]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409