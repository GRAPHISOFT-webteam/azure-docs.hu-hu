<properties
    pageTitle="使用 Azure PowerShell 建置您的第一個 Azure Data Factory 管線"
    description="在本教學課程中，您將使用 Azure PowerShell，建立範例 Azure Data Factory 管線。"
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
    ms.topic="hero-article"
    ms.date="12/08/2015"
    ms.author="spelluru"/>


# 使用 Azure PowerShell 建置您的第一個 Azure Data Factory 管線

> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)



在本文中，您將了解如何使用 Azure PowerShell 來建立您的第一個管線。 本教學課程包含下列步驟：

1.  建立 Data Factory。
2.  建立連結服務 (資料存放區、運算) 和資料集。
3.  建立管線。

> [AZURE.IMPORTANT] 
> 本文不提供 Azure Data Factory 服務的概念性概觀。 如需服務的詳細概觀，請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)。 
> 
> 這篇文章並未涵蓋所有的 Data Factory Cmdlet。 請參閱 [Data Factory Cmdlet 參考](https://msdn.microsoft.com/library/dn820234.aspx) 如需 Data Factory cmdlet 的完整文件。

## 必要條件

除了「教學課程概觀」主題中所列的必要條件，您還需要安裝下列項目：

- **Azure PowerShell** (英文)。 遵循指示 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 文件，以在電腦上安裝最新版的 Azure PowerShell。

如果您使用 Azure PowerShell 的 **1.0 版 <**, ，您必須使用指令程式所記載 [這裡 ][cmdlet-reference]。您也必須在使用 Data Factory Cmdlet 之前，先執行下列命令：

1. 開啟 Azure PowerShell 並執行下列命令。 將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。
    1. 執行 **Add-azureaccount** ，然後輸入使用者名稱和密碼用於登入 Azure 入口網站。
    2. 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
    3. 執行 **Select-AzureSubscription** 以選取您想要使用的訂用帳戶。 此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
4. 切換至 AzureResourceManager 模式，因為 Azure Data Factory Cmdlet 可在此模式中使用：**Switch-AzureMode AzureResourceManager**。


## 步驟 1：建立 Data Factory

在此步驟中，您會使用 Azure PowerShell 建立名為 ADFTutorialDataFactoryPSH 的 Azure Data Factory。

1. 開啟 Azure PowerShell 並執行下列命令。 將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。
    - 執行 **登入 AzureRmAccount** ，然後輸入使用者名稱和密碼用於登入 Azure 入口網站。
    - 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
    - 執行 * * Select-azuresubscription <Name of the subscription>* * 若要選取您想要使用的訂閱。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
3. 執行以下命令，建立名為 **ADFTutorialResourceGroup** 的 Azure 資源群組。

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    本教學課程的某些步驟假設您使用名為 ADFTutorialResourceGroup 的資源群組。 如果使用不同的資源群組，您必須以該群組取代本教學課程中的 ADFTutorialResourceGroup。
4. 執行 **New-AzureRmDataFactory** Cmdlet，建立名為 DataFactoryMyFirstPipelinePSH 的 Data Factory。

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

    > [AZURE.IMPORTANT] Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤：「Data Factory 名稱 “DataFactoryMyFirstPipelinePSH” 無法使用」****，請變更名稱 (例如 yournameADFTutorialDataFactoryPSH)。 執行本教學課程中的步驟時，請使用此名稱來取代 ADFTutorialFactoryPSH。 請參閱 [Data Factory-命名規則](data-factory-naming-rules.md) Data Factory 成品的命名規則的主題。
    > 
    > Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

在後續步驟中，您將了解如何建立將在本教學課程使用的連結服務、資料集及管線。

## 步驟 2：建立連結服務和資料集

在此步驟中，您會將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結至 Data Factory，然後建立資料集以表示由 Hive 處理的輸出資料。

### 建立 Azure 儲存體連結服務

1.  在 C:\ADFGetStartedPSH 資料夾中，使用以下內容建立名為 StorageLinkedService.json 的 JSON 檔案。 建立 ADFGetStartedPSH 資料夾 (如果不存在)。

        {
            "name": "StorageLinkedService",
            "properties": {
                "type": "AzureStorage",
                "description": "",
                "typeProperties": {
                    "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        }

    使用您的 Azure 儲存體帳戶名稱取代帳戶名稱****，並使用 Azure 儲存體帳戶的存取金鑰取代帳戶金鑰****。 若要了解如何取得儲存體存取金鑰，請參閱 [檢視、 複製和重新產生儲存體存取金鑰](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)。

2.  在 Azure PowerShell 中，切換到 ADFGetStartedPSH 資料夾。
3.  您可以使用 **New-AzureRmDataFactoryLinkedService** Cmdlet 建立連結服務。 此 Cmdlet 和您在本教學課程中使用的其他 Data Factory Cmdlet，皆需要您將值傳給 *ResourceGroupName* 和 *DataFactoryName* 參數。 或者，您可以使用 Get-AzureRmDataFactory**** 取得 DataFactory**** 物件，並傳遞此物件，就不需要在每次執行 Cmdlet 時輸入 ResourceGroupName** 和 DataFactoryName**。 執行以下命令，將 **Get-AzureRmDataFactory** Cmdlet 的輸出指派給 **$df** 變數。

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.  現在，執行 **New-AzureRmDataFactoryLinkedService** Cmdlet 以建立連結的 **StorageLinkedService** 服務。

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    如果您還沒執行 **Get-AzureRmDataFactory** Cmdlet 和指派輸出給 **$df** 變數，您就必須指定 ResourceGroupName** 和 DataFactoryName** 參數的值，如下所示。

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

    如果您在教學課程途中關閉 Azure PowerShell，則下次啟動 Azure PowerShell 時必須執行 **Get-AzureRmDataFactory** Cmdlet，才能完成教學課程。

### 建立 Azure HDInsight 連結服務

現在，您將為用來執行 Hive 指令碼的隨選 HDInsight 叢集建立連結服務。

1.  在 C:\ADFGetStartedPSH 資料夾中，使用以下內容建立名為 HDInsightOnDemandLinkedService.json 的 JSON 檔案。

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
            }
          }
        }

    下表提供程式碼片段中所使用之 JSON 屬性的描述：

     屬性| 說明
    -------- | -----------
     版本| 指定所建立的 HDInsight 版本為 3.2。
     ClusterSize| 建立一個單一節點的 HDInsight 叢集。
     TimeToLive| 指定 HDInsight 叢集在被刪除之前的閒置時間。
     linkedServiceName| 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶

2. 執行 **New-AzureRmDataFactoryLinkedService** Cmdlet 建立名為 HDInsightOnDemandLinkedService 的連結服務。

        New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json



### 建立輸出資料集

現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的資料。

1.  在 C:\ADFGetStartedPSH 資料夾中，使用下列內容建立名為 OutputTable.json 的 JSON 檔案：

        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "data/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    在上述範例中，您要建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。 此外，指定將結果儲存在名為 **data** 的 Blob 容器及名為 **partitioneddata** 的資料夾中。 **availability** 區段指定每個月產生一次輸出資料集。

2. 在 Azure PowerShell 中執行以下命令來建立 Data Factory 資料集。

        New-AzureRmDataFactoryDataset $df -File .\OutputTable.json


## 步驟 3：建立您的第一個管線

在此步驟中，您將建立您的第一個管線。

1.  在 C:\ADFGetStartedPSH 資料夾中，使用下列內容建立名為 MyFirstPipelinePSH.json 的 JSON 檔案：
    > [AZURE.IMPORTANT] 取代 **storageaccountname** 在 JSON 中的儲存體帳戶的名稱。

        {
          "name": "MyFirstPipeline",
          "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
              {
                "type": "HDInsightHive",
                "typeProperties": {
                  "scriptPath": "script/partitionweblogs.hql",
                  "scriptLinkedService": "StorageLinkedService",
                  "defines": {
                    "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
                  }
                },
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "policy": {
                  "concurrency": 1,
                  "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
              }
            ],
            "start": "2014-01-01",
            "end": "2014-01-02"
          }
        }

    在上述範例中，您會建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理資料的單一活動。

    Hive 指令碼檔案 (partitionweblogs.hql) 儲存於 Azure 儲存體帳戶 (透過 scriptLinkedService 指定，名為 StorageLinkedService) 且在容器中名為 **script**。

    **defines** 區段用於指定執行階段設定，該設定將會傳遞到 hive 指令碼作為 Hive 設定值 (例如 ${hiveconf:PartitionedData})。

    管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

    在活動 JSON 中，您指定 Hive 指令碼在透過連結的服務 – **HDInsightOnDemandLinkedService** 所指定的電腦上執行。
