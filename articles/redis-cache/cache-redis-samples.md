<properties 
	pageTitle="Azure Redis Cache のサンプル" 
	description="Azure Redis Cache の使用方法について説明します" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache のサンプル 

このトピックでは、キャッシュへの接続、キャッシュ内のデータの読み取りと書き込み、ASP.NET Redis Cache プロバイダーの使用などのシナリオを含め、Azure Redis Cache のサンプルの一覧を示します。サンプルの一部はダウンロード可能なプロジェクトです。ステップ バイ ステップ ガイダンスが用意されているサンプルやコード スニペットが含まれているサンプルもありますが、ダウンロード可能なプロジェクトにはリンクしていません。

## Hello world サンプル

このセクションのサンプルでは、Azure Redis Cache インスタンスへの接続およびさまざまな言語と Redis クライアントを使用したキャッシュ内のデータの読み取りと書き込みの基本を示します。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルでは、[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET クライアントを使用してキャッシュ内の項目を読み書きする方法を示しています。

「[Node.js で Azure Redis Cache を使用する方法](cache-nodejs-get-started.md)」では、Node.js と [node_redis](https://github.com/mranney/node_redis) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[Java で Azure Redis Cache を使用する方法](cache-java-get-started.md)」では、Java と [Jedis](https://github.com/xetorthio/jedis) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[Python で Azure Redis Cache を使用する方法](cache-python-get-started.md)」では、Python と [redis-py](https://github.com/andymccurdy/redis-py) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[PHP の例](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample)」では、PHP と [predis](https://github.com/nrk/predis) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[キャッシュ内で .NET オブジェクトを使用する](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects)」では、Azure Redis Cache インスタンスとの間でオブジェクトを読み書きできる、.NET オブジェクトをシリアル化する方法の 1 つを説明しています。 

## ASP.NET SignalR のスケールアウト バックプレーンとしての Redis Cache の使用

[ASP.NET SignalR のスケール アウト バックプレーンとしての Redis Cache の使用](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)サンプルでは、SignalR のバックプレーンとして Azure Redis Cache を使用する方法について示しています。バックプレーンの詳細については、[Redis を使用した SignalR のスケールアウト](http://www.asp.net/signalr/overview/performance/scaleout-with-redis)に関するページを参照してください。

## Redis Cache の顧客クエリのサンプル

このサンプルでは、キャッシュ内のデータへのアクセスと永続ストレージ内のデータへのアクセスとのパフォーマンスを比較します。このサンプルには 2 つのプロジェクトがあります。

-	[Redis Cache でデータをキャッシュしてパフォーマンスを向上させる方法のデモ](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[デモ用のデータベースおよびキャッシュのシード](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## ASP.NET セッション状態と出力キャッシュ

[Azure Redis Cache を使用した ASP.NET SessionState と OutputCache の格納](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching)サンプルでは、Azure Redis Cache を使用して、Redis の SessionState プロバイダーと OutputCache プロバイダーで ASP.NET セッションと出力キャッシュを格納する方法を示しています。

## MAML を使用した Azure Redis Cache の管理

[Azure 管理ライブラリを使用した Azure Redis Cache の管理](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)サンプルでは、Azure 管理ライブラリを使用してキャッシュを管理 (作成/更新/削除) する方法を示しています。 

## カスタム監視のサンプル

[Redis Cache の監視データへのアクセス ](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)サンプルは、Azure ポータルの外部から Azure Redis Cache の監視データにアクセスする方法を示しています。



<!--HONumber=49-->