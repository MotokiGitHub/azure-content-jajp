<properties 
   pageTitle="Azure Automation Webhook"
   description="HTTP 呼び出しから Azure Automation の Runbook を開始することをクライアントに許可する Webhook。この記事では、Webhook を作成する方法と、Webhook を呼び出して Runbook を開始する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/03/2015"
   ms.author="bwren" />

# Azure Automation Webhook

*Webhook* を使用することにより、単一の HTTP 要求を通して Azure Automation で特定の Runbook を開始することができます。これにより、Visual Studio Online、GitHub などの外部サービス、またはカスタム アプリケーションにおいて、Azure Automation API を使用した完全なソリューションを実装していなくても、Runbook を開始することができます。

![Webhook](media/automation-webhooks/webhooks-overview.png)

[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)で、Webhook と、Runbook を開始する他のメソッドを比較できます

## Webhook の詳細

次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | 説明 |
|:---|:---|
|名前 | Webhook に使用する任意の名前を指定できます。これはクライアントには公開されません。これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。<br>ベスト プラクティスとして、Webhook を使用するクライアントに関連した名前を Webhook に付ける必要があります。 |
|URL |Webhook の URL は、クライアントが Webhook にリンクされた Runbook を開始するために HTTP POST で呼び出す一意のアドレスです。これは、Webhook を作成するときに自動的に生成されます。カスタム URL を指定することはできません。<br> <br>この URL には、追加の認証なしで、サードパーティ製システムによる Runbook 呼び出しを可能にするためのセキュリティ トークンが含まれています。その理由で、これはパスワードと同じように扱う必要があります。セキュリティ上の理由から、Webhook の作成時に Azure プレビュー ポータルで表示できるのは URL だけです。将来の使用に備えて、URL を安全な場所にメモしてください。 |
|有効期限 | 証明書のように、各 Webhook には有効期限があり、それ以降は使用できなくなります。Webhook の作成後に、この有効期限を変更することはできません。また、有効期限に達した後に、Webhook を再度有効にすることもできません。この場合、別の Webhook を作成して現在の Webhook を置き換え、クライアントを更新して新しい Webhook を使用する必要があります。 |
| 有効 | 既定では、Webhook は作成時に有効になります。Disabled に設定した場合、クライアントはそれを使用できなくなります。**Enabled** プロパティは、Webhook の作成時、または作成後はいつでも設定できます。 |


### パラメーター
Webhook は、Runbook がその Webhook によって開始されたときに使用される Runbook のパラメーターの値を定義できます。Webhook には、Runbook の任意の必須パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なるパラメーター値を使用することができます。

>[AZURE.NOTE]現在 Webhook によって設定されているパラメーターの値は、Webhook の作成後は変更できません。別のパラメーターの値を使用する別の Webhook を作成する必要があります。

Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値を上書きできません。クライアントからのデータを受信するため、Runbook は、クライアントが POST 要求に含めるデータが入る、[object] 型の **$WebhookData** という 1 つのパラメーターを受け入れることができます。

![Webhookdata](media/automation-webhooks/webhookdata.png)

**$WebhookData** オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:---|
| WebhookName | Webhook の名前。 |
| RequestHeader | 受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| RequestBody | 受信 POST 要求の本文。文字列、JSON、XML、フォームのエンコード済みデータなどの書式設定が保持されます。想定されるデータ形式を操作するには、Runbook を記述する必要があります。|


**$WebhookData** パラメーターのサポートに必要な Webhook の構成はありません。また、これを受け入れるために Runbook は必要ありません。Runbook がパラメーターを定義していない場合は、クライアントから送信された要求の詳細は無視されます。

Webhook の作成時に $WebhookData の値を指定した場合、クライアントの要求本文にデータが含まれていなくても、クライアントの POST 要求からのデータで Webhook が Runbook を開始した時点でその値はオーバーライドされます。Webhook 以外の方法を使用して $WebhookData が含まれる Runbook を開始する場合、Runbook で認識される $Webhookdata の値を指定することができます。Runbook が正しく処理できるように、この値は $Webhookdata と同じプロパティを持つオブジェクトでなければなりません。

>[AZURE.NOTE]すべての入力パラメーターの値は、Runbook のジョブに記録されます。つまり、Webhook の要求でクライアントから提供された入力が記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。

## セキュリティ

Webhook のセキュリティは、呼び出しを許可するセキュリティ トークンが含まれる URL のプライバシーに依存します。正しい URL に対して要求がなされる場合、Azure Automation はその要求に対してどの認証も実行しません。このため、要求を検証する他の方法を使用しない場合は、機密性の高い機能を実行する Runbook で Webhook を使用しないでください。

Runbook 内にロジックを含め、$WebhookData パラメーターの **WebhookName** プロパティを調べることによって、Webhook で呼び出されたことを判別できます。Runbook は、**RequestHeader** または **RequestBody** プロパティの特定の情報を探すことによって、追加の検証を実行できます。

