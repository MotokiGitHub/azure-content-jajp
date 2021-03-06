<properties
	pageTitle="AlwaysOn 可用性グループの構成 (GUI) | Microsoft Azure"
	description="Azure Virtual Machines で AlwaysOn 可用性グループを作成します。このチュートリアルでは、スクリプトではなく、主にユーザー インターフェイスとツールを使用します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="jroth" />

# Azure VM での AlwaysOn 可用性グループの構成 (GUI)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


このエンド ツー エンドのチュートリアルでは、Azure の仮想マシン上で実行されている SQL Server AlwaysOn を使用して可用性グループを実装する方法について説明します。

>[AZURE.NOTE]Microsoft Azure 管理ポータルでは、リスナーを含む AlwaysOn 可用性グループ用に新しいギャラリーが設定されています。これを使用すると、AlwaysOn 可用性グループに必要なものすべてが自動的に構成されます。詳細については、[Microsoft Azure ポータル ギャラリーで提供されている SQL Server AlwaysOn テンプレート](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)に関する記事を参照してください。PowerShell を使用するには、「[チュートリアル: Azure AlwaysOn 可用性グループの構成 (PowerShell)](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)」で同じシナリオのチュートリアルを参照してください。

チュートリアルの最後には、次の要素で構成された SQL Server AlwaysOn ソリューションが Azure で完成します。

- 複数のサブネットから成る仮想ネットワーク (フロントエンドのサブネットとバックエンドのサブネットを含む)

- Active Directory (AD) ドメインを持つドメイン コントローラー

- バックエンド サブネットにデプロイされ、AD ドメインに参加している 2 つの SQL Server VM

- 3 ノードの WSFC クラスター (ノード マジョリティ クォーラム モデル)

- 可用性データベースの 2 つの同期コミット レプリカを含む可用性グループ

このソリューションをグラフィカルに表すと、次の図のようになります。

