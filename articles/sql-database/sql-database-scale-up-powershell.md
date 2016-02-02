<properties 
    pageTitle="使用 PowerShell 變更 Azure SQL Database 的服務層級和效能等級" 
    description="變更 Azure SQL Database 的服務層級和效能等級說明如何使用 PowerShell 相應增加或減少您的 SQL Database。使用 PowerShell 變更 Azure SQL Database 的定價層。" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="12/01/2015"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# 使用 PowerShell 變更 SQL Database 的服務層級級和效能等級 (定價層)

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)



本文將說明如何使用 PowerShell 變更您的 SQL Database 的服務層級和效能等級。

使用中的資訊 [SQL Database Web/Business 資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md) 和 [Azure SQL Database 服務層和效能層級](sql-database-service-tiers.md) Azure SQL 資料庫判斷適當的服務層和效能層級。
> [AZURE.IMPORTANT] 變更 SQL Database 的服務層級和效能等級是線上作業。 這表示您的資料庫在整個作業過程中，將維持在線上可供使用的狀態而不需停機。

- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。
- 升級的資料庫時 [標準異地複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx) 或 [作用中地理複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx) 啟用，您必須先升級次要資料庫所需的效能層然後再升級主要資料庫。
- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。 您可以依照所述的步驟 [終止連續複製關聯性](https://msdn.microsoft.com/library/azure/dn741323.aspx) 停止主要資料庫與作用中次要資料庫之間的複寫程序主題。
- 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](https://msdn.microsoft.com/library/azure/jj650016.aspx)。
- 您可以在 24 小時期間內，變更最多四個個別的資料庫 (服務層或效能等級)。
- 完成變更之前，不會將新屬性套用至資料庫。



**若要完成本文，您需要下列項目：**

- Azure 訂用帳戶。 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]****，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟: [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。


若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

    Add-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID 或訂用帳戶名稱 (**-SubscriptionName**)。 您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。 當您的訂用帳戶執行了以下 Cmdlet 之後：

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

成功執行 **Select-AzureSubscription** 之後，您會返回 PowerShell 提示字元。 如果您有一個以上的訂用帳戶，您可以執行 **Get-AzureSubscription** 並確認您要使用的訂用帳戶顯示 **IsCurrent: True**。





## 變更您的 SQL Database 的服務層級和效能等級

執行 **Set-AzureRmSqlDatabase** Cmdlet 並將 **-RequestedServiceObjectiveName** 設為想要之定價層的效能等級；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*、...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier

## 變更您的 SQL Database 之服務層級和效能等級的範例 PowerShell 指令碼

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest

## 後續步驟

- [相應放大和縮小](sql-database-elastic-scale-get-started.md)
- [連接並查詢 SQL 資料庫使用 SSMS](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL database](sql-database-export-powershell.md)

## 其他資源

- [業務持續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫的文件](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt163521.aspx)




