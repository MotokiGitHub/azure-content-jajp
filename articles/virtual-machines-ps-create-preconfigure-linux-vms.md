﻿<properties 
	pageTitle="Azure PowerShell を使用して Linux ベースの仮想マシンを作成および事前構成する" 
	description="Linux ベースの Azure 仮想マシンを作成および事前構成するための Azure PowerShell の使用方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="josephd"/>

#Azure PowerShell を使用して Linux ベースの仮想マシンを作成および事前構成する

以下の手順では、Azure PowerShell コマンド セットのカスタマイズ方法を示します。このコマンド セットは構成ブロック手法を使用することにより、Linux ベースの Azure 仮想マシンを作成および事前構成します。このプロセスを使用すると、新しい Linux ベースの仮想マシンにコマンド セットを迅速に作成することができるため、既存のデプロイメントを拡張することができます。また、複数のコマンド セットを作成してカスタム開発やテスト、IT プロの環境をすばやく構築することができます。

これらの手順では、空白に記入する方式に従って Azure PowerShell コマンド セットを作成します。この方法は、PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。PowerShell に慣れているユーザーは、コマンドの変数を独自の値で置き換えることができます ("$" で始まる行)。

このトピックと対になっている、Windows ベースの仮想マシンの構成については、「[Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](../virtual-machines-ps-create-preconfigure-windows-vms/)」を参照してください。

##手順 1:Azure PowerShell をインストールするには

まだ完了していない場合は、「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell/)」の手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。次に、管理者レベルの Azure PowerShell コマンド プロンプトを開きます。

##手順 2:サブスクリプションとストレージ アカウントの設定

Azure PowerShell コマンド プロンプトでこれらのコマンドを実行して Azure サブスクリプションとストレージ アカウントを設定します。引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** コマンドで出力される SubscriptionName プロパティで正しいサブスクリプション名を取得できます。**Select-AzureSubscription** コマンドの実行後、**Get-AzureStorageAccount** コマンドを実行すると、出力される Label プロパティで正しいストレージ アカウント名を取得できます。これらのコマンドをテキスト ファイルに保存して、後で使用することもできます。

##手順 3:ImageFamily の特定

次に、作成する Azure 仮想マシンに対応する特定のイメージで使用するために、ImageFamily 値を特定する必要があります。このコマンドで、利用可能な ImageFamily 値の一覧を取得できます。

	Get-AzureVMImage | select ImageFamily -Unique

Linux ベースのコンピューターで使用する ImageFamily 値の例は次のとおりです。

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

選択したテキスト エディターの最新インスタンスを開き、ImageFamily 値に置き換える以下のコマンドを新しいテキスト ファイルにコピーします。
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

##手順 4:コマンド セットの構築

