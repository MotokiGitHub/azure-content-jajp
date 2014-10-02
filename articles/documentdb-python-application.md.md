<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong"></tags>

# <a name="_Toc395888515"></a><a name="_Toc395809324">DocumentDB を使用した Python Flask (MVC) Web アプリケーションの構築</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">概要</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">シナリオ</a>

このドキュメントでは、Azure DocumentDB を効果的に活用して、JSON ドキュメントの
保存とクエリを行う方法を取り上げます。投票の Web アプリケーションを
 Azure DocumentDB を使って構築するエンド ツー エンドのチュートリアルを紹介
しています。

チュートリアルでは、Azure に用意されている DocumentDB サービスを使用して、
Azure にホストされている Python Web アプリケーションからデータを保存したり
データにアクセスしたりする方法を説明します。Python と Azure Websites の
使用経験がある読者を想定しています。

学習内容:

1. DocumentDB アカウントを作成してプロビジョニングする

2. Python MVC アプリケーションを作成する

3. Web アプリケーションから Azure DocumentDB に接続して使用する

4. Web アプリケーションを Azure Websites にデプロイする

このチュートリアルの手順を実行すると、
アンケートに回答する単純な投票アプリケーションを作成できます。

![Alt text][]

<a name="_Toc395888520"></a><a name="_Toc395809329">前提条件</a>

この記事の手順を実行する前に、次のソフトウェアがインストール
されていることを確認してください。

Visual Studio 2013 (またはその無償版の
[Visual Studio Express][])

Python Tools for Visual Studio ([ここ][]から入手できます)

Azure SDK for Visual Studio 2013 バージョン 2.4 以降
([ここ][1]から入手できます)

Azure クロスプラットフォーム コマンド ライン ツール ([Microsoft
Web Platform Installer][] を通じて入手できます)

# <a name="_Toc395888519"></a><a name="_Toc395809328">DocumentDB データベース アカウントの作成</a>

DocumentDB データベース アカウントを Azure にプロビジョニングするには、
Azure の管理ポータルを開いて、ホームページにある Azure ギャラリー タイルをクリックするか、
または、画面の左下隅にある [+] をクリックします。

![Alt text][2]

これで Azure のギャラリーが開き、Azure に用意されているさまざまな
サービスを選択することができます。ギャラリーのカテゴリ一覧から
[データ、ストレージ、バックアップ] を選択します。

![Alt text][3]

その中から、Azure DocumentDB に該当するオプションを選択します。

![Alt text][4]

次に、画面の一番下にある [作成] を選択します。

![Alt text][5]

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、
リージョン、スケール、リソース グループなどの設定を指定することが
できます。

![Alt text][6]

アカウントに使用する値を指定した後、[作成] をクリックすると、
プロビジョニング プロセスが開始されて、データベース アカウントが作成されます。
プロビジョニング プロセスが完了すると、ポータルの通知領域に
その旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に
変化します。

![Alt text][7]

プロビジョニングの完了後、スタート画面から DocumentDB
タイルをクリックすると、新しく作成した DocumentDB アカウントの
メイン ブレードが表示されます。

![Alt text][8]
![Alt text][9]

