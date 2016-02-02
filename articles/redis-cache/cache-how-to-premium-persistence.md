<properties 
    pageTitle="如何設定高階 Azure Redis 快取的資料永續性" 
    description="了解如何設定和管理高階層 Azure Redis 快取執行個體的資料永續性" 
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
    ms.date="12/03/2015" 
    ms.author="sdanie"/>


# 如何設定高階 Azure Redis 快取的資料永續性

Azure Redis 快取有不同的快取服務，在快取大小和功能 (包括新的高階層) 的選擇上提供了彈性。

Azure Redis 快取高階層包括叢集、永續性及虛擬網路支援。 本文說明如何在高階 Azure Redis 快取執行個體中設定永續性。

如需其他進階快取功能，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md) 和 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。

## 資料永續性是什麼？

Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

Azure Redis 快取提供 Redis 持續性使用 [RDB 模型](http://redis.io/topics/persistence), 、 Azure 儲存體帳戶中儲存資料。 設定永續性後，Azure Redis 快取會依據可設定的備份頻率，在磁碟中保存一份 Redis 二進位格式的 Redis 快取快照。 如果發生同時停用主要和複本快取的災難性事件，即可使用最新的快照重新建構快取。

若要設定永續性，可在建立快取期間從 [**新的 Redis 快取**] 刀鋒視窗設定，也可在現有高階快取的 [**設定**] 刀鋒視窗中設定。

## 建立高階快取

若要建立快取持續性，登入 [Azure 入口網站](https://portal.azure.com) 按一下 **新增**]-> [**資料 + 儲存體**>**Redis 快取**。

![建立 Redis 快取][redis-cache-new-cache-menu]

若要設定永續性，請先在 [**選擇定價層**] 刀鋒視窗中選取其中一個 [**高階**] 快取。

![選擇價格層][redis-cache-premium-pricing-tier]

選取高階定價層後，按一下 [Redis 永續性]****。

![Redis 永續性][redis-cache-persistence]

下節的步驟說明如何在您的新高階快取上設定 Redis 永續性。 Redis 永續性設定後，按一下 [**建立**] 以建立具有 Redis 永續性的新高階快取。

## 設定 Redis 永續性

Redis 永續性是在 [**Redis 資料永續性**] 刀鋒視窗中所設定。 若為新的快取，則在快取建立程序期間存取此刀鋒視窗，如上節所述。 若為現有快取，則從快取的 [**設定**] 刀鋒視窗存取 [**Redis 資料永續性**] 刀鋒視窗。

![Redis 設定][redis-cache-settings]

若要啟用 Redis 持續性，請按一下 [已啟用]**** 來啟用 RDB (Redis 資料庫) 備份。 若要停用先前所啟用高階快取的 Redis 永續性，請按一下 [**已停用**]。

若要設定備份間隔，請選取下拉式清單中的 [備份頻率****。 選項包括 [15 分鐘]****、[30 分鐘]****、[60 分鐘]****、[6 小時]****、[12 小時]**** 及 [24 小]時****。 在先前的備份作業成功完成後，此間隔便會開始倒數計時，時間過後就會起始新的備份。

按一下 [儲存體帳戶]**** 選取要使用的儲存體帳戶，然後從 [儲存體金鑰]**** 下拉式清單中選擇 [主要金鑰]**** 或 [次要金鑰]****。 您必須選擇與快取相同區域的儲存體帳戶，建議選取 [**進階儲存體**] 帳戶，因為進儲存體的輸送量較高。
>[AZURE.IMPORTANT] 如果重新產生了永續性帳戶的儲存體金鑰，您必須從 [**儲存體金鑰**] 下拉式清單中重新選擇所需的金鑰。

![Redis 永續性][redis-cache-persistence-selected]

按一下 [確定]**** 以儲存持續性組態。

備份頻率間隔過後，會啟動下一個備份 (或新快取的第一個備份)。



## 永續性常見問題集

下列清單包含 Azure Redis 快取永續性常見問題的解答。

## 可以對先前建立的快取啟用永續性嗎？

可以，可在建立快取時以及現有高階快取中設定 Redis 永續性。

## 在建立快取之後，可以變更備份頻率嗎？

可以，您可以在 [**Redis 資料永續性**] 刀鋒視窗上變更備份頻率。 如需指示，請參閱 [設定 Redis 持續性](#configure-redis-persistence)。

## 為什麼我的備份頻率是 60 分鐘，備份的間隔卻超過 60 分鐘？

在前一個備份程序順利完成後，備份頻率間隔才會開始計算。 如果備份頻率是 60 分鐘，而備份程序要 15 分鐘才能順利完成，則下一次備份要在先前的備份開始的 75 分鐘後才會開始。

## 建立新的備份時，舊的備份會發生什麼事？

除了最新的備份外，所有備份都會自動刪除。 這項刪除作業可能不會立即發生，但較舊的備份不會無限期保存。

## 後續步驟

了解如何使用更多高階快取功能。

-   [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)
-   [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)




[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png 
[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png 
[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png 
[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png 
[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png 

