<properties 
    pageTitle="從檔案系統來回移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從內部部署檔案系統來回移動資料。" 
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
    ms.date="11/09/2015" 
    ms.author="spelluru"/>

# 使用 Azure Data Factory 從內部部署檔案系統來回移動資料

本文概述如何使用資料處理站複製活動和內部部署檔案系統往來移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

資料處理站支援透過資料管理閘道器連接至內部部署檔案系統。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文章來了解資料管理閘道器和設定閘道器上的逐步指示。 

**注意 ︰** 除了其他二進位檔必須安裝資料管理閘道器進行通訊的內部部署檔案系統。 

## Linux 檔案共用 

執行下列兩個步驟來搭配使用 Linux 檔案共用和檔案伺服器連結服務：

- 安裝 [Samba](https://www.samba.org/) Linux 伺服器上。
- 在 Windows 伺服器上安裝和設定資料管理閘道器。 不支援在 Linux 伺服器上安裝閘道器。 
 
## 範例：將資料從內部部署檔案系統複製到 Azure Blob

下列範例顯示：

1.  連結的服務型別的 [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.   [管線](data-factory-create-pipelines.md) 具有複製活動使用 [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。 

下列範例每小時都會將屬於時間序列的資料從內部部署檔案系統複製到 Azure blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

第一個步驟中，請勿安裝資料管理閘道器中的指示 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文件。 

**內部部署檔案伺服器連結服務：**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

您可以指定主機， **本機** 或 **localhost** 如果檔案共用位於閘道機器本身。 我們建議您使用 **encryptedCredential** 屬性，而不要使用 **userid** 和 **密碼** 屬性。  請參閱 [檔案系統連結服務](#onpremisesfileserver-linked-service-properties) 連結服務的詳細資訊。 

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

**內部部署檔案系統輸入資料集：**

資料是每小時利用反映特定日期時間及小時資料粒度的路徑和檔案名稱，從新的檔案中挑選出來。 

設定 “external”: ”true” 和指定 externalData 原則即可通知 Data Factory 服務：這是 Azure Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ]
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

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                "format": "%HH"
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

**複製活動：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **FileSystemSource** 和 **接收器** 類型設為 **BlobSink**。 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
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

##範例：將資料從 Azure SQL 複製到內部部署檔案系統 

下列範例顯示：

1.  AzureSqlDatabase 類型的連結服務。
2.  類型 OnPremisesFileServer 的連結服務。
3.  AzureSqlTable 類型的輸入資料集。 
3.  類型 FileShare 的輸出資料集。
4.  具有使用 SqlSource 和 FileSystemSink 之複製活動的管線。

此範例會每小時將屬於時間序列的資料從 Azure SQL Database 中的資料表複製到內部部署檔案系統。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

**Azure SQL 連結服務：**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**內部部署檔案伺服器連結服務：**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

您可以指定主機， **本機** 或 **localhost** 如果檔案共用位於閘道機器本身。 我們建議您使用 **encryptedCredential** 屬性，而不要使用 **userid** 和 **密碼** 屬性。  請參閱 [檔案系統連結服務](#onpremisesfileserver-linked-service-properties) 連結服務的詳細資訊。 

**Azure SQL 輸入資料集：**

此範例假設您已在 SQL Azure 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 

設定"external":"true"和指定 externalData 原則即可通知 Data Factory 服務是 data factory 外部的資料表，而且不由 data factory 中的活動所產生。

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

**內部部署檔案系統輸出資料集：**

資料是每小時利用反映特定日期時間及小時資料粒度的 Blob 路徑，從新的檔案所複製。

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                "format": "%HH"
              }
            }
          ]
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

**具有複製活動的管線：**
此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **SqlSource** 和 **接收器** 類型設為 **FileSystemSink**。 針對指定的 SQL 查詢 **SqlReaderQuery** 屬性選取過去一小時內要複製的資料。

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## OnPremisesFileServer 連結服務屬性

您可以利用內部部署檔案伺服器連結服務將內部部署檔案系統連結至 Azure Data Factory。 下表提供內部部署檔案伺服器連結服務專屬 JSON 元素的描述。 

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | Type 屬性應該設定為 **OnPremisesFileServer** | 是 
主機 | 伺服器的主機名稱。 使用 '\' 做為逸出字元，如下列範例所示 ︰ 如果您的共用是 ︰ \\servername，請指定 \\\servername。<p>如果閘道器電腦的本機檔案系統，請使用 Local 或 localhost。 如果檔案系統位於閘道器電腦不同的伺服器，請使用 \\\servername。</p> | 是
userid  | 指定具有伺服器存取權之使用者的識別碼 | 否 (如果您選擇 encryptedCredential)
password | 指定使用者的密碼 (userid) | 否 (如果您選擇 encryptedCredential) 
encryptedCredential | 指定，就可以執行新增 AzureRmDataFactoryEncryptValue 指令程式加密的認證<p>**注意 ︰** 您必須使用 Azure PowerShell 0.8.14 或更高版本，使用型別參數設為 OnPremisesFileSystemLinkedService 的 cmdlet，例如新增 AzureRmDataFactoryEncryptValue</p> | 否 (如果您選擇以純文字指定使用者識別碼和密碼)
gatewayName | 資料處理站服務應該用來連接到內部部署檔案伺服器的閘道器名稱 | 是

請參閱 [設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) 如需詳細資訊設定內部部署檔案系統資料來源的認證。

**範例：使用純文字的使用者名稱和密碼**
    
    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }
    
