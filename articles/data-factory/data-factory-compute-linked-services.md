<properties
    pageTitle="運算連結服務 | Microsoft Azure"
    description="了解您可以在 Azure Data Factory 管線中用來轉換/處理資料的運算環境。"
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
    ms.date="12/10/2015"
    ms.author="spelluru"/>


# 運算連結服務

本文說明您可用來處理或轉換資料的各種運算環境。 其中還提供在設定將這些運算環境連結至 Azure Data Factory 的連結服務時，Data Factory 所支援的不同組態 (隨選與自備) 的詳細資料。

## 隨選運算環境

在這種組態中，運算環境完全是由 Azure Data Factory 服務管理。 Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。 您可以建立隨選運算環境的連結服務、加以設定，以及控制工作執行、叢集管理和啟動動作的細微設定。
> [AZURE.NOTE] 目前僅支援 Azure HDInsight 叢集的隨選組態。

## Azure HDInsight 隨選連結服務

Azure Data Factory 服務會自動建立隨選 HDInsight 叢集，以便處理資料。 此叢集會建立在與叢集相關聯的儲存體帳戶 (JSON 中的 linkedServiceName 屬性) 相同的區域中。

請注意下列有關隨選 HDInsight 連結服務的**重點**：

- 您不會看到隨選 HDInsight 叢集建立在您的 Azure 訂用帳戶中；Azure Data Factory 服務會代表您管理隨選 HDInsight 叢集。
- 在隨選 HDInsight 叢集上執行之工作的記錄檔會被複製到與 HDInsight 叢集相關聯的儲存體帳戶。 您可從 Azure 傳統入口網站的 [**活動執行詳細資料**] 刀鋒視窗存取這些記錄檔。 請參閱 [監視和管理管線](data-factory-monitor-manage-pipelines.md) 文章以取得詳細資料。
- 只會針對 HDInsight 叢集啟動並執行工作的時間來向您收取費用。

> [AZURE.IMPORTANT] 通常會花費 **15 分鐘**以上的時間來佈建隨選 Azure HDInsight 叢集。

### 範例

    {
      "name": "HDInsightOnDemandLinkedService",
      "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
          "clusterSize": 4,
          "timeToLive": "00:05:00",
          "version": "3.2",
          "osType": "linux",
          "linkedServiceName": "MyBlobStore",
          "hcatalogLinkedServiceName": "AzureSqlLinkedService",
          "additionalLinkedServiceNames": [
            "otherLinkedServiceName1",
            "otherLinkedServiceName2"
          ]
        }
      }
    }

### 屬性

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| type 屬性應設為 **HDInsightOnDemand**。| 是
 clusterSize| 隨選叢集的大小。指定您希望此隨選叢集中有多少個節點。| 是
 timetolive| <p>隨選 HDInsight 叢集允許的閒置時間。指定多久隨 HDInsight 叢集後會保持運作的活動執行，如果叢集中沒有任何其他使用中的工作完成。</p><p>比方說，如果活動執行花費 6 分鐘，而 timetolive 設為 5 分鐘，叢集會保持運作 5 分鐘之後 6 分鐘處理的活動執行。如果 6 分鐘視窗執行另一個活動執行時，它會處理由相同的叢集。</p><p>建立隨選 HDInsight 叢集是昂貴的作業 (可能需要一段時間)，因此使用這項設定為需要以改善效能的 data factory，重複使用隨 HDInsight 叢集。</p><p>活動執行處理時，您將 timetolive 值設為 0，如果刪除叢集。另一方面，如果您設定較高的值，叢集可能會有不必要的閒置而導致高成本。因此，很重要，您設定適當的值，根據您的需求。</p><p>有多個管線可以共用相同的隨選 HDInsight 叢集的執行個體，如果適當地設定 timetolive 屬性值</p>| 是
 版本| HDInsight 叢集的版本。針對 Windows 叢集的預設值為 3.1，針對 Linux 叢集的預設值為 3.2。| 否
 linkedServiceName| 隨選叢集用於儲存及處理資料的 Blob 存放區。| 是
 additionalLinkedServiceNames| 指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。| 否
 osType| 作業系統的類型。允許的值為：Windows (預設值) 和 linux| 否
 hcatalogLinkedServiceName| 指向 HCatalog 資料庫的 Azure SQL 連結服務名稱。將會使用 Azure SQL 資料庫作為中繼存放區，建立隨選 HDInsight 叢集。| 否

