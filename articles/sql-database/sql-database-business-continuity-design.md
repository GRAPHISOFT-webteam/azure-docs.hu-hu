<properties 
   pageTitle="SQL Database 的業務續航力設計" 
   description="選擇指引：本節提供如何選擇應使用之 BCDR 功能及何時使用的指引。 其中包括使用 SQL DB 會自動取得之功能的說明。"
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/16/2015"
   ms.author="elfish"/>

#業務續航力的設計

設計應用程式的業務續航力之前，您必須先回答下列問題：

1. 哪項業務續航力功能適合防止我的應用程式中斷？
2. 我使用哪個層級的備援和複寫拓樸？

##何時使用異地還原

SQL Database 預設提供每個資料庫的內建基本保護功能。 其作法是將資料庫備份儲存在 Azure 異地備援儲存體 (GRS) 中。 如果您選擇這個方法，則不需要進行任何特殊組態或其他資源配置。 透過這些備份，您可以在任何區域使用異地還原命令來復原資料庫。 使用 [從中斷復原](sql-database-disaster-recovery.md) 使用地理還原來復原應用程式的詳細資料區段。 

如果您的應用程式符合下列準則，則應該使用內建保護功能：

1. 非關鍵性應用程式。 沒有繫結 SLA，因此停機 24 小時或更長的時間不會衍生財務責任。
2. 資料變更率很低 (例如每小時才有幾筆交易)。 RPO 為 1 小時，因此不會導致大量資料遺失。
3. 應用程式對成本很敏感，因此無法採用需要額外成本的異地複寫 

> [AZURE.NOTE] 異地還原不會預先配置的任何特定區域中的計算容量，中斷期間從備份還原作用中資料庫。 這項服務管理與異地還原要求相關之工作負載的方式，是將對該區域中現有資料庫的影響降到最低，並會優先考慮資料庫的容量需求。 因此，資料庫的復原時間取決於當時相同區域中要復原的其他資料庫數目。 

##何時使用異地複寫

異地複寫會在與主要資料庫不同的區域中建立複本 (次要) 資料庫。 它會確保資料庫具有可在復原後支援應用程式工作負載的必要資料和計算資源。 請參閱 [從中斷復原](sql-database-disaster-recovery.md) 一節，以復原應用程式中使用容錯移轉。

如果您的應用程式符合下列準則，則應該使用異地複寫：

1. 是關鍵性應用程式。 具有主動式 RPO 和 RTO 的繫結 SLA。 遺失資料和可用性會衍生財務責任。 
2. 資料變更率很高 (例如每分鐘或每秒便有幾筆交易)。 與預設保護相關的 RPO 為 1 小時，因此可能會導致無法接受的資料遺失。
3. 與潛在的財務責任和相關企業損失相較下，使用異地複寫的相關成本明顯較低。

> [AZURE.NOTE] 如果您的應用程式使用基本層資料庫異地複寫不支援

##何時選擇標準與主動式異地複寫

標準層資料庫沒有使用主動式異地複寫的選項，因此如果您的應用程式使用標準資料庫並符合上述準則，則應該啟用標準異地複寫。 至於高階資料庫則可以選擇任一選項。 標準異地複寫已設計成更簡單且成本更低的嚴重損壞修復解決方案，特別適用於只在發生未規劃事件 (例如中斷) 時使用這個複寫提供保護的應用程式。 透過標準異地複寫，您只能使用 DR 配對區域進行復原，且只能針對每一個主要資料庫建立一個次要資料庫。 應用程式升級案例可能需要額外的次要資料庫。 因此，如果您的應用程式需要升級，則應該改為啟用主動式異地複寫。 請參閱 [升級應用程式，而不需要停機](sql-database-business-continuity-application-upgrade.md) 如需詳細資訊。 

> [AZURE.NOTE] 作用中地理複寫也支援唯讀存取，藉此提供更大的唯讀工作負載的次要資料庫。 

##如何啟用異地複寫

您可以使用 Azure 傳統入口網站，或者透過呼叫 REST API 或 PowerShell 命令來啟用異地複寫。

###Azure 傳統入口網站

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. 登入 [Azure 傳統入口網站](https://portal.Azure.com)
2. 在畫面左側選取 **瀏覽** ，然後選取 **SQL 資料庫**
3. 瀏覽至您資料庫分頁中，選取 **異地複寫對應** 按一下 **設定異地複寫**。
4. 巡覽至 [異地複寫] 刀鋒視窗。 選取目標區域。 
5. 瀏覽至 [建立次要] 刀鋒視窗。 選取目標區域中現有的伺服器，或建立新的伺服器。
6. 選取次要類型 (*可讀取* 或 *不可讀取*)
7. 按一下 [ **建立** 以完成設定

> [AZURE.NOTE] 地理複寫] 分頁上的 DR 配對的區域會標示為 *建議*。 如果您使用高階層資料庫，則可以選擇不同的區域。 如果您使用標準資料庫，則無法變更。 高階資料庫會有第二個類型的選項 (*可讀取* 或 *不可讀取*)。 標準資料庫只能選取 *不可讀取* 次要。


###PowerShell

使用 [新增 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) PowerShell cmdlet 來建立異地複寫組態。 此命令是在同步處理主要和次要資料庫時會同步傳回。 

若要為高階或標準資料庫建立使用不可讀取之次要資料庫的異地複寫進行設定：
        
    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"

若要為高階資料庫建立使用可讀取之次要資料庫的異地複寫：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"
         

###REST API 

使用 [Create Database](https://msdn.microsoft.com/library/mt163685.aspx) API 與 *createMode* 設 *NonReadableSecondary* 或 *次要* 以程式設計方式建立異地複寫的次要資料庫。

這個 API 是非同步的。 傳回使用後， [取得複寫連結](https://msdn.microsoft.com/library/mt600778.aspx) API 來檢查這項作業的狀態。  *ReplicationState* 作業完成時，回應主體的欄位會有更新的值。


##如何選擇容錯移轉組態 

設計應用程式的業務續航力時，您應該考慮幾個組態選項。 您的選擇會視應用程式部署拓撲，以及應用程式的哪些部分最容易受到中斷的影響而定。 請參閱 [設計的嚴重損壞修復使用異地複寫的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md) 指導方針。

