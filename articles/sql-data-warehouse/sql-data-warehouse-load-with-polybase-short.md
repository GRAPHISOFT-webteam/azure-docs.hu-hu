<properties
   pageTitle="利用 PolyBase 教學課程載入資料 |Microsoft Azure"
   description="了解如何使用 PolyBase 將資料載入 SQL 資料倉儲。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# 使用 PolyBase 載入資料

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

本教學課程將示範如何將資料載入至您的 Azure SQL 資料倉儲使用 PolyBase。  若要深入了解 PolyBase，請參閱 [SQL 資料倉儲教學課程中的 PolyBase][]。


## 必要條件
若要逐步執行本教學課程，您需要

- SQL 資料倉儲資料庫
- Azure 儲存體帳戶


## 步驟 1：建立來源資料檔
開啟 [記事本]，並將下列幾行資料複製到新的檔案。 將此檔案儲存到本機暫存目錄，C:\Temp\DimDate2.txt。

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


## 步驟 2：將資料移轉至 Azure Blob 儲存體

- 下載 [最新版本的 AzCopy][]。
- 開啟命令視窗，並瀏覽至電腦上的 AzCopy 安裝目錄，也就是 AzCopy.exe 的位置。 根據預設，安裝目錄是在 64 位元 Windows 電腦上的 %programfiles(x86) %\Microsoft SDKs\Azure\AzCopy\。
- 執行下列命令以上傳檔案。 指定 /DestKey 的 Azure 儲存體帳戶金鑰。

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

若要深入了解 AzCopy，請參閱 [開始使用 AzCopy 命令列公用程式][]。


## 步驟 3：建立外部資料表

接下來，您必須在 SQL 資料倉儲中建立外部資料表以參考 Azure blob 儲存體中的資料。
若要建立外部資料表，請使用下列步驟：

- [建立主要金鑰][]︰ 加密的密碼。 您的資料庫範圍認證。
- [Create Database Scoped Credential]︰ 若要指定儲存體帳戶的驗證資訊。
- [Create External Data Source]︰ 若要指定 Azure blob 儲存體的位置。
- [Create External File Format]︰ 若要指定的資料配置。
- [Create External Table]︰ 若要參考的 Azure 儲存體資料。


```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (  
        TYPE = Hadoop, 
        LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
        CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
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


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## 步驟 4：將資料載入 SQL 資料倉儲

- 若要將資料載入至新的資料表，執行 [CREATE TABLE AS SELECT (TRANSACT-SQL)][] 陳述式。 新的資料表繼承查詢中指名的資料行。 它會從外部資料表定義繼承這些資料行的資料型別。 
- 若要將資料載入現有的資料表，請使用 INSERT...SELECT 陳述式。  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```


## 步驟 5：為新載入的資料建立統計資料 

Azure 資料倉儲尚未支援自動建立或自動更新統計資料。  為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。  如需統計資料的詳細說明，請參閱 [統計資料][] 開發一組主題中的主題。  以下是快速範例，說明如何在此範例中建立載入資料表的統計資料


```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[latest version of AzCopy]:http://aka.ms/downloadazcopy
[Getting Started with the AzCopy Command-Line Utility]:https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Create External Data Source]:https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Create External File Format]:https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Create External Table]:https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Create Master Key]:https://msdn.microsoft.com/library/ms174382.aspx
[Create Database Scoped Credential]:https://msdn.microsoft.com/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx


<!--Article references-->

[Statistics]: ./sql-data-warehouse-develop-statistics.md


