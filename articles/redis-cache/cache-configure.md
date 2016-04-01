<properties 
    pageTitle="如何設定 Azure Redis 快取"
    description="了解 Azure Redis 快取的預設 Redis 組態，以及了解如何設定您的 Azure Redis 快取執行個體"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="dwrede"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/11/2015"
    ms.author="sdanie" />

# 如何設定 Azure Redis 快取

本主題描述如何檢視並更新您的 Azure Redis 快取執行個體的組態，並涵蓋 Azure Redis 快取執行個體的預設 Redis 伺服器組態。

>[AZURE.NOTE] 如需有關設定和使用進階快取功能的詳細資訊，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md), ，[如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md), ，和 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。

## 設定 Redis 快取設定

可存取快取 [Azure 入口網站](https://portal.azure.com) 使用 **瀏覽** 刀鋒視窗。

![Azure Redis 快取瀏覽刀鋒視窗](./media/cache-configure/IC796920.png)

按一下 [ **Redis 快取** 若要檢視您的快取。

![Azure Redis 快取瀏覽快取清單](./media/cache-configure/IC796921.png)

選取所需的快取，來檢視該快取的屬性。

![Redis 快取所有設定](./media/cache-configure/IC808312.png)

按一下 [ **設定** 或 **所有設定** 來檢視和設定快取。

![Redis 快取設定](./media/cache-configure/IC808313.png)

## 屬性

按一下 [ **屬性** 若要檢視快取，包括快取端點和連接埠的相關資訊。

![Redis 快取屬性](./media/cache-configure/IC808314.png)

## 存取金鑰

按一下 [ **存取金鑰** 來檢視或重新產生您的快取的存取金鑰。 主機名稱和連接埠，以及使用這些金鑰 **屬性** 刀鋒視窗中的用戶端連接到您的快取。

![Redis 快取存取金鑰](./media/cache-configure/IC808315.png)

## 存取連接埠

根據預設，新的快取會停用非 SSL 存取。 若要啟用非 SSL 連接埠，請按一下 [ **存取連接埠** ] 刀鋒視窗，然後按一下 [ **否**。

![Redis 快取存取連接埠](./media/cache-configure/IC808316.png)

## 定價層

按一下 [ **定價層** 來檢視或變更您的快取的價格區間。 如需有關調整的詳細資訊，請參閱 [如何調整 Azure Redis 快取](cache-how-to-scale.md)。

![Redis 快取定價層](./media/cache-configure/pricing-tier.png)

## 診斷

按一下 [ **診斷** 來設定用來儲存快取診斷的儲存體帳戶。

![Redis 快取診斷](./media/cache-configure/IC808317.png)

如需詳細資訊，請參閱 [如何監視 Azure Redis 快取](cache-how-to-monitor.md)。

## Maxmemory-policy 和 maxmemory-reserved

按一下 [ **Maxmemory 原則** 設定快取的記憶體原則。  **Maxmemory 原則** 設定會設定快取的收回原則和 **maxmemory 保留** 設定保留給非快取程序的記憶體。

![Redis 快取 Maxmemory 原則](./media/cache-configure/IC808318.png)

**Maxmemory 原則** 可讓您選擇從下列收回原則。

-   volatile-lru - 這是預設值。
-   allkeys lru
-   volatile-random
-   allkeys-random
-   volatile-ttl
-   noeviction

如需 maxmemory 原則的詳細資訊，請參閱 [收回原則](http://redis.io/topics/lru-cache#eviction-policies)。

 **Maxmemory 保留** 設定會設定記憶體數量 （mb），保留給非快取作業，例如在容錯移轉期間的複寫。 當具有高片段比率時，也可使用它。 設定此值可讓您在負載變動時具有更一致的 Redis 伺服器體驗。 對於頻繁寫入的工作負載，此值應該設定為更高的值。 當記憶體保留給這類作業時，無法用於儲存快取的資料。

>[AZURE.IMPORTANT]  **Maxmemory 保留** 設定僅適用於標準和高級快取。

## Keyspace 通知 (進階設定)

按一下 [ **進階設定** 來設定 Redis keyspace 通知。 Keyspace 通知可讓用戶端在特定事件發生時收到通知。

![Redis 快取進階設定](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT] Keyspace 通知和 **通知 keyspace 事件** 設定僅適用於標準和進階版快取。

如需詳細資訊，請參閱 [Redis Keyspace 通知](http://redis.io/topics/notifications)。 範例程式碼，請參閱 [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) 檔案中 [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例。

## Redis 資料永續性

按一下 [ **Redis 資料持續性** 加以啟用，停用，或設定進階快取的資料持續性。

![Redis 資料永續性](./media/cache-configure/redis-cache-persistence-settings.png)

若要啟用 Redis 持續性，請按一下 [ **啟用** 啟用 RDB （Redis 資料庫） 備份。 若要停用 Redis 持續性，請按一下 [ **已停用**。

若要設定備份的時間間隔，請選取 **備份頻率** 從下拉式清單。 選項包括 **15 分鐘**, ，**30 分鐘內**, ，**60 分鐘**, ，**6 小時**, ，**12 小時**, ，和 **24 小時**。 在先前的備份作業成功完成後，此間隔便會開始倒數計時，時間過後就會起始新的備份。

按一下 [ **儲存體帳戶** 選取儲存體帳戶使用，然後選擇 [ **主索引鍵** 或 **次要金鑰** 如何從使用 **儲存體金鑰** 下拉式清單。 您必須選擇儲存體帳戶中快取中，相同的區域和 **高階儲存體** 建議的帳戶，因為高階儲存體具有更高的輸送量。 每當您的持續性帳戶的儲存體金鑰重新產生時，您必須重新選擇所需的索引鍵，從 **儲存體金鑰** 下拉式清單。

按一下 [ **確定** 儲存持續性設定。

>[AZURE.IMPORTANT] Redis 資料持續性僅適用於進階版快取。 如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。

## Redis 叢集大小

按一下 [ **（預覽） Redis 叢集大小** 來新增或移除分區執行高階啟用叢集的快取。

>[AZURE.NOTE] 請注意，雖然 Azure Redis 快取進階層已經發行上市，Redis 叢集大小功能目前為預覽狀態。

![Redis 叢集大小](./media/cache-configure/redis-cache-redis-cluster-size.png)

若要變更分區計數，使用滑桿，或輸入一個介於 1 和 10，以 **分區計數** 文字方塊中，然後按一下 [ **確定** 儲存。

>[AZURE.IMPORTANT] Redis 叢集只適用於進階版快取。 如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。


## 使用者和標記

![Redis 快取使用者和標記](./media/cache-configure/IC808320.png)

 **使用者** 一節提供支援角色型存取控制 (RBAC) 在 Azure 網站，以協助組織輕鬆又準確地滿足其存取管理需求。 如需詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](http://go.microsoft.com/fwlink/?LinkId=512803)。

 **標記** 節可幫助您組織您的資源。 如需詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

## 預設 Redis 伺服器組態

新的 Azure Redis 快取執行個體是以下列的預設 Redis 組態值設定。

>[AZURE.NOTE] 無法變更此區段中的設定，使用 `StackExchange.Redis.IServer.ConfigSet` 方法。 如果在本區段中利用其中一個命令呼叫此方法，則會擲回如下的例外狀況：  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>任何值，可加以設定，例如 **最大記憶體原則**, ，可以透過 Azure 入口網站。

|設定|預設值|說明|
|---|---|---|
|資料庫|16|預設資料庫為 DB 0。您可以根據每個連線使用 connection.GetDataBase(dbid) 選取一個不同的資料庫，其中 dbid 是介於 0 與 15 之間的數字。|
|maxclients|取決於定價層 <sup>1</sup>|這是允許同時連線的用戶端數目上限。 一旦達到限制，Redis 將關閉所有新的連接，同時傳送「達到用戶端的數目上限」錯誤。|
|maxmemory-policy|volatile-lru|Maxmemory 原則可設定當達到 maxmemory (建立快取時所選取之快取提供項目的大小) 時 Redis 將如何選取要移除的具目。 Azure Redis 快取的預設設定為 volatile-lru，其會移除使用 LRU 演算法設定到期日的金鑰。 此設定可以在 Azure 入口網站中設定。 如需詳細資訊，請參閱 [maxmemory-policy 和 maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)。|
|maxmemory-samples|3|LRU 和最小 TTL 演算法不是精確的演算法，而是近似的演算法 (為了節省記憶體)，因此您也可以選取要檢查的範例大小。 例如，預設為 Redis 將檢查三個金鑰，並挑選最近較少使用的金鑰。|
|lua-time-limit|5,000|Lua 指令碼的最大執行時間 (以毫秒為單位)。 如果已到達最大執行時間，Redis 會記錄指令碼在最大允許的時間之後仍在執行中，並開始回覆查詢發生錯誤。|
|lua-event-limit|500|這是指令碼事件佇列的最大大小。|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|用戶端輸出緩衝區限制可用來強制中斷基於某些原因而無法足夠快地從伺服器讀取資料之用戶端的連線 (常見的原因是 Pub/Sub 用戶端使用訊息的速度無法與發佈者產生這些訊息的速度一樣快)。 如需詳細資訊，請參閱 [http://redis.io/topics/clients](http://redis.io/topics/clients)。|

每個 Azure Redis 快取定價層的 <sup>1</sup>`maxclients` 皆不相同。

-   基本和標準的快取
    -   C0 (250 MB) 快取 - 最多 256 個連接
    -   C1 (1 GB) 快取 - 最多 1,000 個連接
    -   C2 (2.5GB) 快取 - 最多 2,000 個連接
    -   C3 (6 GB) 快取 - 最多 5,000 個連接
    -   C4 (13 GB) 快取 - 最多 10,000 個連接
    -   C5 (26 GB) 快取 - 最多 15,000 個連接
    -   C6 (53 GB) 快取 - 最多 20,000 個連接
-   高階快取
    -   P1 (6 GB - 60 GB) - 最多 7,500 個連接
    -   P2 (13 GB - 130 GB) - 最多 15,000 個連接
    -   P3 (26 GB - 260 GB) - 最多 30,000 個連接
    -   P4 (53 GB - 530 GB) - 最多 40,000 個連接

## Azure Redis 快取中不支援的 Redis 命令

>[AZURE.IMPORTANT] 因為 Azure Redis 快取執行個體的組態與管理是使用 Azure 入口網站的下列命令會停用。 如果嘗試叫用它們，會收到類似 `"(error) ERR unknown command"` 的錯誤訊息。
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIG
>-  DEBUG
>-  MIGRATE
>-  SAVE
>-  SHUTDOWN
>-  SLAVEOF

如需 Redis 命令的詳細資訊，請參閱 [http://redis.io/commands](http://redis.io/commands)。

## Redis 主控台

您安全地發出命令至您的 Azure Redis 快取執行個體使用 **Redis 主控台**, 、 這是適用於標準和高級快取。

>[AZURE.IMPORTANT] Redis 主控台無法搭配 VNET 或叢集。 
>
>-  [VNET](cache-how-to-premium-vnet.md) -當您的快取屬於 VNET，只有在 VNET 中的用戶端可以存取快取。 Redis 主控台使用的 redis cli.exe 用戶端裝載於不屬於您 VNET 的 VM 上，因此主控台無法連接到您的快取。
>-  [叢集](cache-how-to-premium-clustering.md) -Redis 主控台會使用不支援這一次叢集 cli.exe 用戶端。 中的 redis cli 公用程式 [不穩定](http://redis.io/download) 在 GitHub 的 Redis 儲存機制分支實作基本支援，啟動時 `-c` 切換。 如需詳細資訊，請參閱 [播放與叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) 上 [http://redis.io](http://redis.io) 中 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)。

若要存取 Redis 主控台，按一下 [ **主控台** 從 **Redis 快取** 刀鋒視窗。

![Redis 主控台](./media/cache-configure/redis-console-menu.png)

只需在主控台中輸入想要的命令，即可對您的快取執行個體發出命令。

![Redis 主控台](./media/cache-configure/redis-console.png)

如已停用 Azure Redis 快取的 Redis 命令的清單，請參閱先前 [Azure Redis 快取中不支援的 Redis 命令](#redis-commands-not-supported-in-azure-redis-cache) 一節。 如需 Redis 命令的詳細資訊，請參閱 [http://redis.io/commands](http://redis.io/commands)。 

## 後續步驟
-   如需有關使用 Redis 命令的詳細資訊，請參閱 [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)。

