﻿## <a name="what-is"> </a>BLOB ストレージとは

Azure BLOB ストレージは、テキストやバイナリ データなどの大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにする
サービスです。BLOB ストレージを使用すると、データを一般に公開することも、アプリケーション データを公開せずに格納することもできます。

BLOB ストレージの一般的な用途には、次のようなものがあります。

-   画像またはドキュメントをブラウザーに直接配信する
-   分散アクセス用にファイルを格納する
-   ビデオおよびオーディオをストリーミング配信する
-   セキュリティで保護されたバックアップおよび障害復旧を実行する
-   内部設置型サービスまたは Azure ホステッド サービスで分析するデータを
    格納する

## <a name="concepts"></a>概念

BLOB サービスには、次のコンポーネントが含まれます。

![Blob1][Blob1]

-   **ストレージ アカウント:** Azure ストレージへのアクセスはすべて
    ストレージ アカウントを介して行われます。ストレージ アカウントの容量の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/dn249410.aspx) 」を参照してください。

-   **コンテナー:** コンテナーは、BLOB のセットをグループ化します。
    すべての BLOB はコンテナーに格納されている必要があります。1 つのアカウントには、
    無制限の数のコンテナーを格納できます。また、1 つのコンテナーに保存できる
    BLOB の数も無制限です。

-   **BLOB:** 任意の種類およびサイズのファイルです。Azure Storage に格納できる BLOB には、
    ブロック BLOB とページ BLOB の 2 種類があります。
    ほとんどのファイルはブロック BLOB です。1 つのブロック BLOB には、最大で 200 GB の
    データを格納できます。このチュートリアルでは、ブロック BLOB を使用します。もう 1 つの種類の BLOB である ページ BLOB には、
    最大で 1 TB のデータを格納できます。
    ページ BLOB は、ファイル内のバイトが頻繁に変更される場合に効率的です。BLOB の詳細
    については、「[ブロック BLOB およびページ BLOB について][]」を参照してください。

-   **URL 形式:** BLOB は次の URL 形式で
    アドレス指定されます。   
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    次の例の URL を使用すると、上の図のいずれかの BLOB を
    アドレス指定できます。  
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/howto-blob-storage/blob1.jpg


<!--HONumber=42-->