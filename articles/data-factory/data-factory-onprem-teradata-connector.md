<properties 
    pageTitle="從 Teradata 移動資料 | Azure Data Factory" 
    description="了解 Data Factory 服務的 Teradata 連接器，其可讓您從 Teradata 資料庫移動資料" 
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
    ms.date="11/12/2015" 
    ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Teradata 移動資料

本文將概述如何使用 Azure 資料處理站中的複製活動將資料從 Teradata 移動到另一個資料存放區。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

資料處理站支援透過資料管理閘道器連接至內部部署 Teradata 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文章來了解資料管理閘道器和設定閘道器的逐步指示。 

**注意:** 您需要運用閘道器連接到 Teradata，即使裝載於 Azure IaaS Vm 中。 如果您正嘗試連接到裝載於雲端中的 Teradata 執行個體，您也可以在 IaaS VM 中安裝閘道器執行個體。

資料處理站只支援將資料從 Teradata 移至其他資料存放區，而不支援從其他資料存放區移至 Teradata。

## 安裝 

資料管理閘道器連接到 Teradata 資料庫，您需要安裝 [Teradata 的.NET 資料提供者](http://go.microsoft.com/fwlink/?LinkId=278886) 與資料管理閘道器相同的系統上。

### 範例：從 Teradata 複製資料到 Azure Blob

下列範例顯示：

1.  連結的服務型別的 [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。 
4.   [管線](data-factory-create-pipelines.md) 具有複製活動使用 [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此範例會每個小時將資料從 Teradata 資料庫中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

第一個步驟中，設定資料管理閘道器中的指示 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文件。

**Teradata 連結服務：**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Azure Blob 儲存體連結服務：**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**Teradata 輸入資料集：**

此範例假設您已在 Teradata 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestamp")。

設定 “external”: true 和指定 externalData 原則即可通知資料處理站：這是資料處理站外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

    {
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **RelationalSource** 和 **接收器** 類型設為 **BlobSink**。 針對指定的 SQL 查詢 **查詢** 屬性選取過去一小時內要複製的資料。

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
                        }
                    ],                  
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## Teradata 連結服務屬性

下表提供 Teradata 連結服務專屬 JSON 元素的描述。 

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | Type 屬性必須設定為: **OnPremisesTeradata** | 是
伺服器 | Teradata 伺服器的名稱。 | 是
資料庫 | Teradata 資料庫的名稱。 | 是 
結構描述  | 在資料庫中的結構描述名稱。 | 否
authenticationType | 用來連接到 Teradata 資料庫的驗證類型。 可能的值為：匿名、基本和 Windows。 | 是
username | 如果您使用基本或 Windows 驗證，請指定使用者名稱。 | 否 
password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 | 否 
gatewayName | Data Factory 服務應該用來連接到內部部署 Teradata 資料庫的閘道器名稱。 | 是

請參閱 [設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) 如需詳細資訊設定內部部署 Teradata 資料來源的認證。

## Teradata 資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。  **TypeProperties** 類型資料集區段 **RelationalTable** (包含 Teradata 資料集) 具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
tableName | Teradata 資料庫執行個體中連結服務所參照的資料表名稱。 | 否 (如果 **查詢** 的 **RelationalSource** 指定) 

## Teradata 複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。 

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動類型的來源時 **RelationalSource** (包含 Teradata) 中的下列屬性有 **typeProperties** 區段:

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
query | 使用自訂查詢來讀取資料。 | SQL 查詢字串。 例如：select * from MyTable。 | 否 (如果 **tableName** 的 **資料集** 指定)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Teradata 的類型對應

中所述 [資料移動活動](data-factory-data-movement-activities.md) 文章中，複製活動會執行自動類型轉換成接收類型具有下列 2 個步驟的方法的來源類型的自動類型轉換:

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

將資料移到 Teradata 時，下列對應將用於從 Teradata 類型.NET 類型。

Teradata 資料庫類型 | .NET Framework 類型
----------------- | ---------------------------
Char | String
Clob | String
圖形 | String
VarChar | String
VarGraphic | String
Blob | Byte]
位元組 | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
十進位 | 十進位
兩倍 | 兩倍
Integer | Int32
Number | 兩倍
SmallInt | Int16
Date | DateTime
時間 | TimeSpan
時區的時間 | String
Timestamp | DateTime
時區的時間戳記 | DateTimeOffset
間隔日 | TimeSpan
間隔日至小時 | TimeSpan
間隔日至分鐘 | TimeSpan
間隔日至秒鐘 | TimeSpan
間隔小時 | TimeSpan
間隔小時至分鐘 | TimeSpan
間隔小時至秒鐘 | TimeSpan
間隔分鐘 | TimeSpan
間隔分鐘至秒鐘 | TimeSpan
間隔第二 | TimeSpan
間隔年 | String
間隔年至月 | String
間隔月 | String
Period(Date) | String
Period(Time) | String
Period(Time With Time Zone) | String
Period(Timestamp) | String
Period(Timestamp With Time Zone) | String
Xml | String

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]