2. 執行以下命令建立 Data Factory 資料表。

        New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json

5. 恭喜，您已經成功使用 Azure PowerShell 建立您的第一個管線！

### <a name="MonitorDataSetsAndPipeline"></a> 監視資料集和管線

在此步驟中，您將使用 Azure PowerShell 來監視 Azure Data Factory 的運作情形。

1.  執行 **Get-AzureRmDataFactory** 並指派輸出給 **$df** 變數。

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.  執行 **Get-AzureRmDataFactorySlice**，以取得 **EmpSQLTable** (管線的輸出資料表) 所有配量的詳細資料。

        Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

    請注意，您在此處指定的 StartDateTime 與在管線 JSON 中指定的開始時間是相同的。 您應該會看到如下所示的輸出。

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : DataFactoryMyFirstPipelinePSH
        TableName         : AzureBlobOutput
        Start             : 1/1/2014 12:00:00 AM
        End               : 2/1/2014 12:00:00 AM
        RetryCount        : 0
        Status            : InProgress
        LatencyStatus     :
        LongRetryCount    : 0

3.  執行 **Get-AzureRmDataFactoryRun**，以取得特定配量的活動執行詳細資料。

        Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

    您應該會看到如下所示的輸出。

        Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : DataFactoryMyFirstPipelinePSH
        TableName           : AzureBlobOutput
        ProcessingStartTime : 7/7/2015 1:14:18 AM
        ProcessingEndTime   : 12/31/9999 11:59:59 PM
        PercentComplete     : 0
        DataSliceStart      : 1/1/2014 12:00:00 AM
        DataSliceEnd        : 2/1/2014 12:00:00 AM
        Status              : AllocatingResources
        Timestamp           : 7/7/2015 1:14:18 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : RunSampleHiveActivity
        PipelineName        : MyFirstPipeline
        Type                : Script

    您可以繼續執行此 Cmdlet 直到您看到配量處於就緒狀態或失敗狀態。 當配量處於就緒狀態時，檢查您 blob 儲存體中 data 容器內 partitioneddata 資料夾的輸出資料。 請注意，建立隨選 HDInsight 叢集通常需要一些時間。

請參閱 [Data Factory Cmdlet 參考](https://msdn.microsoft.com/library/azure/dn820234.aspx) 如需 Data Factory cmdlet 的完整文件。



## 後續步驟

在本文中，您已經建立可在隨選 Azure HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。 若要了解如何使用 「 複製活動將資料從 Azure Blob 複製到 Azure SQL，請參閱 [教學課程: 將資料從 Azure Blob 複製到 Azure SQL](./data-factory-get-started.md)。



[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx 

