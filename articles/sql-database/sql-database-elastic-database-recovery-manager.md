<properties 
    pageTitle="使用復原管理員修正分區對應問題 | Microsoft Azure" 
    description="使用 RecoveryManager 類別來解決分區對應的問題" 
    services="sql-database" 
    documentationCenter=""  
    manager="jeffreyg"
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/09/2015" 
    ms.author="ddove"/>

# 使用 RecoveryManager 類別來修正分區對應問題

 [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) 類別可讓 ADO.Net 應用程式輕鬆地偵測並更正任何全域分區對應 (GSM) 與分區化資料庫環境中的本機分區對應 (LSM) 不一致。 

GSM 和 LSM 會追蹤分區化環境中每個資料庫的對應。 但偶爾 GSM 和 LSM 之間會發生中斷的情況。 在此情況下，使用 RecoveryManager 類別來偵測並修復中斷。

RecoveryManager 類別是一部分 [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library)。 


![分區對應][1]


關於詞彙定義，請參閱 [彈性資料庫工具字彙](sql-database-elastic-scale-glossary.md)。 若要了解如何 **ShardMapManager** 用來管理分區化解決方案中的資料，請參閱 [分區對應管理](sql-database-elastic-scale-shard-map-management.md)。


## 為何使用復原管理員？

在分區化資料庫環境中，有許多資料庫伺服器。 每一部伺服器包含許多資料庫 - 在多租用戶方案中每個使用者一個資料庫。 必須對應每個資料庫，以便呼叫可以精確地路由傳送至正確的伺服器和資料庫。 根據分區化索引鍵追蹤資料庫，而每個伺服器會獲指派一個範圍的索引鍵值。 例如，分區化索引鍵可能代表客戶名稱從 "D" 到 "F"。 所有伺服器和其索引鍵範圍的對應都包含在全域分區對應中。 每一部伺服器也包含分區對應上所包含的資料庫的對應 - 這稱為本機分區對應。 LSM 用來驗證快取的資料。 (當應用程式連接到分區時，會隨著應用程式快取對應，以供快速擷取。 LSM 會驗證對應。) 

您可以使用工具 (例如彈性資料庫用戶端工具程式庫) 在分區之間移動資料。 如果在移動期間發生中斷，GSM 和 LSM 可能會變得不同步。 其他原因包括：

1. 由於刪除其範圍被認為不再使用中的分區，或由於重新命名分區所造成的不一致。 刪除分區導致 **孤立的分區對應**。 重新命名的資料庫同樣可能造成被遺棄的分區對應。 在此情況下，只需要更新分區位置。 
2. 發生異地備援容錯移轉事件。 若要繼續，必須有人更新分區對應中任何和所有分區的伺服器名稱、資料庫名稱及/或分區對應詳細資料。 在異地複寫容錯移轉中，這類復原邏輯應該在容錯移轉工作流程內自動化。 
3. 分區或 ShardMapManager 資料庫會還原到較早的時間點。 
 
自動化修復動作可為異地備援的資料庫啟用順暢的管理能力，並避免人工的動作。 它也有助於不小心刪除資料時的復原案例。 

如需 Azure SQL Database 彈性資料庫工具、異地複寫和還原的詳細資訊，請參閱下列： 

* [Azure SQL Database 的彈性資料庫功能](sql-database-elastic-scale-introduction.md) 
* [Azure SQL Database 商務持續性](sql-database-business-continuity.md) 
* [開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md)  
* [ShardMap 管理](sql-database-elastic-scale-shard-map-management.md)

## 從 ShardMapManager 擷取 RecoveryManager 

第一個步驟是建立 RecoveryManager 執行個體。  [GetRecoveryManager 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) 會傳回目前的復原管理員 [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) 執行個體。 為了解決分區對應中的任何不一致，您必須先擷取特定分區對應的 RecoveryManager。 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

在此範例中，RecoveryManager 是從 ShardMapManager 初始化。 ShardMapManager 包含也已經初始化的 ShardMap。 

由於此應用程式程式碼會操作分區對應本身，在 Factory 方法中使用的認證 (上述範例中的 smmConnectionString)，應該是在連接字串所參考的 GSM 資料庫上具有唯讀權限的認證。 這些認證通常不同於用來對資料獨立路由開啟連接的認證。 如需詳細資訊，請參閱 [中彈性資料庫用戶端使用認證](sql-database-elastic-scale-manage-credentials.md)。