**範例：使用 encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "localhost",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## 內部部署檔案系統資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (Azure SQL、Azure Blob、Azure 資料表、內部部署檔案系統等)。 

每個資料集類型的 TypeProperties 區段都不同，可提供資料存放區中資料的位置、格式等相關資訊。 類型的資料集的 typeProperties 區段 **FileShare** 資料集具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
folderPath | 資料夾的路徑。 範例 ︰ myfolder<p>使用逸出字元 '\' 字串中的特殊字元。 例如 ︰ 若為 folder\subfolder，指定 folder\\\subfolder 並為 d:\samplefolder，指定 d:\\\samplefolder。</p><p>您可以將其與 **partitionBy** 將資料夾路徑以配量開始/結束日期時間。</p> | 是
fileName | 指定的檔案名稱 **folderPath** 如果您想要參考資料夾中的特定檔案的資料表。 如果未指定此屬性的任何值，資料表會指向資料夾中的所有檔案。<p>檔案名稱未指定輸出資料集，所產生檔案的名稱會在下列這種格式 ︰ </p><p>資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 否
partitionedBy | partitionedBy 可以用來指定時間序列資料的動態 folderPath 和 filename。 例如，folderPath 可針對每小時的資料進行參數化。 | 否
格式 | 支援兩種格式類型 ︰ **TextFormat**, ，**AvroFormat**。 若為此值，您需要將格式底下的 type 屬性設定為其中之一。 如果 forAvroFormatmat 為 TextFormat，您可以指定格式的其他選擇性屬性。 如需詳細資料，請參閱下面的格式一節。 | 否
fileFilter | 指定要用來在 folderPath (而不是所有檔案) 中選取檔案子集的篩選器。 <p>允許的值為: * （多個字元） 和嗎？ （單一字元）。</p><p>範例 1:"fileFilter":"*.log"</p>範例 2:"fileFilter": 2014-1-？。txt"</p><p>**注意**: fileFilter 是適用於輸入 FileShare 資料集</p> | 否
| compression | 指定此資料的壓縮類型和層級。 支援的類型為：GZip、Deflate 和 BZip2，而支援的層級為：最佳和最快。 請參閱 [壓縮支援](#compression-support) 一節以取得詳細資料。  | 否 |

> [AZURE.NOTE] 無法同時使用檔名和 fileFilter。

### 運用 partionedBy 屬性

如上所述，您可以利用 partitionedBy 指定時間序列資料的動態 folderPath 和 filename。 您可以使用 Data Factory 巨集和系統變數以及可指出給定資料配量的邏輯時間週期的 SliceStart、SliceEnd 來這麼做。 

請參閱 [建立資料集](data-factory-create-datasets.md), ，[排程和執行](data-factory-scheduling-and-execution.md), ，和 [建立管線](data-factory-create-pipelines.md) 文章，以了解更多詳細資料的時間序列資料集、 排程和配量。

#### 範例 1：

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在上述範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。 SliceStart 是指配量的開始時間。 每個配量的 folderPath 都不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### 範例 2：

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在上述範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

### 指定 TextFormat

如果格式設為 **TextFormat**, ，您可以指定下列 **選擇性** 屬性在 **格式** 區段內 **typeProperties** 一節。 

屬性 | 說明 | 必要
-------- | ----------- | --------
columnDelimiter | 在檔案中做為資料行分隔符號的字元。 預設值是逗號 (,)。 | 否
rowDelimiter | 在檔案中做為資料列分隔符號的字元。 預設值是下列任一項：[“\r\n”, “\r”,” \n”]。 | 否
escapeChar | 用來逸出內容中顯示之資料行分隔符號的特殊字元。 沒有預設值。 您必須指定這個屬性不能超過一個字元。<p>比方說，如果您以逗號 （，） 做為資料行分隔符號，但您想要有逗號字元的文字 (範例:"Hello，world")，您可以定義 '$' 做為逸出字元，並使用字串"Hello$，world"中的來源。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否
quoteChar | 用來引用字串值的特殊字元。 引號字元內的資料行和資料列分隔符號會被視為字串值的一部分。 沒有預設值。 您必須指定這個屬性不能超過一個字元。<p>比方說，如果您以逗號 （，） 做為資料行分隔符號，但您想要有逗號字元的文字 (範例 ︰ < Hello，world >)，您可以定義 '"' 做為引用字元並在來源中使用字串 <"Hello，world">。 這個屬性不適用於輸入和輸出資料表。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否
nullValue | 用來代表 Blob 檔案內容中 null 值的字元。 預設值為 “\N”.> | 否
encodingName | 指定編碼名稱。 如需有效編碼名稱的清單，請參閱：Encoding.EncodingName 屬性。 <p>例如：windows-1250 或 shift_jis。 預設值為 UTF-8。</p> | 否

#### 範例：

下列範例會示範一些格式屬性 **TextFormat**。

    "typeProperties":
    {
        "folderPath": "MyFolder",
        "fileName": "MyFileName"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
        }
    },

