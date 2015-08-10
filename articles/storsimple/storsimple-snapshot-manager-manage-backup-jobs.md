<properties 
   pageTitle="StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理 |Microsoft Azure"
   description="スケジュールされたバックアップ ジョブ、現在実行中のバックアップ ジョブ、および完了したバックアップ ジョブの表示し、管理するためのStorSimple Snapshot Manager MMC スナップインの使用方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />


# StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理

## 概要

**[スコープ]** ウィンドウ内の **[ジョブ]** ノードは、対話的に、または構成済みポリシーに従って開始した **[スケジュール済み]**、**[過去 24 時間以内]**、および **[実行中]** のバクアップ タスクを表示します。

このチュートリアルでは、スケジュール済みのバックアップ ジョブ、最近のバックアップ ジョブ、および現在実行中のバックアップ ジョブに関する情報を表示するための **[ジョブ]** ノードの使用方法について説明します (ジョブとこれに対応する情報の一覧は、**[結果]** ウィンドウに表示されます)。 さらに、表示されているジョブを右クリックすると、使用可能な操作を一覧で示すコンテキスト メニューを表示できます。

## スケジュールされたジョブの表示

スケジュールされたバックアップ ジョブを表示するには、以下の手順を実行します。

#### スケジュールされたジョブを表示するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。 

2. **[スコープ**] ウィンドウで、**[ジョブ]** ノードを展開し、**[スケジュール済み]** をクリックします。次の情報が **[結果]** ウィンドウに表示されます。

    - **[名前]** – スケジュールされたスナップショットの名前

    - **[次の実行]** – 次回スケジュールされているスナップショットの日時

    - **[最後の実行]** – スケジュールされている最近のスナップショットの日時

    >[AZURE.NOTE]1 回限りのみのスナップショットの場合、**[次の実行]** と **[最後の実行]** は同じになります。
 
    ![スケジュールされたバックアップ ジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png)
 
3. 特定のジョブで追加の操作を実行するには、**[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。

## 最近のジョブの表示

バックアップを表示し、過去 24 時間以内に完了したジョブを復元するには、以下の手順を実行します。

#### 最近のジョブを表示するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ**] ウィンドウで、**[ジョブ]** ノードを展開し、**[過去 24 時間以内]** をクリックします。**[結果]** ウィンドウに、過去 24 時間以内のバックアップ ジョブが表示されます (最大 64 のジョブ)。**[表示]** オプションの指定に応じて、次の情報が **[結果]** ウィンドウに表示されます。

    - **[名前]** – スケジュールされたスナップショットの名前。
 
    - **[開始]** – スナップショットを開始した日時。

    - **[停止]** – スナップショットが修了または停止した日時。

    - **[経過時間]** – **[開始]** 日時と **[停止]** 日時の間の時間。

    - **[状態]** – 最近完了したジョブの状態。**[成功]** は、バックアップが正常に作成されたことを示します。**[失敗]** は、ジョブが正常に実行されなかったことを示します。

    - **[情報]** – 失敗の理由。

    - **[処理バイト数 (MB)]** – 処理されたボリューム グループのデータ量 (MB)。

    ![過去 24 時間に実行したジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png)

3. 特定のジョブで追加の操作を実行するには、**[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。

    ![ジョブを削除する](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)
     
## 現在実行中のジョブの表示

現在実行中のジョブを表示するには、以下の手順を実行します。

#### 現在実行中のジョブを表示するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで、**[ジョブ]** ノードを展開し、**[実行中]** をクリックします。指定した **[表示]** オプションに応じて、次の情報が **[結果]** ウィンドウに表示されます。

    - **[名前]** – スケジュールされたスナップショットの名前。

    - **[開始]** – スナップショットを開始した日時。

    - **[チェックポイント]** – バックアップの現在の操作。

    - **[状態]** – 完了の割合。
    
    - **[経過時間]** – バックアップの開始以降に経過した時間。

    - **[平均スループット (MB)]** – 配信されるデータ量の中央値で、メガバイト (MB) で表されます。

    - **[処理バイト数 (MB)]** – 処理されたボリューム グループのデータ量 (MB)。

    - **[書き込みバイト数 (MB)]** – バックアップに書き込まれたデータ量 (MB)。

    ![現在実行中のジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. 特定のジョブで追加の操作を実行するには、**[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。

## 次のステップ

「[Use StorSimple Snapshot Manager to manage the backup catalog (StorSimple Snapshot Manager を使用したバックアップ カタログの管理)](storsimple-snapshot-manager-manage-backup-catalog.md)」















            


 

<!---HONumber=July15_HO5-->