<properties 
    pageTitle="使用彈性資料庫集區相應放大資源 | Microsoft Azure" 
    description="了解如何藉由建立彈性資料庫集區來管理多個資料庫，進而使用 PowerShell 來相應放大Azure SQL Database 資源。" 
    keywords="multiple databases,scale-out"    
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="adamkr; sstein"/>

# 使用 PowerShell 建立彈性資料庫集區，以相應放大多個 SQL Database 的資源 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

了解如何建立管理多個資料庫 [彈性資料庫集區](sql-database-elastic-pool.md) 使用 PowerShell cmdlet。 

> [AZURE.NOTE] 彈性資料庫集區目前的預覽版，且僅能搭配 SQL Database V12 伺服器。 如果您有 SQL Database V11 伺服器可以 [使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server.md) 一次。

彈性資料庫集區可讓您相應放大多個 SQL Database 的資料庫資源和管理。

本文說明如何建立所需的一切 (包括 V12 伺服器) 以建立和設定彈性資料庫集區，但建立 Azure 訂用帳戶除外。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。




為了清楚起見，我們已將使用 Azure PowerShell 建立彈性資料庫集區的流程細分為以下步驟，並逐一說明。 對於只需要簡單的命令清單，請參閱 **融會貫通** 本文最後一節。


若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。




## 設定您的認證並選取您的訂用帳戶

既然您已在執行 Azure 資源管理員模組，便可以存取建立和設定彈性資料庫集區所需的所有必要 Cmdlet。 首先必須建立對您 Azure 帳戶的存取權。 執行以下項目，然後您會看到要輸入認證的登入畫面。 請使用與登入 Azure 入口網站相同的電子郵件和密碼。

    Add-AzureRmAccount

成功登入後，您應該會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取的訂閱中，您需要訂用帳戶識別碼或訂用帳戶名稱 (**-SubscriptionName**)。 您可以複製上一個步驟中，或如果您有多個訂閱執行 **Get-azuresubscription** 指令程式並複製結果集中所需的訂閱資訊。 當您的訂用帳戶執行了以下 Cmdlet 之後：

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## 建立資源群組、伺服器和防火牆規則

現在您有權在您的 Azure 訂用帳戶下執行 Cmdlet，因此下一步是建立含有伺服器的資源群組，以在伺服器中建立含有多個資料庫的彈性資料庫集區。 為了使用您選擇的任何有效位置，您可以編輯下一個命令。 執行 **(Get AzureRmLocation | where-object {$_。名稱-eq"Microsoft.Sql/servers"})。位置** 以取得有效位置清單。

如果您已經有資源群組，可以前往下一個步驟，或執行以下命令來建立新的資源群組：

    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"

### 建立伺服器 

彈性資料庫集區會建立在 Azure SQL Database 伺服器內。 如果您已經有此伺服器可以前往下一個步驟中，或者您可以執行 [新增 AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet 來建立新的 V12 伺服器。 用您的伺服器名稱取代 ServerName。 這必須是所有 Azure SQL Server 的唯一名稱，因此伺服器名稱若被佔用，您就會收到錯誤訊息。 另外值得注意的是，此命令可能需要數分鐘才能完成。 成功建立伺服器後，會出現伺服器詳細資料和 PowerShell 提示字元。 您可以編輯您選擇的命令來使用任何有效位置。

    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

當您執行此命令] 視窗隨即開啟向您詢問 **使用者名稱** 和 **密碼**。 這不是您的 Azure 認證、 輸入使用者名稱和您想要建立新的伺服器系統管理員認證的密碼。  


### 設定伺服器防火牆規則以允許存取伺服器

建立可存取伺服器的防火牆規則。 執行 [新增 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586.aspx) 命令將開始和結束 IP 位址，以有效的值，為您的電腦。

如果您的伺服器需要允許存取其他 Azure 服務，請加入 **-AllowAllAzureIPs** 加入特殊的防火牆規則，並允許所有 azure 流量存取伺服器的參數。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。


## 建立彈性資料庫集區和彈性資料庫

