<properties 
   pageTitle="Java で Azure Redis Cache を使用する方法" 
   description="Java を使用して Azure Redis Cache を使用します" 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/20/2015"
   ms.author="mwasson"/>

# Java で Azure Redis Cache を使用する方法 

Azure Redis Cache を使用すると、Microsoft が管理している、セキュリティで保護された専用 Redis Cache にアクセスできます。キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Java を使用して Azure Redis Cache を使用する方法を説明しています。


## 前提条件

[Jedis](https://github.com/xetorthio/jedis) - Redis 用 Java クライアント

このチュートリアルでは Jedis を使用しますが、[http://redis.io/clients](http://redis.io/clients) に記載されている任意の Java クライアントを使用できます。


## Azure で Redis Cache を作成する

[Azure の管理ポータル](http://go.microsoft.com/fwlink/?LinkId=398536)で、**[新規]** をクリックし、**[Redis Cache]** を選択します。 

  ![][1]

DNS ホスト名を入力します。`<名前>.redis.cache.windows.net` という形式になります。**[作成]** をクリックします。

  ![][2]


キャッシュが作成されたら、ポータルでそのキャッシュをクリックして設定を表示します。**[キー]** の下のリンクをクリックして、プライマリ キーをコピーします。このキーは、要求を認証するのに必要になります。

  ![][4]


## 非 SSL エンドポイントを有効にする


**[ポート]** の下のリンクをクリックし、[SSL 経由でのみアクセスを許可する] に対して **[いいえ]** をクリックします。これにより、キャッシュに対して非 SSL ポートが有効になります。現在、Jedis クライアントは SSL をサポートしていません。

  ![][3]


## キャッシュに何か追加し、取得する

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;
	 
	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	} 
    

## 次のステップ

- [キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。 
- 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png


<!--HONumber=49-->