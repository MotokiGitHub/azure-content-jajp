<properties 
    pageTitle="Azure Automation でのソース管理の統合 | Microsoft Azure"
    description="この記事では、Azure Automation での GitHub とのソース管理の統合について説明します。"
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="11/04/2015"
    ms.author="sngun" />

# Azure Automation でのソース管理の統合

ソース管理の統合により、Automation アカウントの Runbook と PowerShell スクリプトを GitHub のソース管理リポジトリに関連付けることができます。その結果、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを簡単に実行できる環境を利用できるようになります。たとえば、ソース管理により、開発、テスト、または運用の Automation アカウントに異なるブランチを同期できるようになると、開発環境内でテストされたコードを運用環境に昇格するのに役立ちます。Azure Automation からソース管理にコードをプッシュしたり、ソース管理から Azure Automation に Runbook をプルしたりできます。この記事では、Azure Automation 環境内でソース管理を設定する方法について説明します。最初に GitHub リポジトリを作成した後、GitHub リポジトリにアクセスするよう Azure Automation を構成します。


>[AZURE.NOTE]ソース管理では、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) および [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) のプル操作とプッシュ操作がサポートされていますが、[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) はサポートされません。<br><br>


Automation アカウントのソース管理を構成するには、2 つの簡単な手順が必要です。ただし、既に GitHub アカウントを持っている場合、必要な手順は 1 つだけです。次に例を示します。
## 手順 1. GitHub リポジトリを作成する

