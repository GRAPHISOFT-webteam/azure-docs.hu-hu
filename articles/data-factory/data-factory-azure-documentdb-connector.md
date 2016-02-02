<properties 
    pageTitle="從 DocumentDB 來回移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從 Azure DocumentDB 集合來回移動資料。" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/15/2015" 
    ms.author="spelluru"/>


# 使用 Azure Data Factory 從 DocumentDB 來回移動資料

本文概述如何在 Azure Data Factory 中使用複製活動，在 Azure DocumentDB 與其他資料存放區之間移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

## 範例：從 DocumentDB 複製資料到 Azure Blob

下列範例顯示：

1. 連結的服務型別的 [DocumentDb](#azure-documentdb-linked-service-properties)。
2. 連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3. 輸入 [資料集](data-factory-create-datasets.md) 型別的 [DocumentDbCollection](#azure-documentdb-dataset-type-properties)。
4. An output [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此範例會將 Azure DocumentDB 中的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure DocumentDB 連結服務：**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure DocumentDB 輸入資料集：**

此範例假設您在 Azure DocumentDB 資料庫中擁有名為 **Person** 的集合。

設定 “external”: ”true” 和指定 externalData 原則即可通知 Data Factory 服務：這是 Azure Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure Blob 輸出資料集：**

資料會每小時利用可反映特定日期時間及小時資料粒度的 Blob 路徑，複製到新的 Blob。

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

DocumentDB 資料庫中 Person 集合中的範例 JSON 文件：

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB 支援在階層式 JSON 文件上使用類似 SQL 的語法來查詢文件。

範例： 
    選取 Person.PersonId，Person.Name.First AS FirstName，Person.Name.Middle as MiddleName，Person.Name.Last AS LastName 從人員

下列管線會將資料從 DocumentDB 資料庫中的 Person 集合複製到 Azure Blob。 複製活動中已指定為輸入和輸出資料集。

    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## 範例：從 Azure Blob 複製資料到 Azure DocumentDB

下列範例顯示：

1. 連結的服務型別的 [DocumentDb](#azure-documentdb-linked-service-properties)。
2. 連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3. An input [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. 輸出 [資料集](data-factory-create-datasets.md) 型別的 [DocumentDbCollection](#azure-documentdb-dataset-type-properties)。
4. A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties)。


此範例會從 Azure Blob 複製資料到 Azure DocumentDB。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure Blob 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure DocumentDB 連結服務：**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob 輸入資料集：**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure DocumentDB 輸出資料集：**

此範例會將資料複製到名為 "Person" 的集合。

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

下列管線會將資料從 Azure Blob 複製到 DocumentDB 中的 Person 集合。 複製活動中已指定為輸入和輸出資料集。

    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }

如果範例 Blob 輸入為

    1,John,,Doe

則 DocumentDB 中的輸出 JSON 會是：

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }

DocumentDB 是 JSON 文件的 NoSQL 存放區 (允許巢狀結構)。 Azure Data Factory 可讓使用者直接表示透過階層 **nestingSeparator**, ，也就是 「。 」 在此範例。 使用分隔符號，複製活動將會根據資料表定義中的 “Name.First”、“Name.Middle” 和 “Name.Last”，產生含有三個子元素 (First、Middle 和 Last) 的 "Name" 物件。

## Azure DocumentDB 連結服務屬性

下表提供 Azure DocumentDB 連結服務專屬 JSON 元素的描述。

| **屬性**| **說明**| **必要**|
| -------- | ----------- | --------- |
| 類型| 類型屬性必須設為：**DocumentDb**| 是|
| connectionString| 指定連接到 DocumentDB 資料庫所需的資訊。| 是|

## Azure DocumentDB 資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 **DocumentDbCollection** 類型資料集的 typeProperties 區段具有下列屬性。

| **屬性**| **說明**| **必要**|
| -------- | ----------- | -------- |
| collectionName| DocumentDB 文件集合的名稱。| 是|


範例：

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

## Azure DocumentDB 複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

**附註：**複製活動只會採用一個輸入，而且只產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動類型的來源時 **DocumentDbCollectionSource**
下列屬性可用於 **typeProperties** 區段:

| **屬性**| **說明**| **允許的值**| **必要**|
| ------------ | --------------- | ------------------ | ------------ |
| query| 指定查詢來讀取資料。| DocumentDB 所支援的查詢字串。<p>範例: SELECT c.BusinessEntityID，c.PersonType，c.NameStyle，c.Title，c.Name.First AS FirstName，c.Name.Last AS LastName，c.Suffix，c.EmailPromotion FROM c 其中 c.ModifiedDate > \"2009-01-01T00:00:00\ 」</p>| 不 <p>如果未指定，執行 SQL 陳述式: 選取 <columns defined in structure> 從 mycollection </p>
| nestingSeparator| 用來表示文件為巢狀文件的特殊字元| 任何字元。<p>DocumentDB 是 NoSQL 存放區 JSON 文件，允許巢狀的結構的情況。Azure Data Factory 可讓使用者透過 nestingSeparator (也就是上述範例中的 “.”) 表示階層。使用分隔符號，複製活動將會產生含有三個子元素的"Name"物件第一，中間名和姓氏，根據"Name.First"、"Name.Middle"和"Name.Last"，資料表定義中的。</p>| 否

**DocumentDbCollectionSink** 支援下列屬性：

| **屬性**| **說明**| **允許的值**| **必要**|
| -------- | ----------- | -------------- | -------- |
| nestingSeparator| 來源資料行名稱中用來表示需要巢狀文件的特殊字元。<p>上面範例: 輸出資料表中的 Name.First 會產生下列 JSON 結構 DocumentDB 文件中:</p><p>"Name": {<br/>  "First":"John"<br/>}，</p>| 用來分隔巢狀層級的字元。<p>是預設值。(點)。</p>| 用來分隔巢狀層級的字元。<p>預設值為。(點)。</p>| 否|
| writeBatchSize| DocumentDB 服務來建立文件的平行要求數目。<p>使用這個屬性從 DocumentDB 來回複製資料時，您可進行微調效能。增加 writeBatchSize 時，您可預期有更好的效能，因為對 DocumentDB 傳送了更多的平行要求。不過，您要避免節流，可能會擲回的錯誤訊息: 「 要求速率很高 」。</p><p>包括文件，文件中的詞彙數目的大小、 索引編製原則的目標集合等，節流由許多因素，來決定。複製作業，您可以使用更好的集合 (例如 S3) 具有最多可用輸送量 (2500 要求單位/秒)。</p>| 整數值| 否|
| writeBatchTimeout| 在逾時前等待作業完成的時間。| (單位 = 時間範圍) 範例：“00:30:00” (30 分鐘)。| 否|

## 附錄

1. **問：** 
    不會更新現有記錄的複製活動支援嗎?

    **答：** 
    號

2. **問：** 
    重試複製到 DocumentDB 如何處理與已複製的記錄?

    **答：** 
    如果記錄的 [ID] 欄位，複製作業會嘗試插入具有相同識別碼的記錄，複製作業會擲回錯誤。

3. **問：**
    Data Factory 支援 [範圍或雜湊為基礎的資料分割](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)嗎?

    **答：**
    號
4. **問：**
    可以指定多個 DocumentDB 集合的資料表嗎?

    **答：**
    號 此時，可以指定只有一個集合。