下の該当するブロック セットを新しいテキスト ファイルにコピーしてから変数の値を入力し、文字 < と > を削除する手順によって残りのコマンド セットを構築します。この記事の最後の 2 つの[例](#examples) を、最終結果のアイデアとして参照してください。

この 2 つのコマンド ブロックのいずれかを選択することからコマンド セットを開始します (必須)。

方法 1:仮想マシンの名前とサイズを指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

方法 2:名前、サイズ、および可用性セット名を指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D、DS、または G シリーズの各仮想マシンの InstanceSize 値の詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。

最初の Linux ユーザー名とパスワードを指定します (必須)。強力なパスワードを選択してください。強度を確認するには、[パスワード チェッカーの強力なパスワードの使用](https://www.microsoft.com/security/pc-security/password-checker.aspx)に関するページを参照してください。

	$username="<user account name>"
	$pass="<user account password>"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

最終的なコマンド セットをファイルに保存する場合は、ファイルを安全な場所に保管して、アカウント名とパスワードが正しく保護されるようにします。

場合によっては、サブスクリプションにデプロイ済みの SSH キー ペアのセットを指定します。

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

詳細については、「[Azure 上の Linux における SSH の使用方法](../virtual-machines-linux-use-ssh-key/)」を参照してください。

場合によっては、サブスクリプションにデプロイ済みの SSH 公開キーの一覧を指定します。

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

これ以外の Linux ベースの仮想マシンの事前構成のオプションについては、「[Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)」で **Linux** パラメーター セットの構文を参照してください。

必要に応じて、次のように、"静的 DIP" と呼ばれる特定の IP アドレスを仮想マシンに割り当てます。

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

次のように実行すると、特定の IP アドレスが利用可能かどうかを調べることができます。

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

必要に応じて、次のように、Azure 仮想ネットワークの特定のサブネットに仮想マシンを割り当てます。

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

必要に応じて、次のように、単一データ ディスクを仮想マシンに追加します。 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

必要に応じて、外部トラフィックを処理するために、既存の負荷分散セットに仮想マシンを次のように追加します。

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

最後に、これらのコマンド ブロックのいずれかを選択して、仮想マシン作成プロセスを開始します (必須)。

方法 1:新しいクラウド サービスに仮想マシンを作成する。 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

次のように実行すると、Azure がある場所の一覧を取得できます。

	Get-AzureLocation | Select DisplayName

方法 2:既存のクラウド サービスに仮想マシンを作成する。 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

クラウド サービスの短い名前が、Azure の管理ポータルの [クラウド サービス] ボックスの一覧または、Azure プレビュー ポータルの [リソース グループ] ボックスの一覧に表示されます。 

方法 3:仮想マシンを既存のクラウド サービスと仮想ネットワークに作成する。

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##手順 5:コマンド セットの実行

手順 4. でテキスト エディターを使用して作成した、複数のコマンド ブロックで構成される Azure PowerShell コマンド セットを確認します。必要なすべての変数が指定され、それらの値がすべて正しいことを確認します。さらに、文字 < と > がすべて削除されていることも確認します。

クリップボードにコマンド セットをコピーしてから、開いている Azure PowerShell コマンド プロンプトを右クリックします。この操作により、コマンド セットが一連の PowerShell コマンドとして実行され、Azure 仮想マシンが作成されます。仮想マシンの作成に使用した、サブスクリプション、ストレージ アカウント、クラウド サービス、可用性セット、仮想ネットワーク、またはサブネットが正しくない場合は、仮想マシンを削除し、コマンド ブロックの構文を修正してから正しいコマンド セットを実行してください。 

仮想マシンを作成したら、「[Linux を実行する仮想マシンにログオンする方法](../virtual-machines-linux-how-to-log-on/)」を参照してください。 

この仮想マシンまたは同様のマシンを再び作成する場合は、次のことができます。 

- このコマンド セットをテキスト ファイルまたは PowerShell スクリプト ファイル (*.ps1) として保存する。
- Azure の管理ポータルの **[自動化]** セクションに、このコマンド セットを Azure の Runbook Automation として保存する。 

##<a id="examples"></a>例

次に、Linux ベースの Azure 仮想マシンを作成するために、前の手順を使用して Azure PowerShell コマンド セットを構築する例を 2 つ示します。

###例 1

次の条件で MySQL サーバー用の最初の Linux 仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- Ubuntu Server 12.10 イメージを使用する
- 名前は AZMYSQL1 
- 500 GB の追加データ ディスク容量
- 静的 IP アドレスは 192.168.244.4
- AZDatacenter 仮想ネットワークの BackEnd サブネットに属している
- Azure-TailspinToys クラウド サービスに属している

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。読みやすくするために各ブロックの間に空白行を入れてあります。

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin397A"
	$pass="3A#q291{Y"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###例 2

次の条件で Apache サーバー用に Linux 仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- SUSE Linux Enterprise Server 12 イメージを使用する
- 名前は LOB1
- 50 GB の追加データ ディスク容量 
- 標準 Web トラフィックの LOBServers ロード バランサー セットのメンバーである
- AZDatacenter 仮想ネットワークの FrontEnd サブネットに属している
- Azure-TailspinToys クラウド サービスに属している

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin261Z"
	$pass="9Z2:3Wqp1~"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##その他のリソース

[仮想マシンに関するドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure の仮想マシンに関する FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure の仮想マシンの概要](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell/)

[Linux を実行する仮想マシンにログオンする方法](../virtual-machines-linux-how-to-log-on/)

[Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](../virtual-machines-ps-create-preconfigure-windows-vms/)


<!--HONumber=45--> 