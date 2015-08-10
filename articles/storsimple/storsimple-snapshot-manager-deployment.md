<properties 
   pageTitle="StorSimple Snapshot Manager のデプロイ | Microsoft Azure"
   description="StorSimple Snapshot Manager をダウンロードしてインストールする方法を説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# StorSimple Snapshot Manager のデプロイ

## 概要

StorSimple Snapshot Manager は、Microsoft Azure StorSimple 環境でのデータ保護とバックアップ管理を簡略化する Microsoft 管理コンソール (MMC) スナップインです。StorSimple Snapshot Manager を使用すれば、Microsoft Azure StorSimple をオンプレミスで管理し、完全に統合されたストレージ システムであるかのようにクラウド ストレージを管理できるため、バックアップと復元処理が簡略化され、コストを削減できます。

このチュートリアルでは構成要件に加え、StorSimple Snapshot Manager のインストール、削除、およびアップグレード手順についても説明します。

## StorSimple Snapshot Manager のインストール

StorSimple Snapshot Manager は、Windows Server® 2008 R2 SP1、Windows Server 2012、または Windows Server 2012 R2 オペレーティング システムを実行しているコンピューターにインストールできます。

>[AZURE.NOTE]Windows 2008 R2 を実行しているサーバーでは、Windows Server 2008 SP1 と Windows Management Framework 3.0 をインストールする必要もあります。

Microsoft 管理コンソール (MMC) 用に StorSimple Snapshot Manager スナップインをインストールまたはアップグレードする前に、Microsoft Azure StorSimple デバイスとホスト サーバーが正しく構成されていることを確認してください。

## 構成の前提条件

次の手順では、StorSimple Snapshot Manager をインストールする前に完了する必要がある構成タスクの概要を説明します。システム要件や詳細な手順の説明など、Microsoft Azure StorSimple の構成とセットアップに関する完全な情報については、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)」をご覧ください。

