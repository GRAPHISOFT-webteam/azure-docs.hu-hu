<properties
   pageTitle="SQL 資料倉儲中的 PolyBase 教學課程 | Microsoft Azure"
   description="瞭解 PolyBase 是什麼及如何用於資料倉儲案例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="sahaj08;barbkess"/>


# 在 SQL 資料倉儲中使用 PolyBase 載入資料

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

本教學課程示範如何使用 AzCopy 和 PolyBase 將資料載入 SQL 資料倉儲。 課程結束後，您將了解如何：

- 使用 AzCopy 將資料複製到 Azure Blob 儲存體
- 建立資料庫物件以定義外部資料
- 執行 T-SQL 查詢以載入資料

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## 必要條件

若要逐步執行本教學課程，您需要 

- SQL 資料倉儲資料庫。
- 類型為標準本地備援儲存體 (標準 LRS)、標準異地備援儲存體 (標準 GRS) 或標準讀取存取異地備援儲存體 (標準 RAGRS) 的 Azure 儲存體帳戶。
- AzCopy 命令列公用程式。 下載並安裝 [最新版本的 AzCopy][] 和 Microsoft Azure 儲存體工具一併安裝。

    ![Azure 儲存體工具](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## 步驟 1：將範例資料新增至 Azure Blob 儲存體

為了載入資料，我們需要在 Azure Blob 儲存體中放入一些範例資料。 在此步驟中，我們會在 Azure 儲存體 Blob 中填入範例資料。 稍後，我們將使用 PolyBase 將此範例資料載入 SQL 資料倉儲資料庫。

### A. 準備範例文字檔

若要準備範例文字檔：

1. 開啟 [記事本]，並將下列幾行資料複製到新的檔案。 將此檔案儲存到本機暫存目錄 %temp%\DimDate2.txt。

    ```
    20150301,1,3
    20150501,2,4
    20151001,4,2
    20150201,1,3
    20151201,4,2
    20150801,3,1
    20150601,2,4
    20151101,4,2
    20150401,2,4
    20150701,3,1
    20150901,3,1
    20150101,1,3
    ```

### B. 尋找您的 Blob 服務端點

若要尋找您的 Blob 服務端點：

1. 從 Azure 傳統入口網站中選取 **瀏覽** > **儲存體帳戶**。
2. 按一下您要使用的儲存體帳戶。
3. 在儲存體帳戶刀鋒視窗中，按一下 [Blob]。

    ![按一下 Blob](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. 儲存您的 Blob 服務端點 URL，以供稍後使用。

    ![Blob 服務端點](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. 找出您的 Azure 儲存體金鑰

若要找出您的 Azure 儲存體金鑰：

1. 從主畫面上，選取 **瀏覽** > **儲存體帳戶**。
2. 按一下您要使用的儲存體帳戶。
3. 選取 **所有設定** > **存取金鑰**。
4. 按一下複製方塊，將其中一個存取金鑰複製到剪貼簿。

    ![複製 Azure 儲存體金鑰](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. 將範例檔案複製到 Azure Blob 儲存體

若要將資料複製到 Azure Blob 儲存體：

1. 開啟命令提示字元，切換至 AzCopy 安裝目錄。 此命令會切換至 64 位元 Windows 用戶端上的預設安裝目錄。

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. 執行下列命令以上傳檔案。 指定 blob 服務端點的 URL <blob service endpoint URL> 和 < azure_storage_account_key > 您 Azure 儲存體帳戶金鑰。

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

另請參閱 [開始使用 AzCopy 命令列公用程式][]。

### E. 瀏覽 Blob 儲存體容器

若要查看您上傳至 Blob 儲存體的檔案：

1. 回到您的 [Blob 服務] 刀鋒視窗。
2. 在容器中，按兩下 [ **datacontainer**。
3. 若要瀏覽路徑，您的資料，請按一下資料夾 **datedimension** ，您會看到您已上傳的檔案 **DimDate2.txt**。
4. 若要檢視屬性，請按一下 [ **DimDate2.txt**。
5. 請注意，在 [Blob 屬性] 刀鋒視窗中，您可以下載或刪除此檔案。 

    ![檢視 Azure 儲存體 Blob](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## 步驟 2：為範例資料建立外部資料表

本節中，我們會建立外部資料表來定義範例資料。 

PolyBase 使用外部資料表存取 Azure Blob 儲存體或 Hadoop 中的資料。 因為資料不會儲存在 SQL 資料倉儲內，PolyBase 使用資料庫範圍認證來處理外部資料的驗證。 

此步驟中的範例使用這些 Transact-SQL 陳述式建立外部資料表。 

- [建立主要金鑰 (TRANSACT-SQL)][] 來加密的密碼。 您的資料庫範圍認證。
- [建立資料庫範圍認證 (TRANSACT-SQL)][] 指定儲存體帳戶的驗證資訊。
- [建立外部資料來源 (TRANSACT-SQL)][] 指定 Azure blob 儲存體位置。
- [建立外部檔案格式 (TRANSACT-SQL)][] 來指定資料的格式。
- [建立外部資料表 (TRANSACT-SQL)][] 來指定資料表定義和資料的位置。

對 SQL 資料倉儲資料庫執行這個查詢。 它會在 dbo 結構描述中建立指向 Azure Blob 儲存體中 DimDate2.txt 範例資料的外部資料表 (名稱為 DimDate2External)。


```
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- Provide your Azure storage account key. The identity is associated with the credential. -- It is not used for authentication to Azure storage.

CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH 
    IDENTITY = 'user', 
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- Specify the blob service endpoint and the name of the database-scoped credential.

CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (  
    TYPE = Hadoop, 
    LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
); 


-- D: Create an external file format
-- Specify the way the sample data is formatted in the Azure storage blobs.

CREATE EXTERNAL FILE FORMAT TextFile 
WITH (
    FORMAT_TYPE = DelimitedText, 
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify the fields and data types for the table. This needs to match the data
-- in the sample file. Also specify the path to the data from the root directory
-- of the data source.

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL, 
    CalendarQuarter TINYINT NOT NULL, 
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='datedimension/', 
    DATA_SOURCE=AzureStorage, 
    FILE_FORMAT=TextFile
);


-- Run a PolyBase query to verify the external table

SELECT count(*) FROM dbo.DimDate2External;

```

## 步驟 4：將資料載入 SQL 資料倉儲

外部資料表建立好後，您可以將資料載入至新資料表，或將其插入到現有資料表。 

- 若要將資料載入至新的資料表，執行 [CREATE TABLE AS SELECT (TRANSACT-SQL)][] 陳述式。 新資料表會擁有查詢中指名的資料行。 資料行的資料類型會符合外部資料表定義中的資料類型。 
- 若要將資料載入現有的資料表中，使用 [INSERT...選取 (TRANSACT-SQL)][] 陳述式。 

```
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * FROM [dbo].[DimDate2External];
```
    

在 Visual Studio 的 [SQL Server 物件總管] 內，您可以看到外部檔案格式、外部資料來源和 DimDate2External 資料表。

![檢視外部資料表](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## 步驟 5：建立新載入資料的統計資料 

SQL 資料倉儲不會自動建立或自動更新統計資料。 因此，若要達到高查詢效能，請務必在第一次載入後，於每個資料表的每個資料行上建立統計資料。 另外，也請務必在大幅變更資料後更新統計資料。 

本範例會在新的 DimDate2External 資料表上建立單一資料行統計資料。

```
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

若要深入了解，請參閱 [統計資料][]。  


## 後續步驟
請參閱 [PolyBase 指南][] 進一步開發使用 PolyBase 的解決方案時，您應該知道的資訊。

<!--Image references-->
[1]:./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png

<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: sql-data-warehouse-load-with-polybase.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[Statistics]: sql-data-warehouse-develop-statistics.md
[PolyBase guide]: sql-data-warehouse-load-polybase-guide.md
[Getting Started with the AzCopy Command-Line Utility]: ../storage/storage-use-azcopy.md
[latest version of AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx




