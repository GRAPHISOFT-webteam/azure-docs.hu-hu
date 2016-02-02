<properties
    pageTitle="使用 SSMS 連接到 SQL Database | Microsoft Azure"
    description="了解如何使用 SQL Server Management Studio (SSMS) 連接到 Azure SQL Database。然後，使用 TRANSACT-SQL (T-SQL) 執行範例查詢。"
    metaCanonical=""
    keywords="connect to sql database,sql server management studio"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor="" />

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/09/2015"
    ms.author="sstein" />


# 使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)


本文說明如何使用 SQL Server Management Studio (SSMS) 連接到 Azure SQL Database，然後使用 Transact-SQL (T-SQL) 陳述式執行簡單查詢。

在 Azure 中您需要先有 SQL Database。 您可以建立一個使用中的指示，快速地 [開始使用 Microsoft Azure SQL Database](sql-database-get-started.md)。 此處的範例是您在該文章中建立的 AdventureWorks 範例資料庫為基礎，但在您執行查詢前，相同的步驟適用於任何的 SQL Database。

## 安裝並啟動 SQL Server Management Studio (SSMS)

使用 SQL Database 時，您應該使用最新版的 SSMS。 請參閱 [下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 來取得它。 在最新版本中，SSMS 會自動在最新的更新可用時通知您。

## 啟動 SSMS 並連接到您的 SQL Database 伺服器

1. 在 Windows 搜尋方塊中輸入 "Microsoft SQL Server Management Studio"，然後按一下桌面應用程式來啟動 SSMS。
2. 在 **連接到伺服器** 對話方塊中，於 **伺服器名稱** 方塊中，輸入裝載 SQL 資料庫格式的伺服器名稱 *< 伺服器名稱 >*。**.database.windows.net**。
3. 從 [驗證]**** 清單中選擇 [SQL Server 驗證]****。
4. 輸入您建立伺服器時設定的 [登入]**** 和 [密碼]****，然後按一下 [連接]**** 來連接到 SQL Database。

    ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-connect-query-ssms/1-connect.png)

### 如果連接到 SQL Database 失敗

最常見的連接失敗原因是伺服器名稱、使用者名稱或密碼錯誤，以及基於安全性理由不允許連接伺服器。 請確定伺服器的防火牆設定允許從本機電腦的 IP 位址和 SSMS 用戶端使用的 IP 位址連接。 它們有時候不同。

如果因為防火牆設定而連線失敗，則最新版的 SSMS 會在詢問後為您建立防火牆規則。 若要取得它，請參閱 [下載 SSMS](https://msdn.microsoft.com/library/mt238290.aspx)。 如果您使用較舊的版本，則會在錯誤訊息中回報 IP 位址，而您需要將此 IP 位址加入至伺服器防火牆規則。 如需詳細資訊，請參閱 [How to: 進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)。

## 執行範例查詢

連接到 SQL Database 之後，您可以執行範例查詢。 如果您未建立使用中的 AdventureWorks 範例資料庫 [開始使用 Microsoft Azure SQL Database](sql-database-get-started.md), ，此查詢將無法運作。 若要深入了解，直接跳到〈後續步驟〉。

1. 在 [物件總管]**** 中，瀏覽至 **AdventureWorks** 資料庫。
2. 在資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]****。

    ![新增查詢](./media/sql-database-connect-query-ssms/4-run-query.png)

3. 在查詢視窗中，複製並貼上下列程式碼。

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. 按一下 [執行]**** 按鈕。 下列螢幕擷取畫面顯示成功的查詢。

    ![成功](./media/sql-database-connect-query-ssms/5-success.png)

## 後續步驟

如同您處理 SQL Server 的方式一樣，您可以使用 T-SQL 陳述式來建立及管理 Azure 中的資料庫。 如果您已熟悉使用 T-SQL 與 SQL Server，請參閱 [Azure SQL Database TRANSACT-SQL 資訊)](sql-database-transact-sql-information.md) 差異的摘要。

如果您是新手 T-SQL，請參閱 [教學課程: 撰寫 TRANSACT-SQL 陳述式](https://msdn.microsoft.com/library/ms365303.aspx) 和 [TRANSACT-SQL 參考 (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx)。





