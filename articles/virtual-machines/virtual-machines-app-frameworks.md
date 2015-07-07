<properties
   pageTitle="アプリケーション フレームワーク"
   description="広く使われている重要なアプリケーション フレームワークを、テンプレートと Azure Resource Management (ARM) を使って作成する方法について説明します。LAMP スタック、Ruby on Rails、SharePoint、SQL Server などが対象となります。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="06/03/2015"
   ms.author="rasquill"/>

# テンプレートを使用したアプリケーション フレームワーク

仮想マシンは、テンプレートを使ってすばやく作成できます。

| テンプレート名 | 説明 | テンプレートを見る | 今すぐデプロイする |
|:---|:---|:---:|:---:|
| Active Directory フォレストと Active Directory ドメイン | このテンプレートは、新しい VM を 2 つ (新しい VNet、ストレージ アカウント、ロード バランサーと共に) デプロイし、AD フォレストと AD ドメインを新たに作成します。それぞれの VM は新しいドメインの DC として作成され、可用性セットに配置され、さらに、パブリックの負荷分散 IP アドレスで RDP エンドポイントが追加されます。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Apache Web サーバー | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して Apache Web サーバーをデプロイします。Ubuntu VM を作成し、Apache2 をインストールして、単純な HTML ファイルを作成します。| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/apache2-on-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapache2-on-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
| Couchbase クラスター | このテンプレートは、Couchbase クラスターを Ubuntu 仮想マシンにデプロイします。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/couchbase-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcouchbase-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Datastax クラスター | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して、Datastax クラスターを Ubuntu VM にインストールします。[詳細なチュートリアル。](virtual-machines-datastax-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Datastax Enterprise クラスター | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して、Datastax Enterprise クラスターを Ubuntu VM にインストールします。[詳細なチュートリアル。](virtual-machines-datastax-enterprise-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-enterprise) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-enterprise%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Django アプリ | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して、Django アプリケーションを Ubuntu VM にデプロイします。Python と Apache のサイレント インストールを実行します。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/django-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdjango-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Docker コンテナー | このテンプレートを使用すると、Docker (Docker 拡張機能) と 3 つの Docker コンテナー (DockerHub から直接取得され、Docker Compose を使ってデプロイされる) を使って Ubuntu VM をデプロイすることができます。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdocker-simple-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Elasticsearch クラスター | このテンプレートは、Elasticsearch クラスターを Ubuntu VM 上にデプロイし、テンプレート リンクを使用してデータ ノード スケール ユニットを作成します。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Felasticsearch%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Hortonworks HDP | このテンプレートは、CentOS 仮想マシン上に、マルチ サーバーの Hortonworks HDP 2.2 Apache Hadoop デプロイを作成し、クラスターに対して HDP インストールを構成します。| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/hortonworks-on-centos) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fhortonworks-on-centos%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>|
| Jenkins マスター ノードとスレーブ ノード | このテンプレートは、Ubuntu VM 上に Jenkins マスター ノードをデプロイし、さらに他の 2 つの VM 上に複数の Jenkin スレーブ ノードをデプロイします。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/jenkins-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fjenkins-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Kafka クラスター | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して、Kafka クラスターを Ubuntu VM にデプロイします。このテンプレートではさらに、パブリックにアクセスできる VM が 1 つ作成されます。この VM を起点として、Kafka ノードに ssh でアクセスし、診断やトラブルシューティングを行うことができます。| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/kafka-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%kafka-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Ubuntu 上の LAMP スタック | このテンプレートは、Azure Linux CustomScript 拡張機能を使用して LAMP アプリケーションをデプロイします。Ubuntu VM を作成し、MySQL、Apache、PHP のサイレント インストールを実行した後、単純な PHP スクリプトを作成します。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Flamp-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| MongoDB | このテンプレートは、Linux CustomScript 拡張機能を使用して、Ubuntu 仮想マシン上に Mongo DB をデプロイします。[詳細なチュートリアル。](virtual-machines-mongodb-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fmongodb-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Redis クラスター | このテンプレートは、Redis クラスターを Ubuntu 仮想マシンにデプロイします。このテンプレートではさらに、パブリックにアクセスできる VM が 1 つ作成されます。この VM を起点として、Redis ノードに ssh でアクセスし、診断やトラブルシューティングを行うことができます。[詳細なチュートリアル。](virtual-machines-redis-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/redis-high-availability) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fredis-high-availability%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Sharepoint ファーム | このテンプレートは、それぞれがパブリック IP アドレス、ロード バランサー、および VNet を持つ、3 つの新しい Azure VM を作成します。1 つ目の VM は新しいフォレストとドメインのための AD DC へと構成し、2 つ目の VM には SQL Server ドメインを結合し、3 つ目は Sharepoint ファームとサイトを使用して構成します。すべての VM がパブリック RDP を備えています。[詳細なチュートリアル。](virtual-machines-rmtemplate-sharepoint-walkthrough.md) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-three-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Spark クラスター | このテンプレートは、Spark クラスターを Ubuntu 仮想マシンにデプロイします。このテンプレートではさらに、パブリックにアクセスできる VM が 1 つ作成されます。この VM を起点として、Spark ノードに ssh でアクセスし、診断やトラブルシューティングを行うことができます。[詳細なチュートリアル。](virtual-machines-spark-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/spark-ubuntu-multidisks) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fspark-ubuntu-multidisks%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Tomcat と Open JDK のインストール | このテンプレートは、OpenJDK と Tomcat を Ubuntu VM にインストールします。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/openjdk-tomcat-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fopenjdk-tomcat-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| WordPress | このテンプレートは、完全な LAMP スタックを単一の Ubuntu VM にデプロイした後、WordPress をインストールして初期化します。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/wordpress-single-vm-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Zookeeper クラスター | このテンプレートは、3 ノードの Zookeeper クラスターを Ubuntu VM に作成します。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/zookeeper-cluster-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fzookeeper-cluster-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |

## 次のステップ

一般向けに公開されているテンプレートを [GitHub](https://github.com/Azure/azure-quickstart-templates) で探します。

Azure のリソース管理の詳細については、[こちら](../resource-group-template-deploy.md)を参照してください。
 

<!---HONumber=58_postMigration-->