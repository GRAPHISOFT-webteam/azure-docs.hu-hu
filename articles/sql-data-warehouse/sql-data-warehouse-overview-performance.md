<properties
   pageTitle="效能和調整延展性概觀 | Microsoft Azure"
   description="SQL 資料倉儲的效能和調整功能簡介。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;mausher;nicw"/>

# 效能和調整概觀
將您的資料倉儲放在雲端，就不再需要處理低階的硬體問題。 您再也不需要為了獲得優異的資料倉儲效能，而研究處理器類型、需要的記憶體容量或儲存空間類型。 相反地，SQL 資料倉儲要問您：您想要多快的資料分析速度？ 

SQL 資料倉儲是雲端分散式資料庫平台，其設計目的是在您對於用來解析查詢的資源擁有完整控制權的範圍內提供優異的效能。 只需調整配置給資料倉儲的資料倉儲單位數量，就能在幾秒內彈性調整倉儲資源的大小。 SQL 資料倉儲是一個分散式的相應放大平台，可讓您的資料倉儲以有效率且效益佳的方式處理大量資料磁碟區，同時您也能完全控制解決方案的成本。

*下列資訊適用於 Azure SQL 資料倉儲在預覽期間發行。 這項資訊將會因為服務隨著 GA 日的到來不斷提升而持續更新。*

我們的 SQL 資料倉儲目標是：
-   可預測的效能與數 PB 資料的線性延展性
-   服務等級協定 (SLA) 支援的所有資料倉儲作業皆有極佳的穩定性
-   跨關聯式與非關聯式資料快速地從載入資料進行至資料深入解析

我們將在預覽期間朝這些目標持續努力，在公開上市 (GA) 之前達成目標。

>[AZURE.NOTE] 下列章節說明公開預覽推出的 Azure SQL 資料倉儲服務。 這項資訊將會因為服務隨著 GA 日的到來不斷提升而持續更新。 

## 資料保護
SQL 資料倉儲會使用異地備援 blob，將所有資料儲存在在 Azure 儲存體中。 並在本機的 Azure 區域維護三份資料的同步複本，確保當地語系化失敗 (例如儲存體磁碟機失敗) 時能夠提供透明的資料保護。 此外，還會在遠端 Azure 區域維護三份非同步複本，確保區域失敗 (災害復原) 時能夠提供資料保護。 將本機和遠端區域配對，以維持可接受的同步處理延遲 (例如美國東部和西部)。

## 資料庫還原
SQL 資料倉儲會使用 Azure 儲存體快照，每 4 小時備份一次所有資料。 這些快照會免費保留 7 天。 這樣一來，在擷取最新快照時的過去 7 天內，最多就有 42 個時間點可用來還原資料。 在預覽期間，會引進指定不同保留值的功能。 可使用 PowerShell 或 REST API 從快照還原資料。 快照會以非同步的方式複製到遠端 Azure 區域，以在區域失敗 (災害復原) 時增加復原能力。 

## 可靠性
SQL 資料倉儲是具有多個元件的分散式系統，其元件數量會隨著資料倉儲單位 (DWU) 增加而成長。 SQL 資料倉儲會自動偵測並降低計算失敗，不過，作業 (例如資料載入或查詢) 可能會因為個別計算失敗而失敗。 在預覽期間，我們將持續增強查詢可靠性，讓大部分查詢都可不受失敗的影響順利完成作業。

根據所收集的遙測資料顯示，SQL 資料倉儲對典型的資料倉儲工作負載的可靠性預估為 98%。 這表示，100 次查詢中平均會有 2 次查詢可能會因系統錯誤而失敗。 這並非預覽的 SLA，而是所執行查詢的預期可靠性。 請注意，查詢失敗的可能性會隨著其執行時間而增加 (例如，需時 2 小時以上的查詢，其失敗的可能性遠高於需時 10 分鐘以下的查詢)。 在預覽期間，我們將持續增強服務品質，確保作業無論執行時間長短都能提供相同等級的可靠性。 我們將透過發行增強功能，更新預期的可靠性，來達成在 GA 發行時提供完整 SLA 的目標。

在預覽期間，SQL 資料倉儲每個月都可能有高達 5 個維護事件，用以安裝重大修正程式。 每個事件都可能會造成查詢失敗，而失敗時間取決於 SQL 資料倉儲執行個體所使用的 DWU 量，最多為 2 小時。 我們將盡一切努力，在發生這些事件的 48 小時前通知客戶，以進行適當的規劃。

## 效能和延展性
SQL 資料倉儲引進了資料倉儲單位 (DWU)，做為許多節點的彙總計算資源 (CPU、記憶體、儲存體 I/O) 的抽象概念。 增加 DWU 數量也會增加 SQL 資料倉儲執行個體的彙總計算資源。 SQL 資料倉儲會將作業 (例如資料載入或查詢) 散發給執行個體中的所有計算基礎結構，隨著系統相應增加或減少而提高或降低載入與查詢的效能。

任何資料倉儲都具有 2 個基本效能度量： 
- **載入速率**︰ 可以載入資料倉儲每秒的記錄數目。 我們會特別測量可透過 PolyBase 從 Azure Blob 儲存體匯入資料表 (具有叢集資料行存放區索引) 的記錄數量。
- **掃描速率**︰ 可以從每秒的資料倉儲循序擷取的記錄數目。 這項計量會特別測量查詢所傳回的資料表 (具有叢集資料行存放區索引) 的記錄數量。

在預覽期間，我們將持續增強服務品質以提高這些速率，並確保這些速率依可預期的方式調整。 

## 關鍵效能概念

請參閱下列文章，以瞭解一些關鍵效能與調整的其他概念：

- [效能和延展性][]
- [並行模型][]
- [設計資料表][]
- [為資料表選擇雜湊散發索引鍵][]
- [用來改善效能的統計資料][]

## 後續步驟
請參閱 [開發概觀][] 文章，以取得有關建置 SQL 資料倉儲解決方案的一些指引。

<!--Image references-->

<!--Article references-->

[performance and scale]: sql-data-warehouse-performance-scale.md
[concurrency model]: sql-data-warehouse-develop-concurrency.md
[designing tables]: sql-data-warehouse-develop-table-design.md
[choose a hash distribution key for your table]: sql-data-warehouse-develop-hash-distribution-key
[statistics to improve performance]: sql-data-warehouse-develop-statistics.md
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->


