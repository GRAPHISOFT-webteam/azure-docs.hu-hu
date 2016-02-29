<properties 
    pageTitle="如何設定高階 Azure Redis 快取的 Redis 叢集" 
    description="了解如何建立和管理高階層 Azure Redis 快取執行個體的 Redis 叢集" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="sdanie"/>

# 如何設定高階 Azure Redis 快取的 Redis 叢集
Azure Redis 快取有不同的快取服務，在快取大小和功能 (包括新的高階層) 的選擇上提供了彈性。

Azure Redis 快取高階層包括叢集、永續性及虛擬網路支援。 本文說明如何在高階 Azure Redis 快取執行個體中設定叢集。

如需其他進階快取功能，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md) 和 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。

## Redis 叢集是什麼？
Azure Redis 快取提供 Redis 叢集做為 [Redis 中實作](http://redis.io/topics/cluster-tutorial)。 使用 Redis 叢集可以獲得下列好處： 

-   能夠自動分割您在多個節點之間的資料集。 
-   當節點的子集發生故障或無法與叢集的其餘部分通訊時，可以繼續作業。 
-   更多輸送量：當您增加分區數目時，輸送量會呈線性增加。 
-   更多記憶體大小：當您增加分區數目時，會呈線性增加。  

請參閱 [Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 如需詳細資訊大小、 輸送量和進階版快取使用的頻寬。 

在 Azure 中，Redis 叢集以主要/複本模型方式提供，其中的每個分區都有一個具複寫功能的主要/複本組，而複寫是由 Azure Redis 快取服務管理。 

## 叢集
叢集上啟用 **新增 Redis 快取** 期間建立快取的刀鋒視窗。 若要建立快取，登入 [Azure 入口網站](https://portal.azure.com) 按一下 **新增**]-> [**資料 + 儲存體**>**Redis 快取**。

![建立 Redis 快取][redis-cache-new-cache-menu]

若要設定叢集，請先選取其中一個 **高階** 會在快取 **選擇價格層** 刀鋒視窗。

![選擇定價層][redis-cache-premium-pricing-tier]

叢集上設定 **Redis 叢集** 刀鋒視窗。

![叢集][redis-cache-clustering]

叢集中最多可包含 10 個分區。 按一下 [ **啟用** 並將滑桿，或輸入一個介於 1 和 10 **分區計數** 按一下 **確定**。

每個分區都是一個由 Azure 管理的主要/複本快取組，快取總大小的計算方式，是將分區數目乘以在定價層中選取的快取大小。 

![叢集][redis-cache-clustering-selected]

建立快取後，您可以連接並使用它，就像非叢集化快取一樣，而且 Redis 將會在整個快取分區散發資料。 如果是診斷 [啟用](cache-how-to-monitor.md#enable-cache-diagnostics), ，會分別擷取每個分區的度量，而且可以是 [檢視](cache-how-to-monitor.md) Redis 快取刀鋒視窗中。 

>[AZURE.IMPORTANT] 啟用使用 StackExchange.Redis 叢集連線至 Azure Redis 快取時, 可能遇到的問題和接收 `MOVE` 例外狀況。 這是因為 StackExchange.Redis 快取用戶端收集快取叢集中節點資訊的間隔時間短。 如果您是第一次連接快取，並在用戶端完成收集此資訊前立即呼叫快取，便會發生這些例外狀況。 解決應用程式中此問題最簡單方式就是連接快取，然後等候一秒後再對快取進行任何呼叫。 做法是依照下列範例程式碼所示新增 `Thread.Sleep(1000)`。 請注意，`Thread.Sleep(1000)` 只會在初次連接快取的期間發生。 如需詳細資訊，請參閱 [StackExchange.Redis.RedisServerException-移動 #248](https://github.com/StackExchange/StackExchange.Redis/issues/248)。 此問題的修正程式正在開發中，若有任何更新，將張貼於此處。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        // Connect to the Redis cache for the first time
        var connection =  ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

        // Wait for 1 second
        Thread.Sleep(1000);

        // Return the connected ConnectionMultiplexer
        return connection;
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## 從執行中的高階快取新增或移除分區

若要新增或移除分區執行高階的快取叢集已啟用，按一下 [ **Redis 叢集大小 (預覽)** 從 **設定** 刀鋒視窗。

>[AZURE.NOTE] 請注意，雖然 Azure Redis 快取進階層已經發行上市，Redis 叢集大小功能目前為預覽狀態。

![Redis 叢集大小][redis-cache-redis-cluster-size]

若要變更分區計數，使用滑桿，或輸入一個介於 1 和 10，以 **分區計數** 文字方塊中，然後按一下 [ **確定** 儲存。

## 叢集常見問題集

下列清單包含 Azure Redis 快取叢集常見問題的解答。

## 我需要對我的用戶端應用程式進行任何變更才能使用叢集嗎？

-   啟用叢集時，只可以使用資料庫 0。 如果用戶端應用程式使用多個資料庫，它會嘗試讀取或寫入至 0 以外的資料庫，則會擲回下列例外狀況。 `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
-   如果您使用 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 您必須使用 1.0.481 或更新版本。 您連接到使用相同的快取 [端點、 連接埠和索引鍵](cache-configure.md#properties) 連接沒有啟用叢集的快取時，所使用。 唯一的差別在於必須在資料庫 0 上完成所有的讀取和寫入。
    -   其他用戶端可能有不同的需求。 請參閱 [執行所有的 Redis 用戶端支援叢集?](#do-all-redis-clients-support-clustering)。
-   如果您的應用程式使用分成單一命令的多個索引鍵作業，則所有索引鍵都必須位於相同的分區。 若要這麼做，請參閱 [金鑰散發在叢集中的方式?](#how-are-keys-distributed-in-a-cluster)。
-   如果您使用 Redis ASP.NET 工作階段狀態提供者，則必須使用 2.0.0 或更高版本。 請參閱 [使用群集的 Redis ASP.NET 工作階段狀態與輸出快取提供者與?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)。

## 如何在叢集中散發索引鍵？

每個 Redis [金鑰發佈模型](http://redis.io/topics/cluster-spec#keys-distribution-model) 文件: 主要空間分割成 16384 的位置。 每個索引鍵都會雜湊並指派給上述的其中一個位置，而這些位置散發於叢集的各個節點。 您可以設定哪個部分的索引鍵會雜湊，以確保多個索引鍵位於使用雜湊標記的相同分區中。

-   具有雜湊標記的金鑰 - 如果金鑰的任何部分被括在 `{` 和 `}` 中，則只有該部分的金鑰會為了判斷金鑰的雜湊位置而進行雜湊。 例如，下列 3 個金鑰會位於相同的分區︰`{key}1`、`{key}2` 和 `{key}3`，因為只會雜湊名稱的 `key` 部分。 索引鍵雜湊標記規格的完整清單，請參閱 [金鑰雜湊標記](http://redis.io/topics/cluster-spec#keys-hash-tags)。
-   沒有雜湊標記的索引鍵 - 整個索引鍵名稱都用於雜湊。 這會導致以統計方式平均散發於快取的各個分區。

如需最佳的效能和輸送量，我們建議平均散發索引鍵。 如果您使用具有雜湊標記的索引鍵，則應用程式必須負責確保平均散發索引鍵。

如需詳細資訊，請參閱 [金鑰發佈模型](http://redis.io/topics/cluster-spec#keys-distribution-model), ，[Redis 叢集資料分區化](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), ，和 [金鑰雜湊標記](http://redis.io/topics/cluster-spec#keys-hash-tags)。

## 我可以建立的最大快取大小為何？

最大的高階快取大小為 53 GB。 您最多可以建立 10 個分區，等於最大大小為 530 GB。 如果您需要較大的大小可以 [要求更多](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。 如需詳細資訊，請參閱 [Azure Redis 快取定價](https://azure.microsoft.com/pricing/details/cache/)。

## 所有 Redis 用戶端都支援叢集嗎？ 

現階段，並非所有用戶端都支援 Redis 叢集。 StackExchange.Redis 就是不支援的其中一例。 如需其他用戶端的詳細資訊，請參閱 [播放與叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) 區段 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)。

>[AZURE.NOTE] 如果您使用 StackExchange.Redis 作為您的用戶端，請確定您使用最新版的 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 或更新版本才能正常運作的叢集。

## 啟用叢集後，要如何連接到我的快取？

您可以連接到您使用相同的快取 [端點、 連接埠和索引鍵](cache-configure.md#properties) 連接沒有啟用叢集的快取時，所使用。 Redis 會管理後端上的叢集，因此您不需從用戶端進行管理。

## 我可以直接連接到我的快取的個別分區嗎？

目前官方尚未提供支援。 如前所述，每個分區都包含一個主要/複本快取組，統稱為快取執行個體。 您可以連接到使用中的 redis cli 公用程式的這些快取執行個體 [不穩定](http://redis.io/download) 在 GitHub 的 Redis 儲存機制分支。 使用 `-c` 參數啟用這個版本時，會實作基本支援。 如需詳細資訊，請參閱 [播放與叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) 上 [http://redis.io](http://redis.io) 中 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)。

如為非 SSL，請使用下列命令。

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

如為 SSL，將 `1300N` 取代為 `1500N`。

## 我可以為先前建立的快取設定叢集嗎？

目前您只能在建立快取時啟用叢集。 您可以在建立快取之後變更分區計數，但無法在建立快取之後將叢集新增至高階快取，或從高階快取中移除叢集。 已啟用叢集且只有一個分區的高階快取，與相同大小且沒有叢集的高階快取不同。

## 我可以設定基本或標準快取的叢集嗎？

叢集僅適用於高階快取。

## 我可以將叢集使用於 Redis ASP.NET 工作階段狀態和輸出快取提供者嗎？

-   **Redis 輸出快取提供者** -不需要變更。
-   **Redis 工作階段狀態提供者** -若要使用叢集，您必須使用 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.0 或更高版本，或例外狀況就會擲回。 這是一項重大變更。如需詳細資訊，請參閱 [v2.0.0 的重大變更詳細資料](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。

## 後續步驟
了解如何使用更多高階快取功能。

-   [如何設定高階 Azure Redis Cache 的永續性](cache-how-to-premium-persistence.md)
-   [如何設定高階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