GitHub アカウントと、Azure Automation にリンクするリポジトリが既にある場合は、既存のアカウントにログインし、次の手順 2. から開始してください。それ以外の場合は、[GitHub](https://github.com/) に移動し、新しいアカウントにサインアップして、[新しいリポジトリを作成](https://help.github.com/articles/create-a-repo/)します。


## 手順 2. ソース管理を設定する

* Azure プレビュー ポータルの [Automation アカウント] ブレードで、**[ソース管理の設定]** をクリックします。<br> ![Set Up Source Control](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
* **[ソース管理]** ブレードが開きます。ここでは、GitHub アカウントの詳細を構成できます。構成するパラメーターを次に示します。<br>

|パラメーター |説明 |
|:---|:---| 
|ソースの選択 | ソースを選択します。現時点では、**GitHub** のみがサポートされています。 |
|承認 | **[承認する]** ボタンをクリックすると、GitHub リポジトリへのアクセスが Azure Automation に許可されます。既に別のウィンドウで GitHub アカウントにログインしている場合は、そのアカウントの資格情報が使用されます。承認が成功すると、ブレードの **[承認プロパティ]** の下に GitHub のユーザー名が表示されます。 |
|リポジトリの選択 | 使用可能なリポジトリの一覧から GitHub リポジトリを選択します。 |
|ブランチの選択 | 使用可能なブランチの一覧からブランチを選択します。ブランチを作成したことがない場合は、**master** ブランチのみが表示されます。 |
|Runbook フォルダーのパス | Runbook フォルダーのパスは、コードをプッシュまたはプルする GitHub リポジトリのパスを指定します。これは、**/foldername/subfoldername** 形式で指定する必要があります。Runbook フォルダーのパスにあるスクリプトのみが Automation アカウントと同期されます。Runbook フォルダーのパスのサブフォルダーにあるスクリプトは同期**されません**。リポジトリですべての PowerShell スクリプトを同期するには、**/** を使います。 |


たとえば、**PowerShellScripts** という名前のリポジトリに **RootFolder** という名前のフォルダーがあり、このフォルダー内に **SubFolder** という名前のフォルダーがあるとします。同期する Runbook がすべてのフォルダーとリポジトリに含まれている場合は、次のようになります。

  1. **リポジトリ**から Runbook を同期するには、Runbook フォルダーのパスは */* となります。
  2. **RootFolder** から Runbook を同期するには、Runbook フォルダーのパスは */RootFolder* となります。
  3. **SubFolder** から Runbook を同期するには、Runbook フォルダーのパスは */RootFolder/SubFolder* となります。<br>

* 構成したパラメーターは、**[ソース管理の設定]** ブレードに表示されます。

![Configure Blade](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


* これで、ソース管理の統合は、お使いの Automation アカウント用に構成されたため、GitHub 情報を使用して更新する必要があります。この部分をクリックすると、ソース管理の同期ジョブの履歴がすべて表示されます。<br> ![Repository Values](media/automation-source-control-integration/automation_03_RepoValues.png)

* ソース管理の設定後、次の Automation リソースが Automation アカウントに作成されます。

2 つの[変数アセット](automation-variables.md)が作成されます。
      

  * 次に示すように、**Microsoft.Azure.Automation.SourceControl.Connection** 変数には、接続文字列の値が格納されます。<br>

|パラメーター |値 |
|:---|:---|
| 名前 | Microsoft.Azure.Automation.SourceControl.Connection |
| 型 | String |
| 値 | {"Branch":<*ブランチ名*>,"RunbookFolderPath":<*Runbook フォルダーのパス*>,"ProviderType":<*GitHub の場合は値 1*>,"Repository":<*リポジトリ名*>,"Username":<*GitHub ユーザー名*>} |


  * **Microsoft.Azure.Automation.SourceControl.OauthToken** 変数には、OAuthToken のセキュリティで保護され暗号化された値が格納されます。<br>

|パラメーター |値 |
|:---|:---|
| 名前 | Microsoft.Azure.Automation.SourceControl.OauthToken |
| 型 | Unknown(Encrypted) |
| 値 | <*暗号化された OAuthToken*> |

![変数](media/automation-source-control-integration/automation_04_Variables.png)

**Automation ソース管理**は、承認済みのアプリケーションとして GitHub アカウントに追加されます。アプリケーションを表示するには、GitHub のホーム ページから **[profile]**、**[Settings]**、**[Applications]** の順に移動します。このアプリケーションにより、Azure Automation は GitHub リポジトリを Automation アカウントに同期できます。

![Git Application](media/automation-source-control-integration/automation_05_GitApplication.png)


## Automation でのソース管理の使用


### Azure Automation からソース管理に Runbook をチェックインする

Runbook をチェックインすると、Runbook に対して行った変更をソース管理リポジトリにプッシュできます。Runbook のチェックイン手順は次のとおりです。

* Automation アカウントから、[テキスト形式の Runbook を新しく作成](automation-first-runbook-textual.md)するか、[テキスト形式の既存の Runbook を編集](automation-edit-textual-runbook.md)します。この Runbook は、PowerShell ワークフローまたは PowerShell スクリプト Runbook のどちらでもかまいません。  
* Runbook を編集して保存したら、**[編集]** ブレードの **[チェックイン]** をクリックします。![Checkin Button](media/automation-source-control-integration/automation_06_CheckinButton.png)


 >[AZURE.NOTE]Azure Automation からチェックインすると、ソース管理に現在存在しているコードが上書きされます。チェックインするための対応する Git コマンド ライン命令は、**git add + git commit + git push** です。

* **[チェックイン]** をクリックすると、メッセージが表示されるので、[はい] をクリックして続行します。![Checkin Message](media/automation-source-control-integration/automation_07_CheckinMessage.png)
* チェックインにより、**Sync-MicrosoftAzureAutomationAccountToGitHubV1** Runbook が開始されます。この Runbook は、GitHub に接続し、変更を Azure Automation からリポジトリにプッシュします。チェックイン ジョブの履歴を表示するには、**[ソース管理の統合]** タブに戻り、[リポジトリの同期] ブレードをクリックして開きます。このブレードには、すべてのソース管理ジョブが表示されます。表示するジョブを選択し、クリックして詳細を表示します。![Checkin Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
* チェックイン ジョブにエラーがある場合は、ジョブが中断状態になるため、ジョブ ブレードでエラーに関する詳細情報を表示できます。**[すべてのログ]** 部分には、該当するジョブに関連付けられた PowerShell ストリームがすべて表示されます。これにより、問題の解決に役立つ詳細情報が示されます。  
* 変更された Runbook の名前は、入力パラメーターとしてチェックイン Runbook に送信されます。**[リポジトリの同期]** ブレードで Runbook を展開すると、[ジョブの詳細を表示](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal)できます。![Checkin Input](media/automation-source-control-integration/automation_09_CheckinInput.png)
* ジョブが完了したら GitHub リポジトリを更新して、変更を表示します。リポジトリにコミットすると、"**Azure Automation で *Runbook 名*が更新されました**" というコミット メッセージが表示されます。  



### ソース管理から Azure Automation に Runbook を同期する 

[Runbook の同期] ブレードの [同期] ボタンを使用すると、リポジトリの Runbook フォルダーのパスにあるすべての Runbook を Automation アカウントにプルできます。同一リポジトリを複数の Automation アカウントに同期できます。Runbook の同期手順は次のとおりです。

* ソース管理を設定した Automation アカウントで、**[ソース管理の統合] の [リポジトリの同期] ブレード**を開き、**[同期]** をクリックします。その後、メッセージが表示されたら、**[はい]** をクリックして続行します。![Sync Button](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
* 同期により、**Sync-MicrosoftAzureAutomationAccountToGitHubV1** Runbook が開始されます。この Runbook は、GitHub に接続し、変更をリポジトリから Azure Automation にプルします。Runbook を表示するには、**[ソース管理の統合] の [リポジトリの同期]** ブレードに戻り、Runbook を展開します。![Sync Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
>[AZURE.NOTE]ソース管理から同期すると、ソース管理に現在含まれている**すべての** Runbook のうち、Automation アカウントに現在存在するドラフト バージョンの Runbook が上書きされます。同期するための対応する Git コマンド ライン命令は、**git pull** です。


## ソース管理の切断

GitHub アカウントから切断するには、[リポジトリの同期] ブレードを開き、**[切断]** をクリックします。ソース管理を切断すると、それまでに同期された Runbook はそのまま Automation アカウントに保持されますが、[リポジトリの同期] ブレードは有効になりません。![Disconnect Button](media/automation-source-control-integration/automation_12_Disconnect.png)



## 次のステップ

ソース管理の統合の詳細については、次のリソースを参照してください。 - [Azure Automation: Azure Automation でのソース管理の統合](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [お気に入りのソース管理システムに関するアンケート](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Azure Automation: Visual Studio Online を使用した Runbook ソース管理の統合](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=Nov15_HO3-->