若要使用 escapeChar 而不是 quoteChar，請以下列內容取代具有 quoteChar 的行：

    "escapeChar": "$",

### 指定 AvroFormat

如果格式設為 **AvroFormat**, ，您不需要在 typeProperties 區段內的 Format 區段中指定任何屬性。 範例：

    "format":
    {
        "type": "AvroFormat",
    }
    
若要 Hive 資料表中使用 Avro 格式，請參閱 [Apache Hive 的教學課程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。       

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## 檔案共用複製活動類型屬性

**了解 FileSystemSource** 支援下列屬性 ︰

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 | True/False (預設值為 False)| 否 | 

 **FileSystemSink** 支援下列屬性 ︰  

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 當來源為 BlobSource 或 FileSystem 時，定義複製行為。 | <p>有三種可能的值為 copyBehavior 屬性。 </p><ul><li>**PreserveHierarchy:** ︰ 保留目標資料夾，亦即，來源資料夾的原始程式檔的相對路徑中的檔案階層完全相同的目標資料夾的目標檔案相對路徑。</li><li>**FlattenHierarchy:** 從來源資料夾的所有檔案會都出現在第一層的目標資料夾。 目標檔案會有自動產生的名稱。 </li><li>**MergeFiles:** 合併到一個檔案的來源資料夾的所有檔案。 如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。</li></ul> | 否 |

### 遞迴和 copyBehavior 範例
本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。 

遞迴 | copyBehavior | 產生的行為
--------- | ------------ | --------
true | preserveHierarchy | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 Folder1 會有相同的結構做為來源<p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>.  
true | flattenHierarchy | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 1 將會有下列結構 ︰ <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; 自動產生名稱 File1<br/>（& s) nbsp; （& s) nbsp; （& s) nbsp; & nbsp; File2 如自動產生的名稱<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; File3 自動產生名稱<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; File4 自動產生名稱<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; File5 自動產生名稱</p>
true | mergeFiles | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 1 將會有下列結構 ︰ <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;1 + 2 + File3 + File4 + 5 檔案內容會合併成一個檔案，以自動產生的檔案名稱</p>
false | preserveHierarchy | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/></p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.
false | flattenHierarchy | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; 自動產生名稱 File1<br/>（& s) nbsp; （& s) nbsp; （& s) nbsp; & nbsp; File2 如自動產生的名稱<br/></p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.
false | mergeFiles | <p>來源資料夾 Folder1 具有下列結構 ︰</p> <p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;File1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;2<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;Subfolder1<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File3<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File4<br/>（& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>（& s) nbsp; & nbsp; & nbsp; （& s) nbsp;1 + 2 的內容會合併成一個檔案，以自動產生的檔案名稱。 自動產生名稱 File1</p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 