>[AZURE.IMPORTANT]開始する前に、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)
> 」にある「[インストール前のチェックリスト](storsimple-deployment-walkthrough.md#pre-installation-checklist)」と「[デプロイメントの前提条件](storsimple-deployment-walkthrough.md#deployment-prerequisites)」を確認してください。
<br>
 
### StorSimple Snapshot Manager をインストールする前に

1. 「[StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md)」または「[StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)」で説明されているように、Microsoft Azure StorSimple デバイスを開梱、マウント、および接続します。

2. ホスト コンピューターで、次のいずれかのオペレーティング システムが実行されていることを確認します。

    - Windows Server 2008 R2 (Windows 2008 R2 を実行しているサーバーでは、Windows Server 2008 SP1 および Windows Management Framework 3.0 もインストールする必要があります)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    > [AZURE.NOTE]StorSimple 仮想デバイスの場合、ホストは Microsoft Azure Virtual Machine である必要があります。

3. すべての Microsoft Azure StorSimple 構成要件を満たしていることを確認します。詳細については、「[デプロイメントの前提条件](storsimple-deployment-walkthrough.md#deployment-prerequisites)」を確認してください。

4. ホストにデバイスを接続し、初期構成を実行します。詳細については、「[デプロイメントの手順](storsimple-deployment-walkthrough.md#deployment-steps)」を確認してください。

5. ボリュームをデバイスに作成してホストを割り当ててから、ホストがボリュームをマウントし、使用できることを確認します。StorSimple Snapshot Manager では、次のタイプのボリュームがサポートされています。

    - 基本ボリューム
    - シンプル ボリューム
    - 動的ボリューム
    - ミラー化された動的ボリューム (RAID 1)
    - クラスターの共有ボリューム
 
    StorSimple デバイスまたは StorSimple 仮想デバイスにボリュームを作成する方法については、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)」の「手順 6: ボリュームを作成する」を確認してください。

## StorSimple Snapshot Manager の新規インストール

StorSimple Snapshot Manager をインストールする前に、StorSimple デバイスまたは StorSimple 仮想デバイスに作成したボリュームが、「[構成の前提条件](#configure-prerequisites)」に説明されているようにマウント、初期化、およびフォーマットされていることを確認します。

>[AZURE.IMPORTANT]
>
>- StorSimple 仮想デバイスの場合、ホストは Microsoft Azure Virtual Machine である必要があります。 
>
>- ホストは、Windows 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を実行している必要があります。サーバーが Windows Server 2008 R2 を実行している場合、Windows Server 2008 SP1 と Windows Management Framework 3.0 もインストールする必要があります。
>
>- デバイスを StorSimple Snapshot Manager に接続するには、ホストから StorSimple デバイスに iSCSI 接続を構成する必要があります。

次の手順に従って、StorSimple Snapshot Manager のフレッシュ インストールを実行します。アップグレードをインストールする場合は、「[StorSimple Snapshot Manager のアップグレードまたは再インストール](#upgrade-or-reinstall-storsimple-snapshot-manager)」をご覧ください。

- 手順 1: StorSimple Snapshot Manager をインストールする 
- 手順 2: StorSimple Snapshot Manager をデバイスに接続する 
- 手順 3: デバイスへの接続を確認する 

###手順 1: StorSimple Snapshot Manager をインストールする

StorSimple Snapshot Manager をインストールするには、以下の手順を実行します。

#### StorSimple Snapshot Manager をインストールするには

1. StorSimple Snapshot Manager ソフトウェアをダウンロードして (Microsoft ダウンロード センターの [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) にアクセス)、ローカルのホストに保存します。

2. エクスプローラーで圧縮ファイルを右クリックし、**[すべて展開する]** をクリックします。

3. **[圧縮 (ZIP 形式) フォルダーの展開]** ウィンドウの **[展開先の選択とファイルの展開]** ボックスに、ファイルの展開先にするパスを入力するか、参照して指定します。

      >[AZURE.IMPORTANT]StorSimple Snapshot Manager は C: ドライブにインストールする必要があります。
 
4. **[完了時に展開されたファイルを表示する]** チェック ボックスを選択し、**[抽出]** をクリックします。

    ![ファイル展開ダイアログ ボックス](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. 展開が完了したら、展開先フォルダーが開きます。展開先フォルダーに表示されるアプリケーションのセットアップ アイコンをダブルクリックします。

5. **[セットアップ完了]** メッセージが表示されたら、**[閉じる]** をクリックします。デスクトップに StorSimple Snapshot Manager のアイコンが表示されます。

    ![デスクトップ アイコン](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### 手順 2: StorSimple Snapshot Manager をデバイスに接続する

次の手順に従って、StorSimple Snapshot Manager を StorSimple デバイスに接続します。

#### StorSimple Snapshot Manager をデバイスに接続するには

1. デスクトップの StorSimple Snapshot Manager のアイコンをクリックします。[StorSimple Snapshot Manager] ウィンドウが表示されます。このウィンドウには、**[スコープ]** ウィンドウ、**[結果]** ウィンドウ、および **[操作]** ウィンドウがあります。 

    ![StorSimple Snapshot Manager のユーザー インターフェイス](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - **[スコープ]** ウィンドウ (左ウィンドウ) には、ノードのリストがツリー構造で編成されて表示されます。一部のノードを展開すると、そのノードに関連するビューまたは特定のデータを選択できます。ノードを展開または折りたたむには、矢印アイコンをクリックします。**[スコープ]** ウィンドウの項目を右クリックすると、その項目に使用可能な操作のリストが表示されます。 

    - **[結果]** ウィンドウ (中央ウィンドウ) には、**[スコープ]** ウィンドウで選択したノード、ビュー、またはデータに関する詳細な状態情報が表示されます。

    - **[操作]** ウィンドウには、**[スコープ]** ウィンドウで選択したノード、ビュー、またはデータに実行できる操作がリストされます。

    StorSimple Snapshot Manager ユーザー インターフェイスの完全な説明については、「[StorSimple Snapshot Manager のユーザー インターフェイス](storsimple-use-snapshot-manager.md)」をご覧ください。

2. **[スコープ]** ウィンドウで **[デバイス]** ノードを右クリックし、**[デバイスの構成]** をクリックします。**[デバイスの構成]** ダイアログ ボックスが表示されます。

    ![デバイスの構成](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. **[デバイス]** ボックスの一覧で、Microsoft Azure StorSimple デバイスまたは仮想デバイスの IP アドレスを選択します。**[パスワード]** ボックスに、Microsoft Azure 管理ポータルでデバイス用に作成した StorSimple Snapshot Manager パスワードを入力します。**[OK]** をクリックします。

4. 指定したデバイスが StorSimple Snapshot Manager によって検索されます。指定したデバイスが取得されると、StorSimple Snapshot Manager によって接続が追加されます。[デバイスへの接続を検証](#to-verify-the-connection)して、接続が正常に追加されたことを確認できます。

    何かの理由でデバイスを取得できなかった場合、StorSimple Snapshot Manager によってエラー メッセージが返されます。**[OK]** をクリックしてエラー メッセージを閉じ、**[キャンセル]** をクリックして **[デバイスの構成]** ダイアログ ボックスを閉じます。

5. ボリューム グループに関連するバックアップがある場合、StorSimple Snapshot Manager はデバイスに接続されると、そのデバイス用に構成された各ボリューム グループをインポートします。関連付けられているバックアップがないボリューム グループはインポートされません。また、ボリューム グループに対して作成されたバックアップ ポリシーはインポートされません。インポートされたグループを表示するには、**[スコープ]** ウィンドウの最上部にある **[ボリューム グループ]** ノードを右クリックし、**[インポートされたグループを切り替え]** をクリックします。

### 手順 3: デバイスへの接続を確認する

次の手順に従って、StorSimple Snapshot Manager が StorSimple デバイスに接続されていることを確認します。

#### 接続を確認するには

1. **[スコープ]** ウィンドウで、**[デバイス]** ノードをクリックします。

    ![StorSimple Snapshot Manager デバイスの状態](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. 次を参考にして、**[結果]** ウィンドウを確認します。

   - デバイス アイコンに緑色のインジケーターが表示され、**[状態]** 列に **[使用可能]** と表示されている場合、デバイスは接続されています。 

   - デバイス アイコンに赤色のインジケーターが表示され、**[状態]** 列に [使用不可] と表示されている場合、デバイスは接続されていません。

   - **[状態]** 列に **[更新しています]** と表示されている場合、StorSimple Snapshot Manager は、接続されたデバイスのボリューム グループと関連するバックアップを取得中です。

## StorSimple Snapshot Manager のアップグレードまたは再インストール

StorSimple Snapshot Manager をアップグレードまたは再インストールするには、このソフトウェアを完全にアンインストールする必要があります。

StorSimple Snapshot Manager を再インストールする前に、ホスト コンピューターで既存の StorSimple Snapshot Manager データベースをバックアップします。これにより、バックアップ ポリシーと構成情報が保存されるため、バックアップからこのデータを簡単に復元できます。

StorSimple Snapshot Manager をアップグレードまたは再インストールするには、以下の手順を実行します。

- 手順 1: StorSimple Snapshot Manager をアンインストールする 
- 手順 2: StorSimple Snapshot Manager データベースをバックアップする 
- 手順 3: StorSimple Snapshot Manager を再インストールし、データベースを復元する 

### 手順 1: StorSimple Snapshot Manager をアンインストールする

StorSimple Snapshot Manager をアンインストールするには、以下の手順を実行します。

#### StorSimple Snapshot Manager をアンインストールするには

1. ホスト コンピューターで **[コントロール パネル]** を開き、 **[プログラム]**、**[プログラムと機能]** の順にクリックします。

2. 左側のウィンドウで、**[プログラムのアンインストールまたは変更]** をクリックします。

3. **[StorSimple Snapshot Manager]** を右クリックし、**[アンインストール]** をクリックします。

4. これにより、StorSimple Snapshot Manager のセットアップ プログラムが開始します。**[セットアップの変更]** をクリックし、**[アンインストール]** をクリックします。

    >[AZURE.NOTE]StorSimple Snapshot Manager や ディスクの管理など、バック グラウンドで実行されている MMC プロセスがある場合、アンインストールは失敗し、プログラムのアンインストールを試行する前に MMC のすべてのインスタンスを閉じるように求めるメッセージが表示されます。**[セットアップの完了後、アプリケーションを自動的に終了して、再起動する]** を選択し、**[OK]** をクリックします。
 
5. アンインストール プロセスが完了したら、**[セットアップ完了]** メッセージが表示されます。**[閉じる]** をクリックします。

### 手順 2: StorSimple Snapshot Manager データベースをバックアップする

StorSimple Snapshot Manager データベースのコピーを作成して保存するには、以下の手順を実行します。

#### データベースをバックアップするには

1. Microsoft StorSimple Management Service を停止します。

   1. Server Manager を起動します。

   2. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

   3. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。

   4. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスを停止]** をクリックします。

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。

    >[AZURE.NOTE]ProgramData は隠しフォルダーです。

3. カタログ XML ファイルを検索し、ファイルをコピーして、安全な場所またはクラウドにコピーを保存します。

    ![StorSimple バックアップ カタログ ファイル](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Microsoft StorSimple Management Service を再起動します。

    1. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

    2. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。

    3. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスを再起動]** をクリックします。

### 手順 3: StorSimple Snapshot Manager を再インストールし、データベースを復元する

StorSimple Snapshot Manager を再インストールするには、「[StorSimple Snapshot Manager の新規インストール](#install-a-new-storsimple-snapshot-manager)」の手順に従います。次に、以下の手順に従って、StorSimple Snapshot Manager データベースを復元します。

#### データベースを復元するには

1. Microsoft StorSimple Management Service を停止します。

    1. Server Manager を起動します。

    2. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

    3. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。

    4. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスを停止]** をクリックします。

2. C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。

     >[AZURE.NOTE]ProgramData は隠しフォルダーです。

3. カタログ XML ファイルを削除し、以前に保存したバージョンで置き換えます。

4. Microsoft StorSimple Management Service を再起動します。

    1. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

    2. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。

    3. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスを再起動]** をクリックします。

## 次のステップ

StorSimple Snapshot Manager の詳細については、「[StorSimple Snapshot Manager について](storsimple-what-is-snapshot-manager.md)」をご覧ください。

StorSimple Snapshot Manager ユーザー インターフェイスの詳細については、「[StorSimple Snapshot Manager のユーザー インターフェイス](storsimple-use-snapshot-manager.md)」をご覧ください。

<!------HONumber=July15_HO4-->