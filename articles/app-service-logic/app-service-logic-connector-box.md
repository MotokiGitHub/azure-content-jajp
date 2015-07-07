<properties
   pageTitle="ロジック アプリでの Box コネクタの使用"
   description="ロジック アプリでの Box コネクタの使用"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# ロジック アプリでの Box コネクタの使用



ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。このシナリオでは、ファイアウォールの外側にいる人ともデータを安全に共有できる Box を操作する必要がある場合を想定しています。



Box のギャラリー アプリでは、Box を操作するためのしくみとしてアクションを提供します。



1. **アクション**: アクションにより、ロジック アプリに構成済みの Box アカウントで事前に定義されたアクションを実行することができます。Box コネクタを使用して Box アカウントで実行できるアクションを次に示します。

	a.*ファイルの一覧の取得*: この操作により、フォルダー内のすべてのファイルの情報が返されます。アクションに必要なパラメーターの一覧を次に示します。

	<table>
  <tr>
    <td><b>パラメーター名</b></td>
    <td><b>説明</b></td>
    <td><b>必須</b></td>
  </tr>
  <tr>
    <td>フォルダー パス</td>
    <td>リスト化が行われるフォルダーのパス。</td>
    <td>あり</td>
  </tr>
</table>[AZURE.NOTE]ファイル コンテンツは返されません。



    b.*ファイルの取得*: この操作により、コンテンツとプロパティを含むファイルを取得します。アクションに必要なパラメーターの一覧を次に示します。

	<table>
  <tr>
    <td><b>パラメーター名</b></td>
    <td><b>説明</b></td>
    <td><b>必須</b></td>
  </tr>
  <tr>
    <td>ファイル パス</td>
    <td>ファイルが存在するフォルダーのパス。</td>
    <td>あり</td>
  </tr>
  <tr>
    <td>ファイルの種類</td>
    <td>ファイルがテキストかバイナリかを指定します。</td>
    <td>いいえ</td>
  </tr>
</table>[AZURE.NOTE]この操作では、ファイルの読み取り後にファイルは削除されません。



    c.ファイルのアップロード: 名前が示すとおり、Box アカウントにファイルをアップロードするアクションです。ファイルが既に存在する場合、上書きはされず、エラーがスローされます。アクションに必要なパラメーターの一覧を次に示します。

	<table>
  <tr>
    <td><b>パラメーター名</b></td>
    <td><b>説明</b></td>
    <td><b>必須</b></td>
  </tr>
  <tr>
    <td>ファイル パス</td>
    <td>ファイルへのパス。</td>
    <td>あり</td>
  </tr>
  <tr>
    <td>ファイル コンテンツ</td>
    <td>アップロードされるファイルのコンテンツ。</td>
    <td>あり</td>
  </tr>
  <tr>
    <td>コンテンツ転送エンコード</td>
    <td>コンテンツのエンコードの種類。[Base64] または [なし]。</td>
    <td> </td>
  </tr>
</table>d.ファイルの削除: フォルダーから指定したファイルを削除します。ファイルまたはフォルダーが見つからない場合は、例外がスローされます。アクションに必要なパラメーターの一覧を次に示します。

 	<table>
  <tr>
    <td><b>パラメーター名</b></td>
    <td><b>説明</b></td>
    <td><b>必須</b></td>
  </tr>
  <tr>
    <td>ファイル パス</td>
    <td>フォルダーを含むファイルのパスを入力します。</td>
    <td>あり</td>
  </tr>
</table>




## ロジック アプリの Box コネクタの作成

Box コネクタを使用するにはまず、Box コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリ デザイナーの中からでも外からでも実行できます。デザイナーの外から作成する場合の手順は次のとおりです。

1. Azure ポータルのホーム ページから Azure Marketplace を開きます。

2. [すべて] の下で "Box コネクタ" を検索します。

3. Box コネクタを構成して、[作成] をクリックします。

	![][1]

4. 作成したら、Box コネクタを使用するために同じリソース グループにロジック アプリを作成できます。


## ロジック アプリでの Box コネクタの使用

API アプリを作成したら、ロジック アプリのアクションとして Box コネクタを使用できます。そのためには、次の手順を実行する必要があります。


1. 新しいロジック アプリを作成し、Box コネクタと同じリソース グループを選択します。

2. [トリガーとアクション] を開き、Logic Apps デザイナーを開いてフローを構成します。Box コネクタが右側のギャラリーの [最近使用した項目] セクションに表示されます。それを選択します。

3. ロジック アプリの開始時に Box コネクタを選択した場合、コネクタを使用して Box アカウントにアクションを実行するトリガーのように動作します。この記事の執筆時点では、Box コネクタにトリガーはありません。

4. 最初の手順では、ユーザーの代わりに操作を実行するロジック アプリの認証および承認を行います。承認を開始するには、Box コネクタで [承認] をクリックします。

	![][2]

5. [承認] をクリックすると、Box の承認ダイアログが開きます。操作を実行する Box アカウントのログインの詳細情報を入力します。

	![][3]

6. 代わりに操作を行わせるために、アカウントにロジック アプリのアクセス権を付与します。

	![][4]

7. アクションの一覧が表示され、実行する適切な操作を選択できます。

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=62-->