### 進階屬性

您也可以針對隨選 HDInsight 叢集的細微組態指定下列屬性。

 屬性| 說明| 必要
:-------- | :----------- | :--------
 coreConfiguration| 指定要建立之 HDInsight 叢集的核心組態參數 (如 core-site.xml 所示)。| 否
 hBaseConfiguration| 指定 HDInsight 叢集的 HBase 組態參數 (hbase-site.xml)。| 否
 hdfsConfiguration| 指定 HDInsight 叢集的 HDFS 組態參數 (hdfs-site.xml)。| 否
 hiveConfiguration| 指定 HDInsight 叢集的 hive 組態參數 (hive-site.xml)。| 否
 mapReduceConfiguration| 指定 HDInsight 叢集的 MapReduce 組態參數 (mapred-site.xml)。| 否
 oozieConfiguration| 指定 HDInsight 叢集的 Oozie 組態參數 (oozie-site.xml)。| 否
 stormConfiguration| 指定 HDInsight 叢集的 Storm 組態參數 (storm-site.xml)。| 否
 yarnConfiguration| 指定 HDInsight 叢集的 Yarn 組態參數 (yarn-site.xml)。| 否

#### 範例 – 包含進階屬性的隨選 HDInsight 叢集組態

    {
      "name": " HDInsightOnDemandLinkedService",
      "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "version": "3.2",
          "linkedServiceName": "adfods1",
          "coreConfiguration": {
            "templeton.mapper.memory.mb": "5000"
          },
          "hiveConfiguration": {
            "templeton.mapper.memory.mb": "5000"
          },
          "mapReduceConfiguration": {
            "mapreduce.reduce.java.opts": "-Xmx4000m",
            "mapreduce.map.java.opts": "-Xmx4000m",
            "mapreduce.map.memory.mb": "5000",
            "mapreduce.reduce.memory.mb": "5000",
            "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
          },
          "yarnConfiguration": {
            "yarn.app.mapreduce.am.resource.mb": "5000",
            "mapreduce.map.memory.mb": "5000"
          },
          "additionalLinkedServiceNames": [
            "datafeeds",
            "adobedatafeed"
          ]
        }
      }
    }

### 節點大小

您可使用下列屬性指定前端、資料和的 zookeeper 節點的大小。

 屬性| 說明| 必要
:-------- | :----------- | :--------
 headNodeSize| 指定前端節點的大小。預設值為：大。如需詳細資料，請參閱以下**指定節點大小**一節。| 否
 dataNodeSize| 指定資料節點的大小。預設值為：大| 否
 zookeeperNodeSize| 指定 Zoo Keeper 節點的大小。預設值為：小| 否

#### 指定節點大小

