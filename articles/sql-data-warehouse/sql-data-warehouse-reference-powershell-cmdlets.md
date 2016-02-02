<properties
   pageTitle="開始使用 SQL 資料倉儲中的 Cmdlet"
   description="使用 PowerShell Cmdlet 暫止和重新啟動 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/15/2015"
   ms.author="twounder;sidneyh;barbkess"/>


# 開始使用 Azure 資料倉儲 Cmdlet 和 REST API

您可使用 Azure PowerShell Cmdlet 或 REST API 管理 SQL 資料倉儲。

針對 **Azure SQL Database** 定義的命令也可用於 **SQL 資料倉儲**。 如需最新的清單，請參閱 [Azure SQL Cmdlet](https://msdn.microsoft.com/library/mt574084.aspx)。 **Suspend-AzureSqlDatabase** 和 **Resume-AzureSqlDatabase** 這兩個 Cmdlet (如下) 是針對 SQL 資料倉儲設計的新增命令。

同樣地，**SQL Azure Database** 的 REST API 也可用於 **SQL 資料倉儲**執行個體。 目前的清單，請參閱 [Azure SQL Database 的作業](https://msdn.microsoft.com/library/azure/dn505719.aspx)。

## 取得和執行 Azure PowerShell Cmdlet

1. 若要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。
2. 如要執行模組，請在開始視窗中鍵入 **Microsoft Azure PowerShell**。
3. 如果您尚未將帳戶加入電腦，請執行下列 Cmdlet。 (如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell]():

        Add-AzureAccount

3. 使用此 Cmdlet 切換模式：

        Switch-AzureMode AzureResourceManager


## Suspend-AzureSqlDatabase

### 範例 1：依名稱在伺服器上暫停資料庫

此範例會暫停「Server01」伺服器上託管的「Database02」資料庫。 此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### 範例 2：暫停資料庫物件

此範例從「ResourceGroup1」資源群組包含的「Server01」伺服器中，擷取「Database02」資料庫。 它將擷取的物件輸送到 **Suspend-AzureSqlDatabase**。 結果就是暫停資料庫。

    $database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
    $resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### 範例 1：依名稱在伺服器上繼續資料庫

此範例會繼續「Server01」伺服器上託管的「Database02」資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

    Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### 範例 2：繼續資料庫物件

此範例會從「ResourceGroup1」資源群組包含的 「Server01」伺服器中，擷取「Database02」資料庫。 物件會被輸送到 **Resume-AzureSqlDatabase**。

    $database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
    $resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

此 Cmdlet 會列出 Azure SQL Databases 的備份還原點。 還原點可用來還原資料庫。
所傳回之物件的屬性如下所示。

 屬性| 說明
---|---
 RestorePointType| DISCRETE/CONTINUOUS。離散還原點說明可還原 Azure SQL Databases 的可能時間點。連續還原點說明可還原 Azure SQL Databases 的最早可能時間點。資料庫可還原至最早時間點之後的任一時間點。
 EarliestRestoreDate| 最早還原時間 (在 restorePointType = CONTINUOUS 時填入)
 RestorePointCreationDate| 備份快照時間 (在 restorePointType = DISCRETE 時填入)

### 範例 1：依名稱在伺服器上擷取資料庫的還原點

此範例會從「ResourceGroup1」資源群組包含的「Server01」伺服器中，擷取「Database02」資料庫的還原點。

    $restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### 範例 2：繼續資料庫物件

此範例會從「ResourceGroup1」資源群組包含的「Server01」伺服器中，擷取「Database02」資料庫。 資料庫物件會被輸送到 **Get-AzureSqlDatabase**，結果就是資料庫的還原點。

    $database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
    $restorePoints = $database | Get-AzureSqlDatabaseRestorePoints

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 Powershell Cmdlet 中的 -ServerName。


## 後續步驟

如需詳細參考資訊，請參閱 [SQL 資料倉儲參考概觀 []][]。









[sql data warehouse reference overview]: sql-data-warehouse-overview-reference.md 
[how to install and configure azure powershell]: ../articles/powershell-install-configure.md 
[gog]: http://google.com/ 
[yah]: http://search.yahoo.com/ 
[msn]: http://search.msn.com/ 