別の方法として、Webhook 要求の受信時に外部条件のいくつかの検証を Runbook に実行させることです。たとえば、GitHub リポジトリに新しいコミットがある場合は常に GitHub によって呼び出される Runbook について考えてみましょう。Runbook は、新しいコミットが実際に発生したばかりであることを検証するために、GitHub に接続してから、処理を続行することができます。

## Webhook の作成

次の手順を使用して、Runbook にリンクされた新しい Webhook を Azure プレビュー ポータルに作成します。

1. Azure プレビュー ポータルの **[Runbook] ブレード**から、Webhook がその詳細ブレードの表示を開始する Runbook をクリックします。 
3. ブレードの上部にある **Webhook** をクリックして、**[Webhook の追加]** ブレードを開きます。<br> ![Webhook ボタン](media/automation-webhooks/webhooks-button.png)
4. **[新しい Webhook の作成]** をクリックして、**[Webhook ブレードの作成]** を開きます。
5. Webhook の**名前**、**有効期限**、およびそれを有効にする必要があるかどうかを指定します。これらのプロパティの詳細については、「[Webhook の詳細](#details-of-a-webhook)」を参照してください。
6. コピー アイコンをクリックし、Ctrl + C キーを押して Webhook の URL をコピーします。次に、それを安全な場所に記録します。**Webhook を作成したら、再度 URL を取得することはできません。** <br> ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
3. **[パラメーター]** をクリックして、Runbook のパラメーターの値を指定します。Runbook に必須のパラメーターがある場合、値を指定しない限り Webhook を作成できません。
1. **[作成]** をクリックして Webhook を作成します。


## Webhook の使用

Webhook を作成後に使用する場合、クライアント アプリケーションは Webhook の URL で HTTP POST を発行する必要があります。Webhook の構文は、次の形式になります。

	http://<Webhook Server>/token?=<Token Value>

クライアントは、POST 要求から次のリターン コードの 1 つを受信します。

| コード | テキスト | 説明 |
|:---|:----|:---|
| 202 | 承認済み | 要求が承認され、Runbook が正常にキューに登録されました。 |
| 400 | 正しくない要求 | 次のいずれかの理由で要求が受け入れられませんでした。<ul> <li>Webhook の有効期限が切れている。</li> <li>Webhook は無効になっている。</li> <li>URL 内のトークンが無効である。</li> </ul>|
| 500 | 内部サーバー エラー | URL は有効ですが、エラーが発生しました。要求を再送信してください。 |

要求が成功したと仮定した場合、Webhook の応答には、次のような JSON 形式のジョブ ID が含まれています。ここに含まれるジョブ ID は 1 つですが、JSON 形式は将来の拡張にも対応します。

	{"JobIds":["<JobId>"]}  

Runbook ジョブの完了時期と Webhook からの完了状態は、クライアントで決定できません。この情報は、[Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) や [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163826.aspx) などの方法でジョブ ID を使用すると決定できます。

### 例

次の例では、Windows PowerShell を使用して Webhook で Runbook を開始します。HTTP 要求を実行できる言語では、Webhook を使用できます。ここでは、Windows PowerShell を例に取って使用します。

Runbook では、要求の本文に JSON 形式の仮想マシン一覧が必要です。この例ではほかにも、要求のヘッダーに Runbook を開始したユーザーや日時などの情報を含めます。

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


次の図に、この要求の ([Fiddler](http://www.telerik.com/fiddler) トレースを使用した) ヘッダー情報を示します。この中には、追加したカスタムの Date ヘッダーや From ヘッダー以外に、HTTP 要求の標準ヘッダーが含まれます。これらの値は、**WebhookData** の **RequestHeaders** プロパティで Runbook に対して使用できます。

![Webhook ボタン](media/automation-webhooks/webhook-request-headers.png)

次の図に、**WebhookData** の **RequestBody** プロパティで Runbook に対して利用できる ([Fiddler](http://www.telerik.com/fiddler) トレースを使用した) 要求の本文を示します。要求の本文に含まれていた形式が JSON であったため、この形式は JSON になります。

![Webhook ボタン](media/automation-webhooks/webhook-request-body.png)

次の図に、Windows PowerShell と結果の応答から送信される要求を示します。ジョブ ID は応答から抽出され、文字列に変換されます。

![Webhook ボタン](media/automation-webhooks/webhook-request-response.png)

次の例の Runbook では、前の例の要求を受け入れ、要求の本文で指定された仮想マシンを起動します。

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = (ConvertFrom-Json -InputObject $WebhookBody).VirtualMachines
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				Write-Output "Starting $VM.Name."
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}

	

## 関連記事:

- [Runbook の開始](automation-starting-a-runbook.md)
- [Runbook ジョブの状態の表示](automation-viewing-the-status-of-a-runbook-job.md) 

<!---HONumber=58_postMigration-->