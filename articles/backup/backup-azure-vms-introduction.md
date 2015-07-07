<properties
	pageTitle="Azure 仮想マシンのバックアップの概要"
	description="Azure Backup サービスを使用した Azure 仮想マシンのバックアップの概要"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>

# Azure 仮想マシンのバックアップ - 概要

このセクションでは、Azure 仮想マシンを保護する Microsoft Azure Backup の使用方法の概要を示します。この記事では、次の項目について説明します。

- Azure 仮想マシンのバックアップの機能
- Azure 仮想マシンのバックアップの手順
- スムーズなバックアップ操作を実現するための前提条件
- 一般的なエラーの発生とその対処法
- サポートされていないシナリオと、製品への変更に影響を与える方法の一覧

Azure 仮想マシンの詳細については、「[Virtual Machine のドキュメント](https://azure.microsoft.com/documentation/services/virtual-machines/)」をご覧ください。

## Azure 仮想マシンをバックアップする理由
クラウド コンピューティングは、拡張性と可用性の高い環境でアプリケーションを実行できます。これが、マイクロソフトが Azure 仮想マシンを開発した理由です。Azure 仮想マシンから生成されるデータは重要であるため、保管のためのバックアップが必要です。バックアップからデータを復元する必要がある一般的なシナリオは、次のとおりです。

- 偶発的または悪意のあるファイルの削除
- 修正プログラム更新中の仮想マシンの破損
- 偶発的または悪意のある仮想マシン全体の削除

このような仮想マシンから次の 2 つの方法でデータをバックアップできます。

- 仮想マシン内のデータのソースを個別にバックアップする
- 仮想マシン全体をバックアップする

管理しやすく、アプリケーションとオペレーティング システム全体の復元も簡単にできるため、仮想マシン全体のバックアップが一般的です。Azure Backup は、インゲスト データ バックアップまたは仮想マシンの完全バックアップで使用されます。

仮想マシンのバックアップに Azure Backup を使用するビジネス上のメリットは、次のとおりです。

- 仮想マシンのバックアップと回復のワークフローを自動化します。
- アプリケーション整合性のあるバックアップにより、回復されたデータが一貫性のある状態から開始されることを保証します。
- 仮想マシンのバックアップに関連するダウンタイムがありません。
- Windows や Linux の仮想マシンをバックアップできます。
- 回復ポイントを Azure Backup 資格情報コンテナーに簡単に復元できます。
- 古い回復ポイントの自動排除とガベージ コレクションを実行します。

## Azure 仮想マシンのバックアップの動作
仮想マシンをバックアップするには、まずデータの特定の時点のスナップショットが必要です。Azure Backup サービスは、スケジュールされた時刻にバックアップ ジョブを開始し、バックアップ拡張機能をトリガーしてスナップショットを作成します。バックアップ拡張機能は、インゲスト VSS サービスと連携して一貫性を実現し、一貫性に達すると、Azure ストレージ サービスの BLOB スナップショット API を呼び出します。これにより、仮想マシンをシャット ダウンすることなく、一貫性のあるディスクのスナップショットが作成されます。

スナップショットが作成されると、データは Azure Backup サービスによってバックアップ資格情報コンテナーに転送されます。このサービスは、最新のバックアップから変更されたブロックのみを識別して転送することで、バックアップ ストレージの効率を高めます。データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。この回復ポイントは、Microsoft Azure 管理ポータルで確認できます。

![Azure 仮想マシンのバックアップ アーキテクチャ](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Linux 仮想マシンでは、ファイル整合性のバックアップのみが可能です。

## 保護するインスタンスの計算
Azure Backup を使用してバックアップされている Azure 仮想マシンは、[Azure バックアップ料金](http://azure.microsoft.com/pricing/details/backup/)の対象になります。保護するインスタンスの計算は、仮想マシンの*実際*のサイズ (”リソース ディスク” を除く、仮想マシン上の全データの合計) に基づきます。仮想マシンに接続されている各データ ディスクがサポートする最大サイズ*ではなく*、データ ディスクに格納されている実際のデータに基づいて課金されます。同様に、バックアップ ストレージの課金は、Azure Backup で格納されているデータ量 (各回復ポイントの実際のデータの合計) に基づきます。

たとえば、最大サイズが各 1 TB のデータ ディスクが 2 台追加で搭載されている A2 標準サイズの仮想マシンがあります。次の表は、これらの各ディスクに格納されている実際のデータを示しています。

|ディスクの種類|最大サイズ|実際のデータ量|
|---------|--------|------|
| OS ディスク | 1023 GB | 17 GB |
| ローカル ディスク / リソース ディスク | 135 GB | 5 GB (バックアップに含まれない) |
| データ ディスク 1 |	1023 GB | 30 GB |
| データ ディスク 2 | 1023 GB | 0 GB |

この場合の仮想マシンの*実際*のサイズは、17 GB + 30 GB + 0 GB = 47 GB です。これが毎月の課金の基になる保護するインスタンス サイズになります。仮想マシンのデータ量が大きくなると、課金に使用される保護するインスタンス サイズも変化します。

課金は、最初のバックアップが正常に完了するまでは開始されません。この時点では、ストレージと保護するインスタンスの両方に対する課金が開始されます。仮想マシンの *Azure Backup で格納されているバックアップ データ*がある限り、課金は継続します。バックアップ データを保持している場合は、保護の停止操作を行っても課金は停止されません。保護が停止され、*さらに*バックアップ データが削除されている場合のみ、指定した仮想マシンの課金が停止されます。アクティブなバックアップ ジョブがない場合 (保護が停止されている場合) は、最後にバックアップに成功した時点の仮想マシンのサイズは、毎月の課金の基になる保護するインスタンス サイズになります。

## 前提条件
### 1.バックアップ資格情報コンテナー
Azure 仮想マシンのバックアップを開始するにはまずバックアップ資格情報コンテナーを作成する必要があります。資格情報コンテナーは、時間の経過と共に作成されるすべてのバックアップと回復ポイントを格納するエンティティです。資格情報コンテナーには、バックアップ対象の仮想マシンに適用されるバックアップ ポリシーも含まれています。

以下の図は、さまざまな Azure Backup エンティティの関係を示したものです。![Azure Backup のエンティティとの関係](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### バックアップ資格情報コンテナーを作成するには

1. [管理ポータル](http://manage.windowsazure.com/)にサインインします。

2. **[新規]** > **[Data Services]** > **[復旧サービス]** > **[バックアップ資格情報コンテナー]** > **[簡易作成]** の順にクリックします。組織のアカウントに関連付けられたサブスクリプションが複数ある場合は、正しいアカウントを選択してバックアップ資格情報コンテナーに関連付けてください。各 Azure サブスクリプションに複数のバックアップ資格情報コンテナーを保有し、保護する仮想マシンを編成できます。

3. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。これは、サブスクリプションごとに一意である必要があります。

4. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。資格情報コンテナーは、保護する仮想マシンと同じリージョンにある必要があります。仮想マシンが別のリージョンにある場合は、資格情報コンテナーをそれぞれ作成します。バックアップ データを格納するストレージ アカウントを指定する必要はありません。バックアップ資格情報のコンテナーと Azure Backup サービスはこれを自動的に処理します。![バックアップ資格情報コンテナーの作成](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]リージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。「[リージョン別のサービス](http://azure.microsoft.com/regions/#services)」の一覧をご確認ください。目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。

5. **[資格情報コンテナーの作成]** をクリックします。バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。ポータルの下部にある状態の通知を監視します。![資格情報コンテナーのトースト通知の作成](./media/backup-azure-vms-introduction/creating-vault.png)

6. 資格情報コンテナーが正常に作成されたことを示すメッセージが表示され、[復旧サービス] ページに [アクティブ] と表示されます。![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. バックアップ資格情報コンテナーをクリックして、**[クイック スタート]** ページに進むと、Azure 仮想マシンのバックアップ手順が表示されます。![ダッシュ ボード ページの仮想マシンのバックアップ手順](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]資格情報コンテナーを作成したら、適切なストレージの冗長オプションが選択されていることをご確認ください。詳細については、「[setting the storage redundancy option in the backup vault (バックアップ資格情報コンテナーのストレージ冗長オプションの設定)][vault-storage-redundancy]」をご覧ください。

### 2.VM エージェント
Azure 仮想マシンのバックアップを開始する前に、Azure VM エージェントが仮想マシンに正しくインストールされていることをご確認ください。仮想マシンをバックアップするために、Azure Backup サービスは VM エージェントの拡張機能をインストールします。VM エージェントは仮想マシンを作成する際のオプション コンポーネントであるため、仮想マシンをプロビジョニングする前に、VM エージェントのチェック ボックスがオンになっていることを確認する必要があります。

詳細については、「[VM エージェント](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)」と「[インストール方法](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」に関するページをご覧ください。

>[AZURE.NOTE]仮想マシンをオンプレミスのデータ センターから Azure に移行する場合は、移行プロセスを開始する前に、VM エージェント MSI をダウンロードしてインストールしてください。これは、Azure Site Recovery を使用して Azure で保護される仮想マシンにも適用されます。

## プレビュー中の制限事項

- 5 台以上のディスクを搭載した仮想マシンのバックアップはサポートされません。
- Premium Storage を使用した仮想マシンのバックアップはサポートされません。
- 複数のNIC を使用した仮想マシンのバックアップ、または負荷分散の構成の仮想マシンのバックアップはサポートされません。
- 復元中に既存の仮想マシンを置き換えることはサポートされません。まず既存の仮想マシンに関連付けられているディスクを削除し、次にバックアップからデータを復元します。
- Azure Site Recovery を使用して復元された仮想マシンのバックアップはサポートされません。
- リージョン間のバックアップと復元はサポートされません。
- リージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。「[リージョン別のサービス](http://azure.microsoft.com/regions/#services)」の一覧をご確認ください。目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。
- オペレーティング システムのバージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。
  - **Linux**: Azure で動作保証済みのディストリビューションの一覧は、[こちら](../virtual-machines-linux-endorsed-distributions.md)でご確認ください。他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作する限り使用できます。
  - **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。

確認したい機能が含まれている場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## 次のステップ
仮想マシンのバックアップを開始するには、次をご覧ください。

- [仮想マシンの検出、登録、保護](backup-azure-vms.md)

- [仮想マシンの復元](backup-azure-restore-vms.md)

+ バックアップ ジョブの監視

<!---HONumber=GIT-SubDir--> 