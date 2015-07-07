<properties 
	pageTitle="Azure でのレプリカ ドメイン コントローラーのインストール" 
	description="このチュートリアルでは、Azure の仮想マシンに内部設置型の Active Directory フォレストからドメイン コントローラーをインストールする方法について説明します。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="Justinha"/>


# Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール

このトピックでは、Azure の仮想ネットワークの Azure 仮想マシン (VM) 上に、内部設置型 Active Directory ドメインの追加のドメイン コントローラー (レプリカ DC とも呼ばれます) をインストールする方法を説明します。 

必要に応じて次の関連するトピックもご覧ください。

- オプションで、Azure の仮想ネットワーク上に新しい Active Directory フォレストをインストールすることもできます。これらの手順については、「[Azure の仮想ネットワークでの Active Directory フォレストのインストール](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)」をご覧ください。
-  Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)」をご覧ください。
-  Azure で AD DS を含むテスト ラボ環境を作成する手順については、「[Test Lab Guide:Base Configuration in Azure (Azure での基本構成に関するテスト ラボ ガイド)](http://www.microsoft.com/download/details.aspx?id=41684)」をご覧ください。

## シナリオ図

このシナリオでは、外部ユーザーは、ドメインに参加しているサーバーで稼働するアプリケーションにアクセスする必要があります。アプリケーション サーバーとレプリカ DC を実行する VM は、Azure の仮想ネットワーク内にインストールされています。次の図に示すように、仮想ネットワークは[サイト間 VPN](http://msdn.microsoft.com/library/azure/dn133795.aspx) 接続で、内部設置型ネットワークに接続できます。また、接続を高速化するために、[ExpressRoute](http://azure.microsoft.com/services/expressroute/) を使用することもできます。 

アプリケーション サーバーと DC は、コンピューティング処理を分散するために別々の[クラウド サービス](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/)内にデプロイされます。また、フォールト トレランスを強化するために[可用性セット](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/)内にデプロイされます。 
DC は、Active Directory レプリケーションを使用して、DC 同士で相互にレプリケートします。また、内部設置型 DC でレプリケートされます。同期ツールは必要ありません。

![][1]

## 手順 1:Azure の仮想ネットワークの Active Directory サイトを作成する

仮想ネットワークに対応するネットワーク地域の Active Directory にサイトを作成することをお勧めします。認証、レプリケーション、他の DC の検出オペレーションが最適化されます。次の手順では、サイトの作成方法を説明しています。背景情報については、「[Adding a New Site (新しいサイトの追加)](http://technet.microsoft.com/library/cc781496.aspx)」をご覧ください。

1. Active Directory サイトとサービスを開きます:**[サーバー マネージャー]**、**[ツール]**、**[Active Directory サイトとサービス]** の順に選択します。
2. Azure の仮想ネットワークを作成したリージョンを表すサイトを作成します: **[サイト]**、**[アクション]**、**[新しいサイト]** の順にクリックし、「Azure US West」などの新しいサイトの名前を入力して、サイト リンクを選択し、**[OK]** をクリックします。
3. サブネットを作成し、新しいサイトに関連付けます: **[サイト]** をダブルクリックして、**[サブネット]**、**[新しいサブネット]** の順に右クリックし、仮想ネットワークの IP アドレスの範囲 (例: シナリオ図の 10.1.0.0/16) を入力し、新しい Azure サイトを選択して、**[OK]** をクリックします。

## 手順 2:Azure の仮想ネットワークを作成する

<ol><li><p>Azure 管理ポータルで、<b>[新規]</b> > <b>[ネットワーク サービス]</b> > <b>[仮想ネットワーク]</b> > <b>[カスタム作成]</b> とクリックし、次の値を使用して、ウィザードを完了します。</p>

<table style="width:100%">
<tr>
<td>ウィザードのページ</td>
<td>指定する値</td>
</tr>
<tr>
<td><b>仮想ネットワークの詳細</b></td>
<td><ul><li>名前:「WestUSVNet」などの仮想ネットワークの名前を入力します。</li><li>リージョン:West US などの、仮想ネットワークが配置される Azure の場所です。仮想ネットワークを作成した後は、この場所を変更することはできません。</li></ul>
</td>
</tr>
<tr>
<td><b>DNS サーバーと VPN 接続</b></td>
<td><ul><li>DNS サーバー:1 つまたは複数の内部設置型 DNS サーバーの名前と IP アドレスを指定します。</li><li>接続:<b>[サイト間 VPN の構成]</b> を選択します。</li><li>ローカル ネットワーク:新しいローカル ネットワークを指定します。</li></ul>VPN ではなく ExpressRoute を使用している場合は、「 <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Exchange プロバイダーを通じて ExpressRoute 接続を構成する</a>」をご覧ください。</td>
</tr>
<tr>
<td><b>サイト間接続</b></td>
<td><ul><li>名前:内部設置型ネットワークの名前を入力します。</li><li>VPN デバイスの IP アドレス:仮想ネットワークに接続するデバイスのパブリック IP アドレスを指定します。VPN デバイスは NAT の内側に配置することはできません。</li><li>アドレス:内部設置型ネットワークのアドレスの範囲 (例: シナリオ図の 192.168.0.0/16) を指定します。</li></ul></td>
</tr>
<tr>
<td><b>仮想ネットワーク アドレス空間</b></td>
<td><ul><li>アドレス空間:Azure の仮想ネットワークで実行する VM のIP アドレスの範囲 (例: シナリオ図の 10.1.0.0/16) を指定します。このアドレス範囲を、内部設置型ネットワークのアドレスの範囲と重複させることはできません。</li><li>サブネット:アプリケーション サーバーのサブネットの名前とアドレス (例: Frontend、10.1.1.0/24) と、DC のサブネットの名前とアドレス (例: Backend、10.1.2.0/24) を指定します。</li><li><b>[ゲートウェイ サブネットの追加]</b> をクリックします。</li></ul></td>
</tr>
</table>
</li>
<li><p>次に、仮想ネットワーク ゲートウェイを構成して、セキュリティで保護されたサイト間 VPN 接続を作成します。手順については、「 <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">管理ポータルでの仮想ネットワーク ゲートウェイの構成</a> 」をご覧ください。</p>
</li>
<li><p>新しい仮想ネットワークと内部設置型 VPN デバイスの間に、サイト間 VPN 接続を作成します。手順については、「 <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">管理ポータルでの仮想ネットワーク ゲートウェイの構成</a> 」をご覧ください。</p>
</li>
</ol>

## 手順 3:DC ロールの Azure VM を作成する

<p>次の手順を繰り返して、必要に応じて、DC ロールをホストする VM を作成します。フォールト トレランスと冗長性を確保するには 2 つ以上の仮想 DC をデプロイする必要があります。Azure の仮想ネットワークに同様に構成されている DC が 2 つ以上の含まれる場合 (つまり、GC が両方とも DNS サーバーで実行され、どちらも FSMO の役割が当てはまらないなど)、可用性セットでこうした DC を実行する VM を配置してフォールト トレランスを向上させます。</p>


<ol><li><p>Azure 管理ポータルで、<b>[新規]</b> > <b>[コンピューティング]</b> > <b>[バーチャル マシン]</b> > <b>[ギャラリーから]</b> の順にクリックします。次の値を使用して、ウィザードを完了します。別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。</p>
<table style="width:100%">
<tr>
<td><b>ウィザードのページ</b></td>
<td><b>指定する値</b></td>
</tr>
<tr>
<td><b>イメージの選択</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><ul><li>仮想マシン名:単一ラベルの名前 (例: AzureDC2) を入力します。</li><li>新しいユーザー名:ユーザーの名前を入力します。このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。最初に VM にサインインするときは、この名前でサインインする必要があります。Administrator という名前の組み込みのアカウントは機能しません。</li><li>新しいパスワード/確認:パスワードを入力します。</li></ul></td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><ul><li>クラウド サービス:1 台目の VM の作成時には、最初の VM 用の <b>[新しいクラウド サービスの作成]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</li><li>クラウド サービス DNS 名:グローバルに一意の名前を指定します。</li><li>リージョン/アフィニティ グループ/仮想ネットワーク:仮想ネットワーク名 (例: WestUSVNet) を指定します。</li><li>ストレージ アカウント:最初の VM に <b>[自動的に生成されたストレージ アカウントを使用]</b> を選択し、DC ロールをホストする VM をさらに作成する場合は、1 台目の VM と同じストレージ アカウント名を選択します。</li><li>可用性セット:<b>[可用性セットの作成]</b> を選択します。</li><li>可用性セット名:1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</li></ul></td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><b>[Install the VM Agent (VM エージェントをインストール)]</b> とそのほかに必要な拡張機能を選択します。</td>
</tr>
</table>
</li>
<li><p>DC サーバーのロールを実行する各 VM にディスクを接続します。AD データベース、ログ、SYSVOL を格納するには、追加のディスクが必要です。ディスクのサイズ (10 GB など) を指定し、<b>[ホスト キャッシュの設定]</b> を <b>[なし]</b> に設定したままにします。VM に初めてサインインした後、<b>[サーバー マネージャー]</b> > <b>[ファイル サービスと記憶域サービス]</b> を開き、NTFS を使用してこのディスクでボリュームを作成します。</p></li>
<li><p>DC ロールを実行する VM の静的 IP アドレスを予約します。静的 IP アドレスを予約するには、Microsoft Web プラットフォーム インストーラーをダウンロードし、 <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">Azure PowerShell をインストール</a> して、 <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> コマンドレットを実行します。</p></li>

</ol>

## 手順 4:Azure VM に AD DS をインストールする

VM にサインインし、サイト間 VPN 接続または ExpressRoute 接続で、内部設置型ネットワーク上のリソースに接続できることを確認します。次に、Azure VM に AD DS をインストールします。内部設置型ネットワーク上に追加の DC をインストールするために使用するのと同じプロセス (UI、Windows PowerShell、応答ファイル) を使用できます。AD DS をインストールするときには、AD データベース、ログ、SYSVOL の場所の新しいボリュームを必ず指定します。AD DS のインストール方法がわからない場合は、「[Active Directory ドメイン サービスをインストールする (レベル 100)](http://technet.microsoft.com/library/hh472162.aspx)」、または、「[Windows Server 2012 のレプリカ ドメイン コントローラーを既存のドメインにインストールする (レベル 200)](http://technet.microsoft.com/library/jj574134.aspx)」をご覧ください。

## 手順 5:仮想ネットワークの DNS サーバーを再構成する

<ol>
<li><p>Azure 管理ポータルで、仮想ネットワークの名前をクリックし、次に <b>[構成]</b> タブをクリックして <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">仮想ネットワークの DNS サーバーの IP アドレスを再構成</a> し、内部設置型 DNS サーバーの IP アドレスではなく、レプリカ DC に割り当てられた静的 IP アドレスを使用します。 </p>
</li>
<li><p>仮想ネットワークのすべてのレプリカ DC VM が仮想ネットワーク上の DNS サーバーを使用するように構成されていることを確認には、<b>[仮想マシン]</b> クリックし、VM ごとの [状態] 列をクリックして、<b>[再起動]</b> をクリックします。サインインする前に、VM で<b>実行</b>状態が表示されるまで待機します。 
</p>
</li>
</ol>

## 手順 6:アプリケーション サーバー用の VM を作成する

<ol><li><p>次の手順を繰り返して、アプリケーション サーバーとして実行するように VM を設定します。別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。</p>

<table style="width:100%">
<tr>
<td><b>ウィザードのページ</b></td>
<td><b>指定する値</b></td>
</tr>
<tr>
<td><b>イメージの選択</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><ul><li>仮想マシン名:単一ラベルの名前 (例: TreyAppServer1) を入力します。</li><li>新しいユーザー名:ユーザーの名前を入力します。このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。最初に VM にサインインするときは、この名前でサインインする必要があります。Administrator という名前の組み込みのアカウントは機能しません。</li><li>新しいパスワード/確認:パスワードを入力します。</li></ul></td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><ul><li>クラウド サービス:1 台目の VM の作成時には、最初の VM 用の [新しいクラウド サービスの作成] を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</li><li>クラウド サービス DNS 名:グローバルに一意の名前を指定します。</li><li>リージョン/アフィニティ グループ/仮想ネットワーク:仮想ネットワーク名 (例: WestUSVNet) を指定します。</li><li>ストレージ アカウント:最初の VM に <b>[自動的に生成されたストレージ アカウントを使用]</b> を選択し、DC ロールをホストする VM をさらに作成する場合は、1 台目の VM と同じストレージ アカウント名を選択します。</li><li>可用性セット:<b>[可用性セットの作成]</b> を選択します。</li><li>可用性セット名:1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</li></ul></td>
</tr>
<tr>
<td><b>仮想マシンの構成</b></td>
<td><b>[Install the VM Agent (VM エージェントをインストール)]</b> とそのほかに必要な拡張機能を選択します。</td>
</tr>
</table>


</li>
<li><p>各 VM がプロビジョニングされたら、サインインし、ドメインに参加させます。<b>サーバー マネージャー</b>で、<b>[ローカル サーバー]</b> > <b>[ワークグループ]</b> > <b>[変更...]</b> の順にクリックし、<b>[ドメイン]</b> を選択して、内部設置型ドメインの名前を入力します。ドメイン ユーザーの資格情報を入力し、VM を再起動して、ドメインへの参加を完了します。
</p>
</li>
</ol>
<p>
VM のプロビジョニングには、管理ポータルを使用する代わりに、Microsoft Azure の Windows PowerShell を使用することもできます。最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> と <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> を使用し、VM を作成するには、 <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> を使用します。 
</p>

Windows PowerShell の使い方の詳細については、「[Azure PowerShell の概要](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)」と「[Azure 管理コマンドレット](http://msdn.microsoft.com/library/windowsazure/jj152841)」をご覧ください。


## その他のリソース

-  [Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Azure PowerShell を使用して既存のオンプレミス ハイパー-V のドメイン コント ローラーを Azure にアップロードする方法](http://support.microsoft.com/kb/2904015)

-  [Azure の仮想ネットワークでの Active Directory フォレストのインストール](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Azure 仮想ネットワーク](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Azure の管理コマンドレット](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=47-->
 