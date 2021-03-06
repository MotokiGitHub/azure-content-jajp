<properties 
    pageTitle="チュートリアル: Azure Active Directory と ShiftPlanning の統合 | Microsoft Azure" 
    description="Azure Active Directory で ShiftPlanning を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と ShiftPlanning の統合
  
このチュートリアルの目的は、Azure と ShiftPlanning の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ShiftPlanning でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、ShiftPlanning に割り当てた Azure AD ユーザーは、ShiftPlanning 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ShiftPlanning のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "シナリオ")
##ShiftPlanning のアプリケーション統合の有効化
  
このセクションでは、ShiftPlanning のアプリケーション統合を有効にする方法を説明します。

###ShiftPlanning のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ShiftPlanning**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ShiftPlanning]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ShiftPlanning でAzure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **ShiftPlanning** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "シングル サインオンの構成")

2.  **[ユーザーの ShiftPlanning へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページで、**[ ShiftPlanning サインオン URL]** テキスト ボックスに、"**https://company.shiftplanning.com/includes/saml/*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "アプリケーション URL の構成")

4.  **[ShiftPlanning でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、**ShiftPlanning** 企業サイトに管理者としてログインします。

6.  上部のメニューで **[管理者]** をクリックします。

    ![管理者](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "管理者")

7.  **[統合]** の **[シングル サインオン]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "シングル サインオン")

8.  **[シングル サインオン]** セクションで、次の手順に従います。

    ![シングル サインオン](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "シングル サインオン")

    1.  **[SAML 有効]** を選択します。
    2.  **[Allow Password Login]** を選択します。
    3.  Azure ポータルで、**[ShiftPlanning でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[SAML Issuer URL]** ボックスに貼り付けます。
    4.  Azure ポータルで、**[ShiftPlanning でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[リモート ログアウト URL]** ボックスに貼り付けます。
    5.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 証明書]** テキスト ボックスに貼り付けます。
    7.  **[設定の保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが ShiftPlanning にログインできるようにするには、ユーザーを ShiftPlanning にプロビジョニングする必要があります。ShiftPlanning の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **ShiftPlanning** 企業サイトに管理者としてログインします。

2.  **[管理者]** をクリックします。

    ![管理者](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "管理者")

3.  **[Staff]** をクリックします。

    ![スタッフ](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "スタッフ")

4.  **[アクション]** の **[Add Employee]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "従業員の追加")

5.  **[Add Employees]** セクションで、次の手順に従います。

    ![従業員の保存](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "従業員の保存")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **[名]**、**[姓]**、**[電子メール]** を入力します。
    2.  **[Save Employees]** をクリックします。

>[AZURE.NOTE]ShiftPlanning から提供されている他の ShiftPlanning ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ShiftPlanning に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **ShiftPlanning ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。

    ![あり](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->