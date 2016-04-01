<properties
   pageTitle="如何使用 SSMS 連接到 Azure SQL Database | Microsoft Azure"
   description="了解如何使用 SSMS 連接到 Azure SQL Database。"
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jeffreyg"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# 連接 SQL Server Management Studio (SSMS)

使用下列步驟以利用 SQL Server Management Studio (SSMS) 連接和查詢您的 SQL Database。

## 必要條件

* SQL Server Management Studio (SSMS)-若要下載最新版的 SSMS，請參閱 [下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
* AdventureWorks 範例資料庫中所述 [開始使用 Microsoft Azure SQL Database](sql-database-get-started.md)。


## 取得您的完整 Azure SQL 伺服器名稱

若要連接到您的資料庫中，您需要伺服器的完整名稱 (***servername**。.database.windows.net*)，其中包含您想要連接到的資料庫。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想連接的資料庫。
3. 找出完整的伺服器名稱：

    ![完整伺服器名稱][6]

    在步驟 3 中使用完整的伺服器名稱。



## 連接到您的 SQL Database

1. 開啟 SSMS。
2. 按一下 [ **連接** > **Database Engine...**

    ![[連接] > [資料庫引擎]][7]

2. 在 **連接到伺服器** 對話方塊的 **伺服器名稱** 方塊中，輸入伺服器名稱的格式 *& lt; 伺服器名稱 >*。**.database.windows.net**。
3. 在 **驗證** 清單中，選取 **SQL Server 驗證**。
4. 輸入 **登入** 和 **密碼** 您指定當您建立 SQL 資料庫伺服器，然後按一下 [ **連接**。

    ![[連接至伺服器] 對話方塊][2]



### 如果連接失敗
確定您建立之邏輯伺服器的防火牆允許來自本機電腦的連線。 如需詳細資訊，請參閱 [How to ︰ 在 SQL 資料庫上設定防火牆設定](sql-database-configure-firewall-settings.md)。

## 執行範例查詢

1. 在 **物件總管] 中**, ，瀏覽至 **AdventureWorks** 資料庫。
2. 以滑鼠右鍵按一下資料庫，然後選取 **新查詢**。

    ![新增查詢][4]

3. 在查詢視窗中，複製並貼上下列程式碼：

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. 按一下 [ **Execute** ] 按鈕。  下列螢幕擷取畫面顯示成功的查詢。

    ![成功][5]




## 後續步驟
您可以使用 Transact-SQL 陳述式來建立或管理資料庫。 如需詳細資訊，請參閱 [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 和 [管理 Azure SQL Database 使用 SQL Server Management Studio](sql-database-manage-azure-ssms.md)。 您也可以將事件記錄到 Azure 儲存體。 請參閱 [開始使用 SQL database 稽核](sql-database-auditing-get-started.md) 如需詳細資訊。

<!--Image references-->

[1]:./media/sql-database-connect-to-database/1-download.png
[2]:./media/sql-database-connect-to-database/2-connect.png
[3]:./media/sql-database-connect-to-database/3-connect-to-database.png
[4]:./media/sql-database-connect-to-database/4-run-query.png
[5]:./media/sql-database-connect-to-database/5-success.png
[6]:./media/sql-database-connect-to-database/server-name.png
[7]:./media/sql-database-connect-to-database/connect-dbengine.png