[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、
それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。
これから作成する Web アプリケーションの中でそれらの値を使用します。

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">新しい Python Flask Web アプリケーションの作成</a>

Visual Studio を開き、[ファイル]、[新しいプロジェクト]、[Python]、
[Flask Web プロジェクト] の順に選択して、「**tutorial**」という名前を付けます。外部パッケージをインストールするかどうかを
たずねるメッセージが表示されます。[仮想環境にインストールする] をクリックし、
[作成] をクリックします。これで、プロジェクトに必要な Python
仮想環境が設定されます。

Flask は、Python で
Web アプリケーションをより迅速に作成するのに役立つ Web フレームワークです。[Flask のチュートリアルについては、ここをクリックしてください][]。

![Alt text][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">プロジェクトへの Flask パッケージの追加</a>

プロジェクトを設定した後は、プロジェクトに必要な特定の Flask
パッケージ (たとえば、フォーム) を追加する必要があります。**[env]** を右クリックし、
**次の Python パッケージをインストールします
(この順序を守ることが重要です)**。

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text][11]

**注:** まれに、出力ウィンドウにエラーが表示されることがあります。
その場合は、エラーがクリーンアップに関連しているかどうかを確認してください。クリーンアップには
失敗しても、インストールは正常に行われる場合があります
(出力ウィンドウを上方向へスクロールして確認してください)。
<a name="verify-the-virtual-environment"></a>その場合は続行して
かまいません。

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">仮想環境の確認</a>

すべてが正しくインストールされているかどうかを確認してみましょう。**F5** キーをクリックして、
Web サイトを起動します。Flask 開発サーバーが起動され、
Web ブラウザーが開きます。次のページが表示されます。

![Alt text][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">プロジェクトへの DocumentDB の追加</a>

DocumentDB Python SDK は、PyPi でホストされ、pip を使用してインストール
できます。

ソリューション エクスプローラーで、[Python 環境] ノードを展開します。
対象の環境を右クリックし、[Python パッケージのインストール] を選択します。

![Alt text][13]

PyPi パッケージの名前である "--pre pydocumentdb" を入力します。オプションで
既知のバージョンを入力することで、インストールするバージョンを管理できます。
バージョンを指定しない場合は、最新の安定バージョンが
インストールされます。"--pre pydocumentdb" のように名前全体を入力する
必要があることに注意してください。

![Alt text][14]

pydocumentdb パッケージがダウンロードされ、環境にインストールされます。
インストールが完了すると、環境の下にモジュールとして
"pydocumentdb" が表示されます。

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">データベース、コレクション、およびドキュメント定義の作成</a>

**forms.py** python ファイルを追加した後、このファイルを
ソリューション エクスプローラーの tutorial フォルダーに追加します。次のコードを forms.py に追加します。
これから、投票アプリケーションを作成していきます。それには、
ユーザー入力に基づいて切り替わる 3 つのブール型フィールドを作成します。


    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">データベース、コレクション、およびドキュメントの作成</a>

\#注: 認証資格情報はアプリ内よりも config ファイルに保持する方が
安全と考えられます。ここでは、簡略化のために、
この情報をソース コードに含めています。views.py 内に create という名前の新しい関数を作成します。
次のコードを **views.py** に追加します。既存のコードを削除しない
ように注意してください。


    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

# <a name="_Toc395888529"></a><a name="_Toc395809338">ユーザーによる投票の受け付けとドキュメントの更新</a>

### <a name="_Toc395809338">必要な import ステートメントの追加

# <a name="_Toc395888529"></a>

**views.py** の先頭に、次の import ステートメントを追加します。これらの
ステートメントにより、DocumentDB の PythonSDK および Flask パッケージがインポートされます。

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">データベース、コレクション、およびドキュメントを読み取る</a>

次のコードを **views.py** に追加します。このコードでは、フォームの設定と、
データベース、コレクション、およびドキュメントの読み取りを行っています。views.py 内の
既存のコードを削除しないように注意してください。単にこのコードを末尾に追加してください。

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document

### <a name="_Toc395809338">投票を登録し、ドキュメントを変更する</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">結果の表示</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')

### <a name="_Toc395809338">html ファイルの作成</a>

templates フォルダーの下に、create.html、results.html、
vote.html の各 html ファイルを追加します。

次のコードを **create.html** に追加します。このコードでは、
新しいデータベース、コレクション、およびドキュメントを作成したことを示すメッセージを処理しています。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

次のコードを **results.html** に追加します。このコードは、
投票の結果を表示します。

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

次のコードを **vote.html** に追加します。このコードは、
アンケートを表示し、投票を受け付けます。投票が登録されると、制御が views.py に
渡され、投票が行われたことが示され、ドキュメントが適宜登録
されます。

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}

# <a name="_Toc395888529"></a>

**layout.html** 内のコードを削除し、次のコードで置き換えます。
このコードでは、ナビゲーションバーと、対応するルーティング用の URL を設定しています。

index.html 内のコードを削除し、次のコードで置き換えます。これが、
アプリケーションのランディング ページとなります。

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">構成ファイルを追加し、\_\_init\_\_.py を変更する</a>

プロジェクト [tutorial] を右クリックし、**config.py** ファイルを追加します。
この config は、Flask のフォームで必要になります。これを使用して秘密キーを
指定することもできます。次のコードを config.py に追加します。

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

同様に、**\_\_init\_\_.py** ファイルを追加します。このファイルを
tutorial フォルダーの下に配置します。元のコードを次のコードで置き換えます。この
コードは、ビューと config.py ファイルの間の接続を処理します。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

上記の手順を実行した後、ソリューション エクスプローラーの表示は
次のようになります。

![Alt text][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">ローカルでのアプリケーションの実行</a>

Visual Studio で F5 キーを押すかまたは実行ボタンをクリックすると、
次のような画面が表示されます。

![Alt text][16]

[Vote] をクリックし、回答を選択します。

![Alt text][17]

回答を投票するごとに、該当するカウンターの値が増加します。次回
投票するときは結果が表示されます。

![Alt text][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Azure Websites へのアプリケーションのデプロイ</a>

以上で、DocumentDB と連携するアプリケーションが完成しました。
今度は、このアプリケーションを Azure Websites にデプロイしたいと思います。ソリューション エクスプローラーで
プロジェクトを右クリックし (ローカル実行したままになっていないことを
確認してください)、[発行] を選択します。

![Alt text][19]

資格情報を入力し、新しい Web サイトを作成するかまたは古い Web サイトを再利用して、
Azure Websites を構成します。

![Alt text][20]

数秒すると、Web アプリケーションの発行が完了し、
ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが
確認できます。

</h1>
# <a name="_Toc395809338">まとめ</a>

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を
使用した Python アプリケーションを作成し、Azure Websites に発行する方法を説明しました。

完全なソリューションは、[ここ][21]から入手できます (注: この場合も、
上に説明したように、仮想環境を追加し、Python ツールとパッケージを
インストールする必要があります)。

</h1>

  [Alt text]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [ここ]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [Flask のチュートリアルについては、ここをクリックしてください]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [21]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409