現在您已擁有資源群組、伺服器和設定完成的防火牆規則，便可以存取伺服器。  [新增 AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cmdlet 會建立彈性資料庫集區。 此命令建立的集區會共用總共 400 個 eDTU。 集區中的每個資料庫保證一律都有 10 個 eDTU 可用 (DatabaseDtuMin)。 集區中的個別資料庫可耗用最多 100 個 eDTU (DatabaseDtuMax)。 如需的參數的詳細說明，請參閱 [Azure SQL Database 彈性集區](sql-database-elastic-pool.md)。 


    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### 建立或新增彈性資料庫至彈性資料庫集區

在上一步中建立的集區是空的，其中沒有彈性資料庫存在。 以下各節說明如何在集區內建立新的彈性資料庫，以及如何將現有的資料庫加入至集區。

*建立集區後您也可以使用 TRANSACT-SQL 建立新的彈性資料庫集區，及將現有的資料庫移入和移出集區。 如需詳細資訊，請參閱 [彈性資料庫集區參考 TRANSACT-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)。*

### 在彈性資料庫集區內建立新的彈性資料庫

若要建立新的資料庫，直接在集區，請使用 [新增 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) 指令程式，並設定 **ElasticPoolName** 參數。


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### 將現有資料庫移入彈性資料庫集區

若要將現有的資料庫移到集區中，使用 [組 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) 指令程式，並設定 **ElasticPoolName** 參數。 


這裡為了示範，建立了不在彈性資料庫集區中的資料庫。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

將這個現有資料庫移入彈性資料庫集區。

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 變更彈性資料庫集區的效能設定


    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 監視彈性資料庫和彈性資料庫集區
彈性資料庫集區提供計量報告，可協助您相應放大工作量以管理多個資料庫。


### 取得彈性資料庫集區作業的狀態

您可以追蹤彈性資料庫集區作業的狀態，包括建立和更新作業。 

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### 取得將彈性資料庫移入和移出彈性資料庫集區的狀態

    Get-AzureRmSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### 取得彈性資料庫集區的資源消耗度量

可以用資源集區限制的百分比來擷取的度量：   

* 平均 CPU 使用率: cpu_percent 
* 平均 IO 使用率：data_io_percent 
* 平均記錄檔使用率：log_write_percent 
* 平均記憶體使用率：memory_percent 
* 平均 eDTU 使用量 (做為 CPU/IO/記錄檔使用率的最大值) - DTU_percent 
* 並行使用者要求 (背景工作) 的數目上限：max_concurrent_requests 
* 並行使用者工作階段的數目上限：max_concurrent_sessions 
* 彈性集區的總儲存體大小：storage_in_megabytes 


度量資料粒度/保留期限：

* 系統會以 5 分鐘的資料粒度傳回資料。  
* 資料會保留 14 天。  


此 Cmdlet 和 API 會將一次呼叫中可擷取的資料列限制為 1,000 個 (大約是 3 天份且資料粒度為 5 分鐘的資料)。 但可以用不同的開始/結束時間間隔來多次呼叫此命令，以擷取更多資料。 


擷取度量：

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

重複呼叫並附加資料來取得其他日期：

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
格式化資料表：

    $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### 取得彈性資料庫的資源消耗度量

除了以下的語意差異外，這些 API 與目前用於監視獨立資料庫之資源使用率的 (V12) API 相同。 

* 這個擷取的 API 度量，會以針對該彈性資料庫集區所設定之每個 databaseDtuMax (或是 CPU、IO 等基礎度量的相等上限) 的百分比來表示。 例如，這些度量有其中一項的使用率為 50%，則表示特定資源的消耗量佔該資源在父彈性資料庫集區中，每個資料庫上限限制的 50%。 

取得度量：

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

視需要重複呼叫並附加資料，來取得其他日期：

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

格式化資料表：

    $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## 總整理


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 後續步驟

建立彈性資料庫集區後，可以藉由建立彈性工作來管理集區中的彈性資料庫。 彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。 如需詳細資訊，請參閱 [彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。


## 彈性資料庫參考

如需關於彈性資料庫和彈性資料庫集區，包括 API 和錯誤的詳細資訊，請參閱 [彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。