## 刪除分區之後從 ShardMap 移除分區

 [DetachShard 方法](https://msdn.microsoft.com/library/azure/dn842083.aspx) 卸離給定的分區從分區對應，並刪除與分區相關聯的對應。  

* location 參數是分區位置，特別是要卸離的分區的伺服器名稱和資料庫名稱。 
* shardMapName 參數是分區對應名稱。 只有在多個分區對應是由相同的分區對應管理員管理時才為必要。 選用。 

**重要**︰ 使用這項技術僅更新的對應如果您確定的範圍是空的。 上述方法並不會檢查要移動的資料範圍，因此您最好在程式碼中納入檢查。

下列範例將使用 RecoveryManager 移除分區從分區對應。分區對應會反映在分區的刪除前 GSM 中的分區位置。 因為已刪除分區，會假設這是特意的，而且分區化索引鍵範圍已不再使用中。 如果不是如此，您可以執行還原時間點，以從較早的時間點復原分區。 (在此情況下，請檢閱下一節來偵測分區不一致的情形。)由於假設刪除資料庫是在預期中，最終的系統管理清除動作是刪除分區對應管理員中分區的項目。 這可避免應用程式不小心將資訊寫入至未預期的範圍。
    
    rm.DetachShard(s.Location, customerMap); 

## 偵測對應的差異 

 [DetectMappingDifferences 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) 選取並傳回其中一個分區對應 （本機或全域） 的真實來源為調解這兩個分區對應 （GSM 和 LSM） 上的對應。

    rm.DetectMappingDifferences(location, shardMapName);

*  *位置* 參數是分區位置，特別是伺服器名稱和資料庫名稱的分區。 
*  *ShardMapName* 參數是分區對應名稱。 只有在多個分區對應是由相同的分區對應管理員管理時才為必要。 選用。 

## 解決對應的差異

 [ResolveMappingDifferences 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) 選取其中一個分區對應 （本機或全域） 作為事實來源，並調解這兩個分區對應 （GSM 和 LSM） 上的對應。

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution);
   
*  *RecoveryToken* 參數列舉之間 GSM 和 LSM 針對特定的分區對應中的差異。 

*  [MappingDifferenceResolution 列舉](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) 用來表示的方法來解析分區對應的差異。 **MappingDifferenceResolution.KeepShardMapping** 建議的 LSM 包含正確的對應，因此應該用在分區對應。 這通常是因為發生容錯移轉：分區現在位於新的伺服器上。 由於必須先從 GSM 中移除分區 (使用 RecoveryManager.DetachShard 方法)，GSM 上不再存在對應。 因此，LSM 必須用來重新建立分區對應。

## 在還原分區之後將分區附加至 ShardMap 

 [AttachShard 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) 附加給定的分區是分區對應。 然後它會偵測任何分區對應不一致，並更新對應以符合分區還原時間點的分區。 假設資料庫也會重新命名以反映原始資料庫名稱 (在還原分區之前)，因為還原時間點預設值為加上時間戳記的新資料庫。 

    rm.AttachShard(location, shardMapName) 

*  *位置* 參數是伺服器名稱和資料庫名稱，所附加的分區。 

*  *ShardMapName* 參數是分區對應名稱。 只有在多個分區對應是由相同的分區對應管理員管理時才為必要。 選用。 

此範例會將分區加入最近從較早還原時間點的分區對應。 因為已還原分區 (也就是 LSM 中的分區對應)，該分區可能會與 GSM 中的分區項目不一致。 在這個範例程式碼之外，分區已還原並重新命名為資料庫的原始名稱。 因為它已還原，就會假設 LSM 中的對應為受信任的對應。 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## 在分區的異地複寫容錯移轉 (還原) 之後更新分區位置

發生異地複寫容錯移轉時，會讓次要資料庫可供寫入存取，並成為新的主要資料庫。 伺服器的名稱和可能的資料庫 (根據您的設定而定)，可能會將原始主要複本的不同。 因此，必須修正 GSM 和 LSM 分區的對應項目。 同樣地，如果資料庫還原至不同的名稱或位置，或到較早的時間點，這可能會在分區對應中造成不一致。 分區對應管理員會處理開啟連接到正確資料庫的散發。 分佈會根據分區對應中的資料和應用程式要求之目標的分區化索引鍵的值。 異地複寫容錯移轉之後，必須以正確的伺服器名稱、資料庫名稱和修復資料庫的分區對應更新這項資訊。 

## 最佳作法

異地複寫容錯移轉和復原是一般由應用程式的雲端系統管理員管理的作業，刻意利用 Azure SQL Database 其中一個商務持續性功能。 商務持續性計劃需要處理程序、程序和措施以確保商務運作能持續而不會中斷。 應該在此工作流程中使用隨著 RecoveryManager 類別提供的方法，以確保根據採取的修復動作，GSM 和 LSM 都處於最新狀態。 在容錯移轉事件後，要正確確保 GSM 和 LSM 反映正確資訊有 5 個基本步驟。 執行這些步驟的應用程式程式碼可以整合至現有的工具和工作流程。 

1. 從 ShardMapManager 擷取復原管理員。 
2. 從分區對應卸離舊分區。
3. 將新的分區附加至分區對應，包括新的分區位置。
4. 在 GSM 和 LSM 之間的對應中偵測到不一致。 
5. 解決 GSM 和 LSM 之間的差異，信任 LSM。 

此範例會執行下列步驟 ︰
1. 從分區對應移除分區，其反映分區在容錯移轉事件之前的位置。
2. 將分區附加至分區對應會反映新分區位置 (參數 "Configuration.SecondaryServer" 是是新伺服器名稱，但是相同的資料庫名稱)。
3. 透過偵測每個分區的 GSM 與 LSM 之間的對應差異來擷取復原權杖。 
4. 透過信任來自每個分區 LSM 的對應，即可解決不一致情形。 

    var 分區 = smm。GetShards() 
    foreach （分區的分區中） 
    { 
     如果 (s.Location.Server Configuration.PrimaryServer = =) 
         { 
          ShardLocation slNew = 新 ShardLocation Configuration.SecondaryServer (s.Location.Database）; 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g,                      MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 

