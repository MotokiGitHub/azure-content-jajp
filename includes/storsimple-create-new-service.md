<properties 
   pageTitle="新しい StorSimple Manager サービスを作成する"
   description="StorSimple Manager サービスの新しいインスタンスを作成する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/28/2015"
   ms.author="v-sharos" />


### 新しいサービスを作成するには

1. Microsoft アカウントの資格情報を使用して、Microsoft Azure の管理ポータル \([Azure の管理ポータル](https://manage.windowsazure.com/)\) にログオンします。

2. 管理ポータルで、**\[新規\]**、**\[データ サービス\]**、**\[StorSimple Manager\]**、**\[簡易作成\]** の順にクリックします。

3. 表示されるフォームで、次の手順を実行します。
  1. サービスの一意の **\[名前\]** を指定します。これは、サービスの識別に使用できるフレンドリ名です。名前の長さは 2 ～ 50 文字とし、文字、数字、ハイフンを含めることができます。名前の最初と最後は、文字か数字とする必要があります。
  2. サービスの **\[場所\]** を指定します。場所は、デバイスをデプロイする地理的領域です。
  3. **\[サブスクリプション\]** ボックスの一覧で、サブスクリプションを選択します。サブスクリプションは、課金アカウントにリンクされます。このフィールドは、保有するサブスクリプションが 1 つだけの場合は表示されません。
  4. **\[新しいストレージ アカウントを作成する\]** をオンにすると、サービスの作成時にストレージ アカウントが自動的に作成されます。このストレージ アカウントは、"storsimplebwv8c6dcnf" などの特別な名前になります。
  5. **\[StorSimple Manager の作成\]** をクリックしてサービスを作成します。

       ![サービスの作成](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

     **\[サービス\]** ランディング ページが表示されます。サービスの作成には数分かかります。サービスが正常に作成されると、適宜、通知が表示され、サービスの状態が **"アクティブ"** に変わります。
 
       ![サービスの作成](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

<!--HONumber=52-->