![Test Lab Architecture for AG in Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

これは、考えられる 1 つの構成であることに注意してください。たとえば、Azure でのコンピューティング時間を節約するために、ドメイン コントローラーを 2 つのノードの WSFC クラスターでクォーラム ファイル共有監視として使用することで、2 つのレプリカを持つ可用性グループ用の VM 数を最小限に抑えることができます。この方法では、上記の構成よりも VM 数が 1 つ減少します。

このチュートリアルでは、次のことを前提としています。

- Azure アカウントを既に所有している。

- GUI を使用して、仮想マシン ギャラリーから SQL Server VM をプロビジョニングする方法を知っている。詳細については、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」を参照してください。

- AlwaysOn 可用性グループについて十分に理解している。詳細については、「[AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」を参照してください。

>[AZURE.NOTE]SharePoint での SQL Server AlwaysOn 可用性グループの使用に興味がある場合は、「[SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)」を参照してください。

## Virtual Network とドメイン コントローラー サーバーの作成

最初に新しい Azure 試用版アカウントを作成します。アカウントのセットアップが完了したら、Azure ポータルのホーム画面が表示されます。

1. 次に示すように、ページの左下隅にある **[新規]** をクリックします。

	![Click New in the Portal](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. 次に示すように、**[Network Services]**、**[Virtual Network]**、**[カスタム作成]** の順にクリックします。

	![Create Virtual Network](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. **[仮想ネットワークの作成]** ダイアログ ボックスの各ページで次の設定を使用して、新しい仮想ネットワークを作成します。

	|ページ|設定|
|---|---|
|Virtual Network の詳細|**名前 = ContosoNET**<br/>**リージョン = 米国西部**|
|DNS サーバーと VPN 接続|なし|
|Virtual Network アドレス空間|設定は、次のスクリーンショットを参照してください。![Create Virtual Network](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. 次に、ドメイン コントローラー (DC) として使用する VM を作成します。次に示すように、もう一度 **[新規]** をクリックし、**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

	![Create a VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. **[仮想マシンの作成]** ダイアログ ボックスの各ページで次の設定を使用して、新しい VM を構成します。

	|ページ|設定|
|---|---|
|仮想マシンのオペレーティング システムの選択|Windows Server 2012 R2 Datacenter|
|仮想マシンの構成|**バージョンのリリース日** = (最新)<br/>**仮想マシン名** = ContosoDC<br/>**レベル** = BASIC<br/>**サイズ** = A2 (2 コア)<br/>**新しいユーザー名** = AzureAdmin<br/>**新しいパスワード** = Contoso!000<br/>**確認** = Contoso!000|
|仮想マシンの構成|**クラウド サービス** = 新しいクラウド サービスの作成<br/>**クラウド サービス DNS 名** = 一意のクラウド サービス名<br/>** DNS 名** = 一意の名前 (例: ContosoDC123)<br/>**リージョン/アフィニティ グループ/仮想ネットワーク** = ContosoNET<br/>**仮想ネットワーク サブネット** = バック (10.10.2.0/24)<br/>**ストレージ アカウント** = 自動的に生成されたストレージ アカウントを使用<br/>**可用性セット** = (なし)|
|仮想マシンのオプション|既定値を使用|

新しい VM の構成が完了したら、VM がプロビジョニングされるまで待ちます。このプロセスは完了するまで時間がかかります。Azure ポータルで **[仮想マシン]** タブをクリックすると、ContosoDC の状態が **[開始中 (プロビジョニング中)]** から **[停止済み]**、**[開始中]**、**[実行中 (プロビジョニング中)]**、最後に **[実行中]** へ変化していくことがわかります。

これで、DC サーバーは正常にプロビジョニングされました。次に、この DC サーバー上の Active Directory ドメインを構成します。

## ドメイン コントローラーの構成

次の手順では、corp.contoso.com のドメイン コントローラーとして ContosoDC コンピューターを構成します。

1. ポータルで、**ContosoDC** コンピューターを選択します。**[ダッシュボード]** タブで、**[接続]** をクリックし、リモート デスクトップ アクセス用の RDP ファイルを開きます。

	![Connect to Vritual Machine](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. 構成した管理者アカウント (**\\AzureAdmin**) とパスワード (**Contoso!000**) でログインします。

1. 既定では、**[サーバー マネージャー]** ダッシュボードが表示されます。

1. ダッシュボードの **[役割と機能の追加]** リンクをクリックします。

	![Server Explorer Add Roles](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。

1. **[Active Directory ドメイン サービス]** と **[DNS サーバー]** という役割を選択します。メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。

	>[AZURE.NOTE]静的 IP アドレスがないことを示す検証の警告が表示されます。構成をテストしている場合は、[続行] をクリックします。運用シナリオの場合は、[PowerShell を使用して、ドメイン コントローラー コンピューターの静的 IP アドレスを設定してください](./virtual-network/virtual-networks-reserved-private-ip.md)。

	![Add Roles Dialog](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. **[確認]** セクションが表示されるまで **[次へ]** をクリックします。**[必要に応じて対象サーバーを自動的に再起動する]** チェック ボックスをオンにします。

1. **[インストール]** をクリックします。

1. 機能のインストールが完了したら、**[サーバー マネージャー]** ダッシュボードに戻ります。

1. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。

1. 黄色の警告バーにある **[その他]** リンクをクリックします。

	![AD DS dialog on DNS Server VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、**[このサーバーをドメイン コントローラーに昇格する]** をクリックします。

1. **Active Directory ドメイン サービスの構成ウィザード**で、次の値を使用します。

	|ページ|設定|
|---|---|
|デプロイ構成|**新しいフォレストを追加する** = 選択<br/>**ルート ドメイン名** = corp.contoso.com|
|ドメイン コントローラー オプション|**パスワード** = Contoso!000<br/>**パスワードの確認入力** = Contoso!000|

1. **[次へ]** をクリックして、ウィザード内の他のページを進めます。**[前提条件のチェック]** ページで、"**すべての前提条件のチェックに合格しました**" というメッセージが表示されることを確認します。関連する警告メッセージを確認する必要がありますが、インストールは続行できます。

1. **[インストール]** をクリックします。**ContosoDC** 仮想マシンは自動的に再起動します。

## ドメイン アカウントの構成

次の手順では、後で使用するために Active Directory (AD) アカウントを構成します。

1. **ContosoDC** コンピューターに再度ログインします。

1. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[Active Directory 管理センター]** をクリックします。

	![Active Directory Administrative Center](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. **[Active Directory 管理センター]** で、左側のウィンドウから **[corp (ローカル)]** を選択します。

1. 右側の **[タスク]** ウィンドウで、**[新規]** を選択し、**[ユーザー]** をクリックします。次の設定を使用します。

	|設定|値|
|---|---|
|**名**|インストール|
|**ユーザー SAM アカウント名**|インストール|
|**パスワード**|Contoso!000|
|**パスワードの確認**|Contoso!000|
|**その他のパスワード オプション**|オン|
|**パスワードを無期限にする**|オン|

1. **[OK]** をクリックして **Install** ユーザーを作成します。このアカウントは、フェールオーバー クラスターと可用性グループの構成に使用されます。

1. 同じ手順を使用して、さらに 2 つのユーザー (**CORP\\SQLSvc1** と **CORP\\SQLSvc2**) を作成します。これらのアカウントは、SQL Server インスタンスに使用されます。次に、Windows Server フェールオーバー クラスタリング (WSFC) を構成するために必要なアクセス許可を **CORP\\Install** に付与する必要があります。

1. **[Active Directory 管理センター]** で、左側のウィンドウの **[corp (ローカル)]** を選択します。その後、右側の **[タスク]** ウィンドウで、**[プロパティ]** をクリックします。

	![CORP User Properties](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. **[拡張機能]** を選択し、**[セキュリティ]** タブの **[詳細設定]** をクリックします。

1. **[corp のセキュリティの詳細設定]** ウィンドウで、**[追加]** をクリックします。

1. **[プリンシパルの選択]** をクリックします。次に、「**CORP\\Install**」を検索します。**[OK]** をクリックします。

1. **[すべてのプロパティの読み取り]** アクセス許可と **[コンピューター オブジェクト作成]** アクセス許可を選択します。

	![Corp User Permissions](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. **[OK]** をクリックし、もう一度 **[OK]** をクリックします。corp のプロパティ ウィンドウを閉じます。

これで Active Directory とユーザー オブジェクトの構成が完了したので、3 つの SQL Server VM を作成してこのドメインに参加させます。

## SQL Server VM の作成

次に、3 つの VM (WSFC クラスター ノードと 2 つの SQL Server VM) を作成します。各 VM を作成するには、Azure ポータルに戻り、**[新規]**、**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。その後、次の表のテンプレートを使用すると、VM の作成に役立ちます。

|ページ|VM1|VM2|VM3|
|---|---|---|---|
|仮想マシンのオペレーティング システムの選択|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|仮想マシンの構成|**バージョンのリリース日** = (最新)<br/>**仮想マシン名** = ContosoWSFCNode<br/>**レベル** = BASIC<br/>**サイズ** = A2 (2 コア)<br/>**新しいユーザー名** = AzureAdmin<br/>**新しいパスワード** = Contoso!000<br/>**確認** = Contoso!000|**バージョンのリリース日** = (最新)<br/>**仮想マシン名** = ContosoSQL1<br/>**レベル** = BASIC<br/>**サイズ** = A3 (4 コア)<br/>**新しいユーザー名** = AzureAdmin<br/>**新しいパスワード** = Contoso!000<br/>**確認** = Contoso!000|**バージョンのリリース日** = (最新)<br/>**仮想マシン名** = ContosoSQL2<br/>**レベル** = BASIC<br/>**サイズ** = A3 (4 コア)<br/>**新しいユーザー名** = AzureAdmin<br/>**新しいパスワード** = Contoso!000<br/>**確認** = Contoso!000|
|仮想マシンの構成|**クラウド サービス** = 以前作成された一意のクラウド サービス DNS 名 (例: ContosoDC123)<br/>**リージョン/アフィニティ グループ/仮想ネットワーク** = ContosoNET<br/>**仮想ネットワーク サブネット** = バック (10.10.2.0/24)<br/>**ストレージ アカウント** = 自動的に生成されたストレージ アカウントを使用<br/>**可用性セット** = 可用性セットの作成<br/>**可用性セット名** = SQLHADR|**クラウド サービス** = 以前作成された一意のクラウド サービス DNS 名 (例: ContosoDC123)<br/>**リージョン/アフィニティ グループ/仮想ネットワーク** = ContosoNET<br/>**仮想ネットワーク サブネット** = バック (10.10.2.0/24)<br/>**ストレージ アカウント** = 自動的に生成されたストレージ アカウントを使用<br/>**可用性セット** SQLHADR (可用性セットは、マシンの作成後に構成することもできます。3 つの仮想マシンはすべて SQLHADR 可用性セットに割り当てる必要があります。)|**クラウド サービス** = 以前作成された一意のクラウド サービス DNS 名 (例: ContosoDC123)<br/>**リージョン/アフィニティ グループ/仮想ネットワーク** = ContosoNET<br/>**仮想ネットワーク サブネット** = バック (10.10.2.0/24)<br/>**ストレージ アカウント** = 自動的に生成されたストレージ アカウントを使用<br/>**可用性セット** SQLHADR (可用性セットは、マシンの作成後に構成することもできます。3 つの仮想マシンはすべて SQLHADR 可用性セットに割り当てる必要があります。)|
|仮想マシンのオプション|既定値を使用|既定値を使用|既定値を使用|

3 つの VM が完全にプロビジョニングされたら、その 3 つの VM を **corp.contoso.com** ドメインに参加させて、それらに対する管理者権限を CORP\\Install に付与する必要があります。この操作を行うには、3 つの VM それぞれで、次の手順を実行します。

1. まず、優先 DNS サーバー アドレスを変更します。最初に、一覧で VM を選択して **[接続]** クリックすることで、各 VM のリモート デスクトップ (RDP) ファイルをローカル ディレクトリにダウンロードします。VM を選択するには、次に示すように、選択する行で最初のセル以外の任意の場所をクリックします。

	![Download the RDP File](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. ダウンロードした RDP ファイルを起動し、構成した管理者アカウント (**BUILTIN\\AzureAdmin**) とパスワード (**Contoso!000**) を使用して VM にログインします。

1. ログインすると、**[サーバー マネージャー]** ダッシュボードが表示されます。左側のウィンドウで **[ローカル サーバー]** をクリックします。

1. **[IPv4 アドレス (DHCP により割り当て)、IPv6 (有効)]** リンクを選択します。

1. **[ネットワーク接続]** ウィンドウで、ネットワーク アイコンを選択します。

	![Change the VM Preferred DNS Server](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. コマンド バーの **[この接続の設定を変更する]** をクリックします (ウィンドウのサイズによっては、右向き二重矢印をクリックしないと、このコマンドが表示されない場合があります)。

1. **[インターネット プロトコル バージョン 4 (TCP/IPv4)]** を選択し、[プロパティ] をクリックします。

1. [次の DNS サーバーのアドレスを使う] を選択し、**[優先 DNS サーバー]** に「**10.10.2.4**」を指定します。

1. アドレス **10.10.2.4** は、Azure Virtual Network の 10.10.2.0/24 サブネット内の VM に割り当てられているアドレスです。このアドレスが割り当てられている VM が **ContosoDC** です。**ContosoDC** の IP アドレスを確認するには、次のように、コマンド プロンプトで **nslookup contosodc** を使用します。

	![Use NSLOOKUP to find IP address for DC](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. **[OK]**、**[閉じる]** の順にクリックして、変更をコミットします。これで、VM を **corp.contoso.com** に参加させることができるようになりました。

1. **[ローカル サーバー]** ウィンドウに戻り、**[ワークグループ]** のリンクをクリックします。

1. **[コンピューター名]** セクションで **[変更]** をクリックします。

1. **[ドメイン]** チェック ボックスをオンにし、テキスト ボックスに「**corp.contoso.com**」を入力します。**[OK]** をクリックします。

1. **[Windows セキュリティ]** ポップアップ ダイアログで、既定のドメイン管理者の資格情報であるアカウント (**CORP\\AzureAdmin**) とパスワード (**Contoso!000**) を指定します。

1. "corp.contoso.com ドメインへようこそ" のメッセージが表示されたら、**[OK]** をクリックします。

1. **[閉じる]** をクリックし、ポップアップ ダイアログの **[今すぐ再起動]** をクリックします。

### 各 VM で Corp \\Install ユーザーを管理者として追加する

1. VM が再起動されるまで待った後、RDP ファイルを再び起動し、**BUILTIN\\AzureAdmin** アカウントを使用して VM にログインします。

1. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[コンピューターの管理]** をクリックします。

	![Computer Management](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. **[コンピューターの管理]** ウィンドウで、**[ローカル ユーザーとグループ]** を展開し、**[グループ]** を選択します。

1. **[Administrators]** グループをダブルクリックします。

1. **[Administrators のプロパティ]** ダイアログ ボックスで、**[追加]** をクリックします。

1. ユーザーに「**CORP\\Install**」を入力し、**[OK]** をクリックします。資格情報の入力を求められたら、**AzureAdmin** アカウントと **Contoso!000** パスワードを使用します。

1. **[OK]** をクリックして **[管理者のプロパティ]** ダイアログ ボックスを閉じます。

### 各 VM に**フェールオーバー クラスタリング**機能を追加します。

1. **[サーバー マネージャー]** ダッシュボードの **[役割と機能の追加]** をクリックします。

1. **役割と機能の追加ウィザード**で、**[機能]** ページが表示されるまで **[次へ]** をクリックします。

1. **[フェールオーバー クラスタリング]** を選択します。メッセージが表示されたら、その他の依存する機能を追加します。

	![Add Failover Clustering Feature to VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. **[次へ]** をクリックして、**[確認]** ページの **[インストール]** をクリックします。

1. **フェールオーバー クラスタリング**機能のインストールが完了したら、**[閉じる]** をクリックします。

1. VM からログアウトします。

1. 3 つのサーバー (**ContosoWSFCNode**、**ContosoSQL1**、**ContosoSQL2**) すべてで、このセクションの手順を繰り返します。

これで SQL Server VM がプロビジョニングされ、実行されている状態になりましたが、これらは SQL Server と共に既定のオプションでインストールされています。

## WSFC クラスターの作成

このセクションでは、後で作成する可用性グループをホストする WSFC クラスターを作成します。ここまでに、WSFC クラスター内で使用する 3 つの VM に対して、次の操作を行いました。

- Azure で完全にプロビジョニングする

- VM をドメインに参加させる

- ローカルの Administrators グループに **CORP\\Install** を追加する

- フェールオーバー クラスタリング機能を追加する

これらはすべて、WSFC クラスターに参加させるための各 VM の前提条件です。

また、Azure Virtual Network の動作は、オンプレミス ネットワークとは異なることに注意してください。クラスターは、次の順序で作成する必要があります。

1. ノードのいずれかに単一ノード クラスターを作成します (**ContosoSQL1**)。

1. クラスターの IP アドレスを未使用の IP アドレス (**10.10.2.101**) に変更します。

1. クラスター名をオンラインにします。

1. 他のノード (**ContosoSQL2** と **ContosoWSFCNode**) を追加します。

次の手順に従って、クラスターを完全に構成する上記のタスクを完了してください。

1. **ContosoSQL1** の RDP ファイルを起動し、ドメイン アカウント **CORP\\Install** を使用してログインします。

1. **[サーバー マネージャー]** ダッシュボードで、**[ツール]** を選択し、**[フェールオーバー クラスター マネージャー]** をクリックします。

1. 次に示すように、左側のウィンドウで、**[フェールオーバー クラスター マネージャー]** を右クリックし、**[クラスターの作成]** をクリックします。

	![Create Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. クラスターの作成ウィザードの各ページで、次の設定を使用して、単一ノード クラスターを作成します。

	|ページ|設定|
|---|---|
|開始する前に|既定値を使用|
|サーバーの選択|**[サーバー名の入力]** に「**ContosoSQL1**」と入力し、**[追加]** をクリックします。|
|検証の警告|**[いいえ、このクラスターに Microsoft のサポートは必要ありませんので、検証テストを実行しません。[次へ] をクリックして、クラスターの作成を続行します。]** を選択します。|
|クラスター管理用のアクセス ポイント|**[クラスター名]** に「**Cluster1**」と入力します。|
|確認|記憶域スペースを使用している場合を除き、既定値を使用します。この表の次の注を参照してください。|

	>[AZURE.WARNING]複数のディスクを記憶域プールにグループ化する[記憶域スペース](https://technet.microsoft.com/library/hh831739)を使用している場合は、**[確認]** ページの **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにする必要があります。このチェック ボックスをオフにしないと、クラスタリング処理中に仮想ディスクがデタッチされます。その結果、記憶域スペースがクラスターから削除され、PowerShell を使用して再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーにも表示されなくなります。

1. 左側のウィンドウで、**[フェールオーバー クラスター マネージャー]** を展開し、**[Cluster1.corp.contoso.com]** をクリックします。

1. 中央のウィンドウで、下方法へスクロールして **[クラスター コア リソース]** セクションを表示し、**[名前: Clutser1]** の詳細を展開します。**[名前]** と **[IP アドレス]** リソースの両方が **[失敗]** 状態で表示されます。クラスターにコンピューター自体と同じ IP アドレスが割り当てられていて、アドレスが重複するため、IP アドレス リソースをオンラインにすることができません。

1. 失敗した **IP アドレス** リソースを右クリックし、**[プロパティ]** をクリックします。

	![Cluster Properties](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. **[静的 IP アドレス]** を選択して、[アドレス] ボックスに「**10.10.2.101**」を指定します。次に、**[OK]** をクリックします

1. **[クラスター コア リソース]** セクションで、**[名前: Cluster1]** を右クリックして、**[オンラインにする]** をクリックします。両方のリソースがオンラインになるまで待ちます。クラスター名リソースがオンラインになると、新しい AD コンピューター アカウントで DC サーバーが更新されます。この AD アカウントは、後で可用性グループのクラスター化サービスを実行する際に使用されます。

1. 最後に、残りのノードをクラスターに追加します。次に示すように、ブラウザー ツリーで **[Cluster1.corp.contoso.com]** を右クリックして、**[ノードの追加]** をクリックします。

	![Add Node to the Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. **ノードの追加ウィザード**で、**[次へ]** をクリックします。**[サーバーの選択]** ページで、**ContosoSQL2** と **ContosoWSFCNode** を一覧に追加します。これには、**[サーバー名の入力]** にサーバー名を入力し、**[追加]** をクリックします。完了したら、**[次へ]** をクリックします。

1. **[検証の警告]** ページで **[いいえ]** をクリックします (実際の運用時には、検証テストを実施する必要があります)。次に、**[次へ]** をクリックします。

1. **[確認]** ページで **[次へ]** をクリックしてノードを追加します。

	>[AZURE.WARNING]複数のディスクを記憶域プールにグループ化する[記憶域スペース](https://technet.microsoft.com/library/hh831739)を使用している場合は、**[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにする必要があります。このチェック ボックスをオフにしないと、クラスタリング処理中に仮想ディスクがデタッチされます。その結果、記憶域スペースがクラスターから削除され、PowerShell を使用して再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーにも表示されなくなります。

1. ノードがクラスターに追加されたら、**[完了]** をクリックします。フェールオーバー クラスター マネージャーでは、クラスターに 3 つのノードがあることが示され、その 3 つのノードが **[ノード]** コンテナーの一覧に表示されます。

1. リモート デスクトップ セッションからログアウトします。

## 可用性グループの SQL Server インスタンスの準備

このセクションでは、**ContosoSQL1** と **contosoSQL2** の両方で、次の操作を行います。

- 既定の SQL Server インスタンスに対する必要なアクセス許可を設定して **NT AUTHORITY\\System** のログインを追加する

- 既定の SQL Server インスタンスの sysadmin ロールとして **CORP\\Install** を追加する

- ファイアウォールを解放し、SQL Server のリモート アクセスを許可する

- AlwaysOn 可用性グループ機能を有効にする

- SQL Server サービス アカウントを **CORP\\SQLSvc1** と **CORP\\SQLSvc2** にそれぞれ変更する

上記の操作は、任意の順序で実行できます。ただし、次の手順はこの順序どおりに実行します。この手順は、**ContosoSQL1** と **ContosoSQL2** の両方で実行します。

1. VM のリモート デスクトップ セッションからログアウトしていない場合は、今すぐログアウトしてください。

1. **ContosoSQL1** と **ContosoSQL2** の RDP ファイルを起動し、**BUILTIN\\AzureAdmin** としてログインします。

1. 最初に、必要なアクセス許可と共に **NT AUTHORITY\\System** を SQL Server ログインに追加します。**SQL Server Management Studio** を起動します。

1. **[接続]** をクリックして、既定の SQL Server インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[セキュリティ]**、**[ログイン]** の順に展開します。

1. **[NT AUTHORITY\\System]** ログインを右クリックし、**[プロパティ]** をクリックします。

1. ローカル サーバーの **[セキュリティ保護可能なリソース]** ページで、次のアクセス許可の **[許可]** を選択し、**[OK]** をクリックします。

	- 可用性グループの変更

	- SQL の接続

	- サーバー状態の表示

1. 次に、既定の SQL Server インスタンスに **sysadmin** ロールとして **CORP\\Install** を追加します。**オブジェクト エクスプローラー**で、**[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。

1. **[ログイン名]** に「**CORP\\Install**」と入力します。

1. **[サーバーの役割]** ページで、**[sysadmin]** を選択します。次に、**[OK]** をクリックします作成されたログインは、**オブジェクト エクスプローラー**で **[ログイン]** を展開すると表示されます。

1. 次に、SQL Server のファイアウォール規則を作成します。**スタート**画面から **[セキュリティが強化された Windows ファイアウォール]** を開きます。

1. 左側のウィンドウで、**[受信の規則]** を選択します。右側のウィンドウで、**[新しい規則]** をクリックします。

1. **[規則の種類]** ページで、**[プログラム]** を選択して **[次へ]** をクリックします。

1. **[プログラム]** ページで、**[このプログラムのパス]** を選択し、テキスト ボックスに「**%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe**」と入力します (SQL Server 2012 を使用してこの手順を実行している場合、SQL Server のディレクトリは **MSSQL11.MSSQLSERVER** になります)。その後、**[次へ]** をクリックします。

1. **[操作]** ページで、**[接続を許可する]** を選択したまま、**[次へ]** をクリックします。

1. **[プロファイル]** ページで、既定の設定をそのまま使用し、**[次へ]** をクリックします。

1. **[名前]** ページで、**[名前]** ボックスに規則の名前 (**SQL Server (プログラム規則)** など) を指定し、**[完了]** をクリックします。

1. 次に、**[AlwaysOn 可用性グループ]** 機能を有効にします。**スタート**画面から **SQL Server 構成マネージャー**を起動します。

1. ブラウザー ツリーで、**[SQL Server のサービス]** をクリックし、**[SQL Server (MSSQLSERVER)]** サービスを右クリックして、**[プロパティ]** をクリックします。

1. 次に示すように、**[AlwaysOn 高可用性]** タブをクリックし、**[AlwaysOn 可用性グループを有効にする]** をオンにして、**[適用]** をクリックします。ポップアップ ダイアログで **[OK]** をクリックします。プロパティ ウィンドウはまだ閉じないでください。サービス アカウントを変更した後に SQL Server サービスを再起動します。

	![Enable AlwaysOn Availability Groups](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. 次に、SQL Server サービス アカウントを変更します。**[ログオン]** タブをクリックし、**[アカウント名]** に「**CORP\\SQLSvc1**」(**ContosoSQL1** の場合) または「**CORP\\SQLSvc2**」(**ContosoSQL2** の場合) を入力します。その後、パスワードを入力および確認入力し、**[OK]** をクリックします。

1. ポップアップ ウィンドウで **[はい]** をクリックして、SQL Server サービスを再起動します。SQL Server サービスが再起動されると、プロパティ ウィンドウで行った変更が有効になります。

1. VM からログアウトします。

## 可用性グループの作成

これで、可用性グループを構成できるようになりました。この後の操作の概要は以下のとおりです。

- **ContosoSQL1** に新しいデータベース (**MyDB1**) を作成する

- データベースの完全バックアップとトランザクション ログ バックアップの両方を作成する

- **NORECOVERY** オプションを使用して完全バックアップとログ バックアップを **ContosoSQL2** に復元する

- 同期コミット、自動フェールオーバー、読み取り可能なセカンダリのレプリカを含む可用性グループ (**AG1**) を作成する

### ContosoSQL1 に MyDB1 データベースを作成する

1. **ContosoSQL1** と **ContosoSQL2** のリモート デスクトップ セッションからまだログアウトしていない場合は、今すぐログアウトします。

1. **ContosoSQL1** の RDP ファイルを起動し、**CORP\\Install** としてログインします。

1. **エクスプローラー**で、**C:** ドライブの下に「**backup**」というディレクトリを作成します。このディレクトリは、データベースのバックアップと復元に使用します。

1. 次に示すように、新しいディレクトリを右クリックして **[共有]** をポイントし、**[特定のユーザー]** をクリックします。

	![Create a Backup Folder](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. 次のように、**CORP\\SQLSvc1** を追加して **[読み取り/書き込み]** アクセス許可を指定し、**CORP\\SQLSvc2** を追加して **[読み取り]** アクセス許可を指定した後、**[共有]** をクリックします。ファイル共有プロセスが完了したら、**[完了]** をクリックします。

	![Grant Permissions For Backup Folder](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. 次に、データベースを作成します。**[スタート]** メニューから **SQL Server Management Studio** を起動し、**[接続]** をクリックして既定の SQL Server インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[データベース]** を右クリックし、**[新しいデータベース]** をクリックします。

1. **[データベース名]** に「**MyDB1**」と入力し、**[OK]** をクリックします。

### MyDB1 の完全バックアップを作成して ContosoSQL2 に復元する

1. 次に、データベースの完全バックアップを作成します。**オブジェクト エクスプローラー**で、**[データベース]** を展開し、**[MyDB1]** を右クリックして **[タスク]** をポイントし、**[バックアップ]** をクリックします。

1. **[ソース]** セクションで、**[バックアップの種類]** を **[完全]** に設定しておきます。**[バックアップ先]** セクションで、**[削除]** をクリックして、バックアップ ファイルの既定のファイル パスを削除します。

1. **[バックアップ先]** セクションで、**[追加]** をクリックします。

1. **[ファイル名]** ボックスに「**\\ContosoSQL1\\backup\\MyDB1.bak**」と入力します。**[OK]** をクリックし、もう一度 **[OK]** をクリックしてデータベースをバックアップします。バックアップ操作が完了したら、もう一度 **[OK]** をクリックしてダイアログを閉じます。

1. 次に、データベースのトランザクション ログ バックアップを作成します。**オブジェクト エクスプローラー**で、**[データベース]** を展開し、**[MyDB1]** を右クリックして **[タスク]** をポイントし、**[バックアップ]** をクリックします。

1. **[バックアップの種類]** で **[トランザクション ログ]** を選択します。**[バックアップ先]** のファイル パスの設定を、前に指定したパスのままにし、**[OK]** をクリックします。バックアップ操作が完了したら、もう一度 **[OK]** をクリックします。

1. 次に、**ContosoSQL2** で完全バックアップとトランザクション ログ バックアップを復元します。**ContosoSQL2** の RDP ファイルを起動し、**CORP\\Install** としてログインします。**ContosoSQL1** のリモート デスクトップ セッションは、開いたままにします。

1. **[スタート]** メニューから **SQL Server Management Studio** を起動し、**[接続]** をクリックして既定の SQL Server インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[データベース]** を右クリックし、**[データベースの復元]** をクリックします。

1. **[ソース]** セクションで **[デバイス]** を選択し、省略記号 (**…**) ボタンをクリックします。

1. **[バックアップ デバイスの選択]** で、**[追加]** をクリックします。

1. [バックアップ ファイルの場所] に「\\ContosoSQL1\\backup」を入力して [更新] をクリックし、[MyDB1.bak] を選択して [OK] をクリックした後、もう一度 [OK] をクリックします。[復元するバックアップ セット] ウィンドウに完全バックアップとログ バックアップが表示されます。

1. [オプション] ページに移動し、[復旧状態] で [RESTORE WITH NORECOVERY] を選択し、[OK] をクリックしてデータベースを復元します。復元操作が完了したら、[OK] をクリックします。

### 可用性グループを作成する

1. **ContosoSQL1** のリモート デスクトップ セッションに戻ります。次に示すように、SSMS の**オブジェクト エクスプローラー**で、**[AlwaysOn 高可用性]** を右クリックして **[新しい可用性グループ ウィザード]** をクリックします。

	![Launch New Availability Group Wizard](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. **[説明]** ページで **[次へ]** をクリックします。**[可用性グループ名の指定]** ページで、**[可用性グループ名]** に「**AG1**」と入力し、もう一度 **[次へ]** をクリックします。

	![New AG Wizard, Specify AG Name](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. **[データベースの選択]** ページで、**[MyDB1]** を選択し、**[次へ]** をクリックします。対象とするプライマリ レプリカで完全バックアップを少なくとも 1 つは作成しているため、このデータベースは可用性グループの前提条件を満たしています。

	![New AG Wizard, Select Databases](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. **[レプリカの指定]** ページで **[レプリカの追加]** をクリックします。

	![New AG Wizard, Specify Replicas](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. **[サーバーに接続]** ダイアログが表示されます。**[サーバー名]** に「**ContosoSQL2**」と入力し、**[接続]** をクリックします。

	![New AG Wizard, Connect to Server](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. **[レプリカの指定]** ページに戻ると、**[可用性レプリカ]** の一覧に **ContosoSQL2** が表示されていることがわかります。次に示すようにレプリカを構成します。構成し終わったら、**[次へ]** をクリックします。

	![New AG Wizard, Specify Replicas (Complete)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. **[最初のデータの同期を選択]** ページで、**[結合のみ]** を選択し、**[次へ]** をクリックします。データの同期は、**ContosoSQL1** で完全バックアップとトランザクション バックアップを作成し、**ContosoSQL2** で復元したときに既に手動で実行しています。データベースのバックアップの作成操作と復元操作を実行しない代わりに、新しい可用性グループ ウィザードで **[完全]** を選択すると、データの同期を実行させることができます。ただし、一部の企業で使用されている大規模なデータベースではこの方法はお勧めしません。

	![New AG Wizard, Select Initial Data Synchronization](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. **[検証]** ページで **[次へ]** をクリックします。このページは、次のようになっています。可用性グループ リスナーを構成していないため、リスナー構成に関する警告が表示されます。このチュートリアルではリスナーを構成しないため、この警告を無視できます。チュートリアルの完了後にリスナーを構成するには、[Azure での AlwaysOn 可用性グループの ILB リスナーの構成](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)に関するページを参照してください。

	![New AG Wizard, Validation](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. **[概要]** ページで、**[完了]** をクリックし、新しい可用性グループが構成されるまで待ちます。**[進行状況]** ページで **[詳細]** をクリックすると、進行状況が詳しく表示されます。ウィザードが完了したら、**[結果]** ページを調べて、次のように、可用性グループが正常に作成されたことを確認します。その後、**[閉じる]** をクリックしてウィザードを終了します。

	![New AG Wizard, Results](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. **オブジェクト エクスプローラー**で、**[AlwaysOn 高可用性]**、**[可用性グループ]** の順に展開します。このコンテナー内に新しい可用性グループが表示されます。**[AG1 (プライマリ)]** を右クリックして **[ダッシュボードの表示]** をクリックします。

	![Show AG Dashboard](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. **AlwaysOn ダッシュボード**は次のように表示されます。レプリカ、各レプリカのフェールオーバー モード、および同期の状態を確認できます。

	![AG Dashboard](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. **[サーバー マネージャー]** に戻り、**[ツール]** を選択し、**[フェールオーバー クラスター マネージャー]** を起動します。

1. **[Cluster1.corp.contoso.com]**、**[サービスとアプリケーション]** の順に展開します。**[役割]** を選択し、**AG1** 可用性グループの役割が作成されていることに注目します。リスナーを構成していないため、AG1 には、データベース クライアントが可用性グループに接続する際に使用する IP アドレスがありません。読み取り/書き込み操作の場合はプライマリ ノードに、読み取り専用クエリの場合はセカンダリ ノードに直接接続できます。

	![AG in Failover Cluster Manager](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING]フェールオーバー クラスター マネージャーから、可用性グループのフェールオーバーを実行しないでください。すべてのフェールオーバー操作は、SSMS の **AlwaysOn ダッシュボード**から実行する必要があります。詳細については、[WSFC フェールオーバー クラスター マネージャーと可用性グループの使用に関する制限事項](https://msdn.microsoft.com/library/ff929171.aspx)のページを参照してください。

## 次のステップ
これで、Azure に可用性グループを作成して、SQL Server AlwaysOn を実装できました。この可用性グループのリスナーを構成するには、[Azure での AlwaysOn 可用性グループの ILB リスナーの構成](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)に関するページを参照してください。

Azure での SQL Server の使用に関するその他の情報については、「[Azure Virtual Machines における SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)」を参照してください。

<!---HONumber=Oct15_HO3-->