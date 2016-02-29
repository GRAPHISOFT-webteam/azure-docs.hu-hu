<properties
    pageTitle="監視 XTP 記憶體內儲存體 | Microsoft Azure"
    description="估計和監視 XTP 記憶體內儲存體使用量、容量；解決容量錯誤 41805"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/28/2015"
    ms.author="jodebrui"/>


# 監視 XTP 記憶體內部儲存體

當使用 [記憶體中](sql-database-in-memory.md), ，記憶體最佳化資料表和資料表變數中的資料位於 XTP 記憶體中儲存體。 每個高階服務層具有最大記憶體中儲存體大小，而其已記錄 [SQL Database 服務層文章](sql-database-service-tiers.md#service-tiers-for-single-databases)。 一旦超過此限制，插入和更新作業可能會開始出錯 (錯誤碼 41805)。 屆時您將需要刪除資料以回收記憶體，或升級您的資料庫的效能層。

## 判斷資料是否在記憶體內部儲存容量上限內

判斷儲存體上限: 請參閱 [SQL Database 服務層文章](sql-database-service-tiers.md#service-tiers-for-single-databases) 針對不同的高階服務層的儲存體上限。

估計記憶體最佳化資料表的記憶體需求，其方式如同在 Azure SQL Database 中估計 SQL Server 的記憶體需求。 若要檢閱在該主題在數分鐘 [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)。

請注意，資料表和資料表變數資料列以及索引都會計入最大的使用者資料大小。 此外，ALTER TABLE 需要足夠的空間來建立新版的完整資料表及其索引。

## 監視和警示

您可以監視記憶體中儲存體使用的百分比 [的效能層的儲存體上限](sql-database-service-tiers.md#service-tiers-for-single-databases) azure [入口網站](http://portal.azure.com/): 

- 在 [資料庫] 刀鋒視窗上，找出 [資源使用率方塊] 並按一下 [編輯]。
- 然後選取度量 XTP 記憶體內部儲存空間百分比。
- 若要新增警示，請按一下 [資源使用率] 方塊以開啟 [計量] 刀鋒視窗，然後按一下 [新增警示]。

或使用下列查詢來顯示記憶體內部儲存體使用率：

    select xtp_storage_percent from sys.dm_db_resource_stats


## 更正記憶體不足情況 - 錯誤 41805

記憶體不足導致插入、更新和建立作業失敗，並出現錯誤訊息 41805。

錯誤訊息 41805 指出記憶體最佳化資料表和資料表變數已超過大小上限。

若要解決此錯誤，您可以：


- 從記憶體最佳化資料表中刪除資料，可能會將資料卸載至傳統、以磁碟為基礎的資料表；或者，
- 將服務層升級至具有足夠記憶體內部記憶體的服務層，以便儲存您需要保留在記憶體最佳化資料表中的資料。

## 後續步驟
深入了解有關 [監視 Azure SQL Database 使用動態管理檢視](sql-database-monitoring-with-dmvs.md)