請參閱 [虛擬機器的大小](../virtual-machines/virtual-machines-size-specs.md#size-tables) 發行項的字串值，您必須指定上述屬性。值必須符合本文件中所參考的 **CMDLET 與 APIS**。如您在文中所見，若資料節點的大小設定為大 (預設值)，則記憶體大小為 7 GB，其可能不適用於您的案例。

若想要建立 D4 大小的前端節點與背景工作節點，必須指定 **Standard_D4** 作為 headNodeSize 與 dataNodeSize 屬性的值。

    "headNodeSize": "Standard_D4",  
    "dataNodeSize": "Standard_D4",

若您為這些屬性指定錯誤的值，可能會顯示下列**錯誤：**無法建立叢集。例外狀況：無法完成叢集建立作業。作業失敗 (錯誤代碼「400」)。叢集剩餘狀態：「錯誤」。訊息：「PreClusterCreationValidationFailure」。若顯示此錯誤，請確認您使用來自前述文章中資料表的 **CMDLET 與 APIS**名稱。



## 自備運算環境

在這種組態中，使用者可以將現有的運算環境註冊為 Data Factory 中的連結服務。 此運算環境是由使用者管理並由 Data Factory 服務用來執行活動。

下列運算環境可支援這類型的組態：

- Azure HDInsight
- Azure Batch
- Azure Machine Learning

## Azure HDInsight 連結服務

您可以建立 Azure HDInsight 連結服務，以向 Data Factory 註冊自己的 HDInsight 叢集。

### 範例

    {
      "name": "HDInsightLinkedService",
      "properties": {
        "type": "HDInsight",
        "typeProperties": {
          "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
          "userName": "admin",
          "password": "<password>",
          "location": "WestUS",
          "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
      }
    }

### 屬性

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| type 屬性應設為 **HDInsight**。| 是
 clusterUri| HDInsight 叢集的 URI。| 是
 username| 指定要用來連接到現有 HDInsight 叢集的使用者名稱。| 是
 password| 指定使用者帳戶的密碼。| 是
 location| 指定 HDInsight 叢集的位置 (例如：WestUS)。| 是
 linkedServiceName| 此 HDInsight 叢集所使用之 Blob 儲存體的連結服務名稱。| 是

## Azure Batch 連結服務

您可以建立 Azure Batch 連結服務，以向 Data Factory 註冊虛擬機器 (VM) 的 Batch 集區。 您可以使用 Azure Batch 或 Azure HDInsight 執行 .NET 自訂活動。

如果您不熟悉 Azure Batch 服務，請參閱下列主題：


- [Azure 批次的基本概念](../batch/batch-technical-overview.md) Azure 批次服務的概觀。
- [New-azurebatchaccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet 來建立 Azure Batch 帳戶 (或) [Azure 傳統入口網站](../batch/batch-account-create-portal.md) 來建立使用 Azure 傳統入口網站的 Azure 批次帳戶。 請參閱 [使用 PowerShell 管理 Azure 批次帳戶](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 主題詳細說明如何使用此指令程式。
- [New-azurebatchpool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet 來建立 Azure Batch 集區。

### 範例

    {
      "name": "AzureBatchLinkedService",
      "properties": {
        "type": "AzureBatch",
        "typeProperties": {
          "accountName": "<Azure Batch account name>",
          "accessKey": "<Azure Batch account key>",
          "poolName": "<Azure Batch pool name>",
          "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
      }
    }

將 "**.<地區名稱**" 附加至為 **accountName** 屬性所用的 Batch 帳戶名稱。範例：

            "accountName": "mybatchaccount.eastus"

另一個選項是提供 batchUri 端點，如下所示。

            "accountName": "adfteam",
            "batchUri": "https://eastus.batch.azure.com",

### 屬性

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| type 屬性應設為 **AzureBatch**。| 是
 accountName| 建立 Azure Batch 帳戶。| 是
 accessKey| Azure Batch 帳戶的存取金鑰。| 是
 poolName| 虛擬機器的集區名稱。| 是
 linkedServiceName| 與此 Azure Batch 連結服務相關聯的 Azure 儲存體服務連結名稱。此連結服務用於執行活動及儲存活動執行記錄檔所需的暫存檔案。| 是


## Azure Machine Learning 連結服務

您可建立 Azure Machine Learning 連結服務，以向 Data Factory 註冊 Machine Learning 批次評分端點。

### 範例

    {
      "name": "AzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch scoring endpoint]/jobs",
          "apiKey": "<apikey>"
        }
      }
    }

### 屬性

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| type 屬性應設為：**AzureML**。| 是
 mlEndpoint| 批次評分 URL。| 是
 apiKey| 已發佈的工作區模型的 API。| 是


## Azure 資料湖分析連結服務

您建立 **Azure 資料湖分析** 連結的服務，將連結的 Azure 資料湖分析計算之前使用 Azure data factory 服務 [資料湖分析 U SQL 活動](data-factory-usql-activity.md) 管線中。

下列範例提供 Azure 資料湖分析連結服務的 JSON 定義。

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>",
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }

下表提供 JSON 定義中所使用之屬性的描述：

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| type 屬性應設為：**AzureDataLakeAnalytics**。| 是
 accountName| Azure 資料湖分析帳戶名稱。| 是
 dataLakeAnalyticsUri| Azure 資料湖分析 URI。| 否
 authorization| 按一下 Data Factory 編輯器中的 [授權]**** 按鈕並完成 OAuth 登入後，即會自動擷取授權碼。| 是
 subscriptionId| Azure 訂用帳戶識別碼| 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。
 resourceGroupName| Azure 資源群組名稱| 否 (若未指定，便會使用 Data Factory 的資源群組)。
 sessionId| OAuth 授權工作階段的工作階段識別碼。每個工作階段識別碼都是唯一的，只能使用一次。這是在 Data Factory 編輯器中自動產生。| 是


## Azure SQL 連結服務

建立 Azure SQL 連結服務，並將它與 [預存程序活動](data-factory-stored-proc-activity.md) 叫用從 Data Factory 管線的預存程序。 請參閱 [Azure SQL 連接器](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 如需詳細資訊，此連結服務的相關文章。





