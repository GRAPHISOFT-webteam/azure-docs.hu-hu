<properties 
    pageTitle="概觀：SQL Database 的管理工具" 
    description="比較管理 Azure SQL Database 的工具和選項" 
    services="sql-database" 
    documentationCenter="" 
    authors="TigerMint" 
    manager="" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/15/2015" 
    ms.author="vinsonyu"/>

# 概觀：SQL Database 的管理工具

本主題將探索並比較管理 SQL 資料庫的工具和選項，讓您可以挑選適合您工作、業務和您自身的正確工具。 要選擇正確的工具就必須考量到您管理多少資料庫、工作本身以及執行工作的頻率。



## Azure 傳統入口網站


 [Azure 傳統入口網站](http://portal.azure.com) 是網頁型傳統入口網站，建立、 更新和刪除資料庫和邏輯伺服器和監視資料庫活動。 如果您剛開始使用 Azure，或管理少量的資料庫，或是需要快速做某件事，那這正是十分適合您的工具。 

如需使用入口網站的深入資訊，請參閱 [使用 Azure 傳統入口網站管理 SQL Database](sql-database-manage-portal.md)。

## Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools


Visual Studio 中的 SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 都是可在您電腦上執行的用戶端工具，並可讓您連接、管理和開發雲端中的資料庫。 如果您的應用程式開發人員熟悉 Visual Studio 或其他整合式的開發環境 (Ide)， [請嘗試在 Visual Studio 中使用 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。 如果您需要 SSDT 中尚未提供的進階 SQL 功能，例如在混合式環境中管理 SQL Server 資料庫，您可以使用 SSMS。

如需有關管理 Azure SQL Database 使用 SSMS， [使用 SSMS 管理 SQL 資料庫](sql-database-manage-azure-ssms.md)


## 命令列工具。

您可以使用命令列工具 (例如 PowerShell) 來管理資料庫和彈性資料庫集區，以及自動化 Azure 資源部署工作。 若您要在生產環境中管理大量資料庫並自動化部署和資源變更，Microsoft 建議使用這項工具。 

如需有關管理 Azure SQL Database 使用命令列工具 [使用 PowerShell 管理 SQL 資料庫](sql-database-command-line-tools.md)
 


