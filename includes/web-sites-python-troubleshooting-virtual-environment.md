﻿デプロイメント スクリプトで、互換性のある仮想環境が既に存在することが検出された場合、Azure での仮想環境の作成はスキップされます。これにより、デプロイメントが大幅に高速化されます。既にインストールされているパッケージは pip でスキップされます。

特定の状況では、既存の仮想環境を強制的に削除することが必要になる場合があります。これが必要になる場合として、仮想環境をリポジトリの一部に組み込むことを決定した場合があります。また、特定のパッケージを削除する場合や、requirements.txt に対する変更をテストする場合があります。

Azure には既存の仮想環境を管理するためのオプションがいくつかあります。

### 方法 1:FTP を使用する

FTP クライアントを使用してサーバーに接続し、env フォルダーを削除することができます。一部の FTP クライアント (Web ブラウザーなど) は読み取り専用の場合があり、その場合はフォルダーを削除できません。そのため、削除を実行できる FTP クライアントを使用していることを確認してください。Azure ポータルで、Web サイトのダッシュボード ページに FTP ホストの名前とユーザー名が表示されます。

### 方法 2:ランタイム切り替え

必要なバージョンの Python と一致しない場合、デプロイメント スクリプトは env フォルダーは削除するという事実を利用した別の方法を次に示します。この方法では、実質的に既存の環境を削除し、新しい環境を作成します。

1. 別のバージョンの Python に (runtime.txt または Web サイト構成ページで) 切り替える
1. git で変更をプッシュする (pip インストール エラーがあっても無視)
1. 最初のバージョンの Python に戻す
1. git でもう一度変更をプッシュする

### 方法 3:デプロイメント スクリプトをカスタマイズする

デプロイメント スクリプトをカスタマイズした場合、env フォルダーの削除を強制するように deploy.cmd 内のコードを変更できます。

<!--HONumber=42-->