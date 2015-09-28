<properties
   pageTitle="Microsoft Azure IaaS でのソフトウェアの更新のためのベスト プラクティス | Microsoft Azure"
   description="この記事では、Microsoft Azure IaaS 環境でのソフトウェアの更新に関するさまざまなベスト プラクティスについて説明します。組織のセキュリティ担当者やコンプライアンス担当者など、日常的に変更管理、ソフトウェアの更新、および資産管理に携わる IT プロフェッショナルとセキュリティ アナリストを対象とした内容です。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="yurid"/>

#Microsoft Azure IaaS でのソフトウェアの更新のためのベスト プラクティス


Azure IaaS 環境のベスト プラクティスについて議論する前に、どのようなシナリオでソフトウェア更新を管理できるのかを理解しておくことが重要です。次の図を参照してください。

![クラウド モデルと義務](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

インフラストラクチャ全体がオンプレミスである従来のデータ センター モデルでは、オペレーティング システム、アプリケーション、ネットワーク デバイス (ルーターやスイッチなど)、およびハードウェア (ファームウェア) の更新を管理する全責任はユーザーにあります。IaaS のシナリオにおいても、オペレーティング システムおよびアプリケーションの更新はユーザーが管理する必要がありますが、オペレーティング システムとアプリケーションの基盤となるインフラストラクチャ全体は Microsoft によって管理されます。これらすべてのモデルでは、お客様はデータの所有者であり、エンド ポイント レベルでデータを保護する責任を担っています。

PaaS のシナリオでは、オペレーティング システムの更新の管理は Microsoft の責任となるため、ソフトウェアの更新に対するユーザーの責任が軽減されます。SaaS のシナリオでは、スタック全体のソフトウェアの更新について Microsoft が責任を持ちます。

次の図に示すように、Azure IaaS VM を使用してオンプレミスのリソースと通信するハイブリッド シナリオに関して、これらの同じ原則が適用されます。

![Microsoft Azure での一般的なハイブリッド シナリオ](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## 最初の評価

会社が既に更新管理システムを使用し、所定のソフトウェア更新ポリシーを保持している場合でも、頻繁に以前のポリシーの評価を見直して現在の要件に基づいて更新することが重要です。つまり、社内のリソースの現在の状態について理解しておく必要があります。そのためには以下の情報が必要です。

-   社内の物理コンピューターと仮想コンピューター

-   各物理/仮想コンピューターで実行されているオペレーティング システムとバージョン

-   各コンピューターに現在インストールされているソフトウェアの更新プログラム (Service Pack バージョン、ソフトウェア更新プログラム、およびその他の変更)

-   社内の各コンピューターで実行される機能

-   各コンピューターで実行されるアプリケーションとプログラム

-   各コンピューターの所有権と連絡先の情報

-   環境内に存在する資産と、最も注意して保護する必要のある領域を特定するための相対的な価値

-   既知のセキュリティ問題と、新しいセキュリティ問題またはセキュリティ レベルの変更を識別するための会社の所定のプロセス

-   使用している環境をセキュリティで保護するためにデプロイされた対処法

この情報は定期的に更新する必要があり、ソフトウェア更新管理プロセスに関係するユーザーがすぐに利用できるようにしておく必要があります。

## ベースラインの確立

ソフトウェア更新管理プロセスの重要な部分は、社内のコンピューターのオペレーティング システムのバージョン、アプリケーション、およびハードウェアの初期の標準インストールを作成することです。これらはベースラインと呼ばれます。ベースラインは、特定の時点で確立された製品またはシステムの構成です。たとえば、アプリケーションまたはオペレーティング システムのベースラインは、コンピューターまたはサービスを特定の状態に再構築する機能を提供します。

ベースラインは潜在的な問題を見つけて修正するための基盤となるだけでなく、社内でデプロイする必要のあるソフトウェア更新プログラムの数を削減し、コンプライアンスを監視する機能を強化することにより、ソフトウェア更新管理プロセスを簡略化します。

自社の最初の監査を実行した後で、その監査で得られた情報を使用して、運用環境内の IT コンポーネントのための運用ベースラインを定義する必要があります。運用環境にデプロイされている各種ハードウェアとソフトウェアに応じて、さまざまなベースラインが必要となる可能性があります。

たとえば一部のサーバーでは、Windows Server 2012 でシャットダウン プロセスに移行したときに処理が停止しないようにするためのソフトウェア更新プログラムが必要です。これらのサーバーのベースラインには、このソフトウェア更新プログラムを含める必要があります。

多くの場合、大規模な組織では、社内のコンピューターを資産のカテゴリに分割して、各カテゴリを標準ベースラインで保持するために同じバージョンのソフトウェアとソフトウェア更新プログラムを使用することが有用です。これらの資産カテゴリを使用して、ソフトウェア更新プログラムの配布に優先順位を付けることができます。

## 適切なソフトウェア更新プログラム通知サービスのサブスクライブ

社内で使用されているソフトウェアの最初の監査を実行後、ソフトウェア製品およびバージョンごとに新しいソフトウェア更新プログラムの通知を受け取るための最適な方法を決定する必要があります。電子メール通知、Web サイト、コンピューター パブリケーションなど、ソフトウェア製品に応じて最適な通知方法があります。

たとえば、Microsoft セキュリティ レスポンス センター (MSRC) は、Microsoft 製品に関するすべてのセキュリティ関連の問題に対応し、マイクロソフト セキュリティ情報サービス (新たに特定された脆弱性を伝える無料の電子メール通知) と、これらの脆弱性に対処するソフトウェア更新プログラムを提供します。<http://www.microsoft.com/technet/security/bulletin/notify.mspx> で、このサービスをサブスクライブできます。

## ソフトウェアの更新に関する注意点

社内で使用されているソフトウェアの最初の監査を実行後、ソフトウェア更新管理システムをセットアップするための要件を決定する必要があります。これは、使用しているソフトウェアの更新管理システムに依存します。WSUS については「[Windows Server Update Services を使用したベスト プラクティス](https://technet.microsoft.com/ja-JP/library/Cc708536)」、System Center については「[Configuration Manager でのソフトウェア更新プログラムの計画](https://technet.microsoft.com/ja-JP/library/gg712696)」を参照してください。

ただし、以下のセクションで示しているように、使用しているソリューションに関係なく適用できる一般的な考慮事項およびベスト プラクティスがあります。

### 環境のセットアップ

ソフトウェア更新管理環境をセットアップする計画を作成する場合は、次の方法を検討してください。

-   **安定した条件に基づいた、運用ソフトウェア更新プログラム コレクションの作成**: 一般的に、安定した条件を使用してソフトウェア更新プログラムのインベントリと配布のコレクションを作成すると、ソフトウェア更新管理プロセスのすべてのステージを簡略化できます。安定した条件には、インストールされたクライアント オペレーティング システムのバージョンと Service Pack レベル、システム ロール、またはターゲット組織を含めることができます。

-   **参照コンピューターを含む実稼働前のコレクションの作成**: 実稼働前のコレクションには、オペレーティング システムのバージョン、基幹業務ソフトウェア、社内で実行されているその他のソフトウェアの典型的な構成を含む必要があります。

ソフトウェア更新サーバーを設置する場所をクラウド内の Azure IaaS インフラストラクチャにするか、またはオンプレミスにするかについても検討する必要があります。オンプレミス リソースと Azure インフラストラクチャの間のトラフィックの量を評価する必要があるため、これは重要な決定です。オンプレミスのインフラストラクチャを Azure に接続する方法の詳細については、「[オンプレミス ネットワークを Microsoft Azure 仮想ネットワークに接続する](https://technet.microsoft.com/ja-JP/library/Dn786406.aspx)」を参照してください。

更新サーバーが設置される場所を決定する設計オプションは、現在のインフラストラクチャや現在使用しているソフトウェア更新システムによっても異なります。WSUS については「[Windows Server Update Services を組織に展開する](https://technet.microsoft.com/ja-JP/library/hh852340.aspx)」、System Center Configuration Manager については「[Configuration Manager のサイトと階層の計画](https://technet.microsoft.com/ja-JP/library/Gg712681.aspx)」を参照してください。

### Backup  


ソフトウェア更新管理のプラットフォームだけでなく、更新対象のサーバーにおいても定期的なバックアップは重要です。所定の[変更管理プロセス](https://technet.microsoft.com/ja-JP/library/cc543216.aspx)を持つ組織では、サーバーを更新する必要がある理由、推定ダウンタイム、および考えられる影響について IT 部門が正当性を示す必要があります。更新が失敗した場合のロールバック用構成を確保できるように、必ずシステムを定期的にバックアップしてください。

次のような Azure IaaS のバックアップ オプションがあります。

-   [Data Protection Manager を使用した Azure IaaS ワークロード保護](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Azure 仮想マシンのバックアップ](https://azure.microsoft.com/ja-JP/documentation/articles/backup-azure-vms/)

### Monitoring

承認された各ソフトウェア更新プログラムに対して定期的なレポートを実行し、適用されていない更新プログラムやインストール済みの更新プログラム、未完了状態の更新プログラムを監視する必要があります。同様に、まだ承認されていないソフトウェア更新プログラムのレポートを実行すると、デプロイを容易に決定できるようになります。

次のタスクも考慮する必要があります。

-   社内のすべてのコンピューターについて、適用可能およびインストール済みのセキュリティ更新プログラムの監査を実施する。

-   該当するコンピューターに対して更新プログラムを承認し、デプロイする。

-   社内のすべてのコンピューターのインベントリを追跡し、インストールの状態と進行状況を更新する。

この記事で説明してきた一般的な考慮事項に加えて、各製品のベスト プラクティスについても考慮する必要があります。たとえば、Azure の VM で SQL Server を保持している場合は、その製品のソフトウェア更新プログラムの推奨事項に従っていることを確認してください。

## 次のステップ

この記事で示されているガイドラインを使用して、Azure IaaS 内の仮想マシンに対してソフトウェア更新プログラムの最適なオプションを決定できます。ソフトウェア更新プログラムのベスト プラクティスについては、従来のデータセンター内と Azure IaaS 内で多数の類似点があります。したがって、現在のソフトウェア更新ポリシーを評価して、全体的なソフトウェア更新プロセスに Azure VM を含め、この記事内の関連するベスト プラクティスを含めることをお勧めします。

<!---HONumber=Sept15_HO2-->