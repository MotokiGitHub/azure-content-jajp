# Azure で MongoDB データベースを作成する方法

この記事では、[MongoLab][] を使用して [Azure ストア][]から MongoDB データベースを作成する方法について説明します。[MongoLab][] は、サービスとしての MongoDB プロバイダーであり、Azure データセンターで MongoDB データベースを実行および管理し、どのアプリケーションからもデータベースに接続できるようにしています。

[Azure ストア][]から MongoDB データベースを作成するには、以下の手順を実行します。

1.  [Azure 管理ポータル][]にログインします。
2.  ページの下部にある **[+新規]** をクリックし、**[ストア]** を選択します。

    ![ストアからアドオンを選択][]

3.  **[MongoLab\]** を選択し、フレームの下部にある矢印をクリックします。

    ![MongoLab を選択][]

4.  データベース名を入力してリージョンを選択し、フレームの下部にある矢印をクリックします。

    ![ストアから MongoLab データベースを購入][]

5.  チェックマークをクリックして購入を完了します。

    ![購入を確認して完了][]

6.  データベースが作成されたら、管理ポータルの **[アドオン]** タブから管理できます。

    ![Manage MongoLab database in Azure portal][]

7.  ページの下部にある **[接続文字列]** をクリックして、データベース接続情報を取得できます (上図を参照)。

    ![MongoLab 接続情報][]

  [MongoLab]: https://mongolab.com/home
  [Azure ストア]: /en-us/store/overview/
  [Azure 管理ポータル]: http://windows.azure.com/
  [ストアからアドオンを選択]: ./media/create-mongolab-mongodb/select-store.png
  [MongoLab を選択]: ./media/create-mongolab-mongodb/select-mongo-db.png
  [ストアから MongoLab データベースを購入]: ./media/create-mongolab-mongodb/purchase-mongodb.png
  [購入を確認して完了]: ./media/create-mongolab-mongodb/complete-mongolab-purchase.png
  [Manage MongoLab database in Azure portal]: ./media/create-mongolab-mongodb/manage-mongolab-add-on.png
  [MongoLab 接続情報]: ./media/create-mongolab-mongodb/mongolab-conn-info.png