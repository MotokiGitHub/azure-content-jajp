<properties
	pageTitle="FTP コネクタ"
	description="FTP コネクタの使用"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

#FTP コネクタ

##概要
FTP コネクタを使用すると、FTP サーバーとの間でデータを移動することができます。FTP コネクタの主な機能は、次のとおりです。

- 必要に応じて FTP サーバーからファイルを取得する
- 構成可能なスケジュールに基づいてポーリングを実行する
- FTP サーバーにポーリングし、FTP サーバー上の新しいドキュメントに基づいてロジック フローをトリガーする
- IP アドレス、ポート、パスワード、およびホスト名として FTP サーバーを指定する
- 必要に応じて送信を実行する
- 必要に応じて FTP サーバー上のファイルを削除する

##新しい FTP コネクタの作成
新しい FTP コネクタを作成するには、次の手順に従います。- Azure ポータルを起動します。- [+ 新規] (ポータル下部)、[Web + モバイル]、[Azure Marketplace] の順にクリックして、Azure Marketplace を開きます。

	![Launch Azure Marketplace][1]

- [API アプリ] をクリックします。
- "FTP" を検索し、[FTP コネクタ] を選択します。

	![Select FTP Connector][2]

- [作成] をクリックします。
- 表示された [FTP コネクタ] ブレードで、次のデータを入力します。

	![Create FTP Connector][3]

- **[場所]** - コネクタをデプロイする地域を選択します。
- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
- **[価格レベル]** - コネクタの価格レベルを選択します。
- **[名前]** - FTP コネクタの名前を指定します。
- **[パッケージの設定]**
	- **[サーバー アドレス]** - FTP サーバーの名前または IP アドレスを指定します。
	- **[ユーザー名]** - FTP サーバーへの接続に使用するユーザー名を指定します。
	- **[パスワード]** - FTP サーバーへの接続に使用するパスワードを指定します。
	- **[ルート フォルダー]** - ルート フォルダーのパスを指定します。
	- **[バイナリを使用]** - バイナリ転送モードを使用する場合は true、ASCII モードを使用する場合は false を指定します。
	- **[SSL を使用]** - セキュリティで保護された SSL/TLS チャネルで FTP を使用する場合は true を指定します。
	- **[サーバー ポート]** - FTP サーバーのポート番号を指定します。
- [作成] をクリックします。新しい FTP コネクタが作成されます。

##ロジック アプリでの FTP コネクタの使用
作成した FTP コネクタはフローから使用できます。

新しいフローを作成するには、[+ 新規]、[Web + モバイル]、[ロジック アプリ] の順に選択します。リソース グループを含むフローのメタデータを指定します。

![Create Logic App][4]

*[トリガーとアクション]* をクリックします。フローのデザイナーが開きます。

![Logic App empty flow designer][5]

FTP コネクタは、トリガーとアクションの両方として使用することができます。

###トリガー
空のフロー デザイナーで、右側のギャラリー ウィンドウの [FTP コネクタ] をクリックします。

![Choose FTP Trigger][6]

FTP コネクタには、"File Available (Read then Delete)" というトリガーが 1 つあります。このトリガーによって以下が実行されます。

- 新しいファイルのフォルダー パスをポーリングする
- 新しいファイルごとにロジック フローをインスタンス化する
- ロジック フローをインスタンス化した後、フォルダー パスからファイルを削除する

"File Available (Read then Delete)" トリガーをクリックします。

![Basic inputs FTP Trigger][7]

入力すると、スケジュールされた頻度でポーリングする特定のフォルダー パスを構成できます。基本的な入力内容は次のとおりです。- [頻度] - FTP ポーリングの頻度を指定します。- [間隔] - スケジュールした頻度の間隔を指定します。- [フォルダーのパス] - FTP サーバーのフォルダーのパスを指定します。- [ファイルの種類] - ファイルの種類がテキストであるかまたはバイナリであるかを指定します。

[...] をクリックすると、詳細な入力が表示されます。

![Basic inputs FTP Trigger][8]

詳細な入力は次のとおりです。- [ファイル マスク] - ポーリング時のファイル マスクを指定します。- [除外ファイル マスク] - ポーリング時に除外するファイル マスクを指定します。

入力し、チェック マークをクリックすると、入力構成は完了です。

![Basic inputs FTP Trigger][9]

構成した FTP トリガーには、出力パラメーターと入力パラメーターの両方が表示されています。

####後続のアクションでの FTP トリガーの出力の使用
FTP コネクタの出力は、フロー内の他のアクションの入力として使用できます。

アクションの入力ダイアログで、[...] をクリックし、ドロップダウン ボックスから FTP の出力を直接選択できます。

アクションの入力ボックスで直接式を記述することもできます。FTP トリガーの出力を参照するためのフロー式を次に示します。

	@triggers('ftpconnector').outputs.body.Content

###アクション
右側のウィンドウの [FTP コネクタ] をクリックします。FTP コネクタがサポートしているアクションの一覧が表示されます。

![List of FTP Actions][10]

FTP コネクタは、次の 4 つのアクションをサポートしています。サポートされるアクションは次のとおりです。

- **[ファイルの取得]** - 特定のファイルの内容を取得します。
- **[ファイルのアップロード]** - FTP フォルダー パスにファイルをアップロードします。
- **[ファイルの削除]** - FTP フォルダー パスからファイルを削除します。
- **[ファイルの一覧の取得]** - FTP フォルダー パスにあるすべてのファイルの一覧を取得します。

ここでは、1 つの例として [ファイルのアップロード] を使用します。[ファイルのアップロード] をクリックします。

最初に基本的な入力が表示されます。

![Basic inputs of Upload File action][11]


- **[コンテンツ]** - アップロードするファイルのコンテンツを指定します。
- **[コンテンツ転送エンコード]** - [なし] または [Base64] を指定します。
- **[ファイル パス]** - アップロードするファイルのファイル パスを指定します。

[...] をクリックすると、入力に関する詳細設定が表示されます。

![Basic inputs of Upload File action][12]


- **[存在する場合に追加]** - True または False に設定します。有効に設定すると、ファイルが存在する場合にファイルにデータが追加されます。無効に設定すると、ファイルが存在する場合にファイルは上書きされます。
- **[一時フォルダー]** - オプション。指定した場合、アダプターは、ファイルを "一時フォルダー パス" にアップロードします。アップロードが完了すると、ファイルは "フォルダー パス" に移動されます。移動操作がアトミックであるためには、"一時フォルダー パス" が "フォルダー パス" と同じ物理ディスクにある必要があります。一時フォルダーは、"存在する場合に追加" プロパティが無効の場合にのみ使用できます。

入力し、チェック マークをクリックすると、入力構成は完了です。

![Configured Upload File action][13]

[ファイル パス] のパラメーターは次のようになります。

	@concat('/Output/',triggers().outputs.body.FileName)

構成した FTP のファイルのアップロード アクションには、出力パラメーターと入力パラメーターの両方が示されています。

####FTP アクションへの入力として前のアクションの出力を使用する
構成済みのスクリーンショットでは、コンテンツは式に設定される値です。

	@triggers().outputs.body.Content


任意の値に設定できます。これは一例です。式はロジック アプリのトリガーの出力を取得し、アップロードするファイルのコンテンツとして使用します。前のアクションの出力を使用する場合は、変換を使います。この場合、式は次のようになります。

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=62-->