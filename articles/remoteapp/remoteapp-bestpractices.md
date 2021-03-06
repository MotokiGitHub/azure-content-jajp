<properties
    pageTitle="Azure ソリューションのベスト プラクティス"
    description="Azure RemoteApp を構成し、使用するためのベスト プラクティス"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2015" 
    ms.author="elizapo" />

# Azure RemoteApp を構成し、使用するためのベスト プラクティス

次の情報を使用して Azure RemoteApp を生産的に構成し、使用することができます。

## 接続


- 常に最新のクライアントのバージョンを使用します。以前のクライアントを使用すると、接続の問題およびその他のエクスペリエンスの低下が発生する可能性があります。デバイスのアプリケーションの自動更新を有効にすると、常に最新のクライアントがインストールされるようになります。
- 利用可能なインターネット接続のうち、最も安定性と信頼性が高い接続を常に使用します。  
- 最適な接続パフォーマンスのためにサポートされているプロキシ接続のみを使用します。SOCKS プロキシはサポートされていません。

## アプリケーション


- アプリケーションを終了する場合は、RemoteApp アプリケーションを保存して閉じます。アプリケーションを終了しないと、データが失われる可能性があります。
- Azure RemoteApp で使用する前にカスタム アプリケーションを検証します。これには、アプリケーションが複数のセッションのプラットフォームで動作し、同じコレクション内の別のユーザーに悪影響を与えるメモリや CPU などの不要なリソースが消費されないことを確認することが含まれます。詳細については、「[Application Compatibility Best Practices for Remote Desktop Services (リモート デスクトップ サービスのためのアプリケーションの互換性ベスト プラクティス)](http://www.microsoft.com/download/details.aspx?id=18704)」をダウンロードして確認してください。

## 構成と管理


- テンプレート イメージを最新に保ち、必要に応じて、ソフトウェア更新プログラムおよびその他の重要な修正プログラムをインストールします。これにより、Azure の RemoteApp は容量に適合するように自動スケールされ、に各インスタンスにはパッチが適用されます。  
- Active Directory フェデレーション サービス (AD FS) 展開が、セキュリティで保護され、信頼性が高いかどうかを確認します。そうしないと、クライアント認証に失敗し、ユーザーが Azure RemoteApp へアクセスができなくなります。
- テンプレート イメージをインストールされているアプリケーション、ロール、または機能がステートレスであるように構成します。これらは、継続状態である RemoteApp サービスの仮想マシンのいずれのインスタンスにも依存してはなりません。
	- ユーザー プロファイルまたは内部設置型ファイル共有または OneDrive などのその他のサービス外の保存先にすべてのユーザー データを格納します。
	- 内部設置型ファイル共有または OneDrive などのサービス外の保存先に共有データを格納します。
	- サービスの個々の仮想マシンではなく、テンプレート イメージにシステム全体のすべての設定を構成します。
	- 発行済みのアプリケーションのソフトウェアの自動更新を無効にします - 代わりにそれらをテンプレート イメージに手動で適用し、テンプレートから展開する前にテストします。
 

<!---HONumber=Oct15_HO3-->