<properties 
    pageTitle="從 Azure SQL 來回移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從 Azure SQL 資料庫來回移動資料。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Azure SQL 來回移動資料

本文概述如何在 Azure Data Factory 中使用複製活動，在 Azure SQL 與其他資料存放區之間移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

## 範例：從 Azure SQL 複製資料到 Azure Blob

下列範例顯示：

1. 連結的服務型別的 [AzureSqlDatabase](#azure-sql-linked-service-properties)。
2. 連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。 
3. 輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureSqlTable](#azure-sql-dataset-type-properties)。 
4. 輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4. A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [SqlSource](#azure-sql-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此範例會每小時將屬於時間序列的資料從 Azure SQL Database 中的資料表複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。  

**Azure SQL 連結服務**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

請參閱 [Azure SQL 連結服務](#azure-sql-linked-service-properties) 此連結服務所支援的屬性清單一節。 

**Azure Blob 儲存體連結服務**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

請參閱 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 清單的發行項，此連結服務支援的屬性。 

**Azure SQL 輸入資料集**

此範例假設您已在 SQL Azure 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 

設定 “external”: ”true” 和指定 externalData 原則即可通知 Azure Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

請參閱 [Azure SQL 資料集類型屬性](#azure-sql-dataset-type-properties) 支援此資料集類型的屬性清單一節。  

**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%H"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

請參閱 [Azure Blob 資料集類型屬性](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 支援此資料集類型的屬性清單一節。  

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **SqlSource** 和 **接收器** 類型設為 **BlobSink**。 針對指定的 SQL 查詢 **SqlReaderQuery** 屬性選取過去一小時內要複製的資料。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

> [AZURE.NOTE] 在上述範例中， **sqlReaderQuery** SqlSource 指定。 複製活動會針對 Azure SQL Database 來源執行這項查詢以取得資料。
>  
> 或者，您可以指定預存程序，藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** （如果預存程序不接受參數）。
>  
> 如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 Azure SQL Database 執行 (從 mytable 選取 column1、column2)。 如果資料集定義沒有結構，則會從資料表中選取所有資料行。 


請參閱 [Sql 來源](#sqlsource) 區段和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 支援 SqlSource 和 BlobSink 的屬性清單。 


## 範例：從 Azure Blob 複製資料到 Azure SQL

下列範例顯示：

1.  連結的服務型別的 [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)。
4.  A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)。

此範例會每小時將屬於時間序列的資料從 Azure Blob 複製到 Azure SQL Database 中的資料表。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 


**Azure SQL 連結服務**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

請參閱 [Azure SQL 連結服務](#azure-sql-linked-service-properties) 此連結服務所支援的屬性清單一節。 

**Azure Blob 儲存體連結服務**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

請參閱 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 清單的發行項，此連結服務支援的屬性。

**Azure Blob 輸入資料集**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 年、 月和日的開始時間的部分，會使用的資料夾路徑和檔案名稱使用開始時間的小時部分。 "external":"true"設定會通知 Data Factory 服務，此資料表是 data factory 外部的並不是由 data factory 中的活動所產生。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%H"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

請參閱 [Azure Blob 資料集類型屬性](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 支援此資料集類型的屬性清單一節。

**Azure SQL 輸出資料集**

此範例會將資料複製到 Azure SQL 中名為 "MyTable" 的資料表。 您應該在Azure SQL 中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。 此資料表會每小時加入新的資料列。 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

請參閱 [Azure SQL 資料集類型屬性](#azure-sql-dataset-type-properties) 支援此資料集類型的屬性清單一節。

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **BlobSource** 和 **接收器** 類型設為 **SqlSink**。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

請參閱 [Sql 接收器](#sqlsink) 區段和 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) SqlSink 和 BlobSource 所支援的屬性清單。 


## Azure SQL 連結服務屬性

下表提供 Azure SQL 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | type 屬性必須設為：AzureSqlDatabase | 是 |
| connectionString | 針對 connectionString 屬性指定連接到 Azure SQL Database 執行個體所需的資訊。 | 是 |

**注意 ︰** 您需要設定 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 您需要將資料庫伺服器設定成 [允許 Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果您要從 Azure 外部 (包括從具有 Fata Factory 閘道器的內部部署資料來源) 將資料複製到 Azure SQL，則必須為傳送資料到 Azure SQL 的機器設定適當的 IP 位址範圍。 

## Azure SQL 資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。 

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。  **TypeProperties** 類型資料集區段 **AzureSqlTable** 具有下列屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| tableName | Azure SQL Database 執行個體中連結服務所參照的資料表名稱。 | 是 |

## Azure SQL 複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

> [AZURE.NOTE] 複製活動會採用一個輸入，而且只產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

### SqlSource

在複製活動類型的來源時 **SqlSource** 下列屬性可用於 **typeProperties** 區段 ︰

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 使用自訂查詢來讀取資料。 | SQL 查詢字串。例如：select * from MyTable。 如果未指定，執行的 SQL 陳述式：select from MyTable。 | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 | 預存程序的名稱。 | 否 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |

如果 **sqlReaderQuery** 指定 SqlSource，複製活動會針對 Azure SQL 資料庫的來源取得的資料來執行此查詢。 

或者，您可以指定預存程序，藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** （如果預存程序不接受參數）。 

如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 Azure SQL Database 執行 (從 mytable 選取 column1、column2)。 如果資料集定義沒有結構，則會從資料表中選取所有資料行。 

### SqlSource 範例

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**預存程序定義：** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### SqlSink 

**SqlSink** 支援下列屬性 ︰

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。 | (單位 = 時間範圍) 範例：“00:30:00” (30 分鐘)。 | 否 | 
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中 | 整數。 (單位 = 資料列計數) | 否 (預設值 = 10000)
| sqlWriterCleanupScript | 使用者指定了可供複製活動執行的查詢，以便清除特定配量的資料。 如需詳細資訊，請參閱下面「重複性」一節。 | 查詢陳述式。  | 否 |
| sliceIdentifierColumnName | 使用者指定了可供複製活動使用自動產生的配量識別碼填入的資料行名稱，在重新執行時將用來清除特定配量的資料。 如需詳細資訊，請參閱下面「重複性」一節。 | 資料類型為 binary(32) 之資料行的資料行名稱。 | 否 |
| sqlWriterStoredProcedureName | 將資料更新插入 (更新/插入) 目標資料表中的預存程序名稱。 | 預存程序的名稱。 | 否 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 | 
| sqlWriterTableType | 使用者指定了要用於上述預存程序中的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 | 資料表類型名稱。 | 否 |

#### SqlSink 範例

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### SQL Server 和 Azure SQL 的類型對應

中所述 [資料移動活動](data-factory-data-movement-activities.md) 文章複製活動會執行轉換成接收類型具有下列 2 個步驟的方法的來源類型的自動類型轉換的自動類型轉換 ︰

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Azure SQL、SQL Server、Sybase 來回移動資料時，將使用下列從 SQL 類型到 .NET 類型的對應，反之亦然。

此對應與 ADO.NET 的 SQL Server 資料類型對應相同。

| SQL Server Database Engine 類型 | .NET Framework 類型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte] |
| bit | Boolean |
| char | 字串，Char] |
| 日期 | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 十進位 | 十進位 |
| FILESTREAM 屬性 (varbinary(max)) | Byte] |
| Float | 兩倍 |
| image | Byte] | 
| int | Int32 | 
| money | 十進位 |
| nchar | 字串，Char] |
| ntext | 字串，Char] |
| numeric | 十進位 |
| nvarchar | 字串，Char] |
| real | 單一 |
| rowversion | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 十進位 | 
| sql_variant | 物件 * |
| 文字 | 字串，Char] |
| 分析 | TimeSpan |
| timestamp | Byte] |
| tinyint | 位元組 |
| uniqueidentifier | Guid |
| varbinary |  Byte] |
| varchar | 字串，Char] |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]




     











