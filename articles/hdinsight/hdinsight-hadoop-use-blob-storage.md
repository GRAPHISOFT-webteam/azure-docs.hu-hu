<properties
    pageTitle="從 HDFS 相容的 Blob 儲存體查詢資料 | Microsoft Azure"
    description="HDInsight 使用 Azure Blob 儲存體作為 HDFS 的巨量資料存放區。 了解如何從 Blob 儲存體查詢資料並儲存分析的結果。"
    keywords="blob storage,hdfs,structured data,unstructured data"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/23/2015"
    ms.author="jgao"/>


# 在 HDInsight 上搭配 Hadoop 使用 HDFS 相容的 Azure Blob 儲存體

了解如何搭配 HDInsight 使用低成本的 Azure Blob 儲存體，建立 Azure 儲存體帳戶和 Blob 儲存體容器，然後處理其中的資料。

Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。

將資料儲存在 Blob 儲存體中，您便可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

> [AZURE.NOTE]   *Asv: / /* 語法不支援在 HDInsight 3.0 版叢集中。 這表示，任何工作提交至 HDInsight 3.0 版叢集且明確使用 *asv: / /* 語法將會失敗。  *Wasb: / /* 應該改為使用語法。 另外，如果工作提交至任何以現有中繼存放區建立的 HDInsight 3.0 版叢集，且中繼存放區中使用 asv:// 語法來明確參考資源，也會失敗。 必須使用 wasb:// 來定址資源以重新建立這些中繼存放區。

> HDInsight 目前僅支援區塊 Blob。

> 大部分 HDFS 命令 (例如， <b>ls</b>， <b>copyFromLocal</b> 和 <b>mkdir</b>) 仍可正常運作。 僅供原生 HDFS 實作 (稱為 DFS)，例如使用的命令 <b>fschk</b> 和 <b>dfsadmin</b>會在 Azure Blob 儲存體中顯示不同的行為。

如需建立 HDInsight 叢集的資訊，請參閱 [開始使用 HDInsight][hdinsight-get-started] 或 [建立 HDInsight 叢集][hdinsight-creation]。


## HDInsight 儲存架構
下圖提供 HDInsight 儲存架構的摘要檢視：

![Hadoop 叢集會使用 HDFS API 來存取和儲存 Blob 儲存體中的結構化和非結構化資料。](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 可使用完整 URI 來存取此檔案系統，例如：

    hdfs://<namenodehost>/<path>

此外，HDInsight 也能夠存取儲存在 Azure Blob 儲存體中的資料。 語法為：

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop 支援預設檔案系統的概念。 預設檔案系統意指預設配置和授權。 也可用來解析相對路徑。 HDInsight 建立過程中會指定 Azure 儲存體帳戶和該帳戶中的特定 Azure Blob 儲存容器，做為預設檔案系統。

除了此儲存體帳戶，您也可以在建立過程中，從相同 Azure 訂用帳戶或不同 Azure 訂用帳戶中新增其他儲存體帳戶。 如需關於新增其他儲存體帳戶的指示，請參閱 [建立 HDInsight 叢集][hdinsight-creation]。

- **連線至叢集的儲存體帳戶中的容器:** 因為帳戶名稱和金鑰與相關聯的叢集建立期間，您可以完整存取這些容器中 blob。

- **公用容器或未連線至叢集的儲存體帳戶中的公用 blob:** 您有容器中 blob 的唯讀權限。

    > [AZURE.NOTE]
        > 公用容器可讓您取得該容器中，並取得容器中繼資料的所有 blob 的清單。 公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">限制對容器和 Blob 的存取</a>。

- **無法連線至叢集的儲存體帳戶中的私用容器:** 您無法存取容器中的 blob，除非在提交 WebHCat 工作時，會定義儲存體帳戶。 稍後在本文中會加以說明。


建立程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。 HDInsight 的預設行為是使用 core-site.xml 檔案中定義的儲存體帳戶。 因為叢集前端節點 (主要) 有可能會隨時重新安裝映像或進行移轉，屆時將會遺失對這些檔案所做的任何變更，所以我們不建議您編輯 core-site.xml 檔案。

多個 WebHCat 工作 (包括 Hive、MapReduce、Hadoop 資料流和 Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料。 (目前適用於含儲存體帳戶的 Pig，但不適用於中繼資料)。在 [使用 Azure PowerShell 存取 blob](#powershell) 區段的本文中，沒有此功能的範例。 如需詳細資訊，請參閱 [其他儲存體帳戶和 metastores 上使用 HDInsight 叢集](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 儲存體可使用於結構化和非結構化資料。 Blob 儲存容器以機碼/值組來儲存資料，沒有目錄階層。 但是，機碼名稱中可使用 ( / ) 斜線字元，使檔案變成好像儲存在目錄結構中一樣。 例如，blob 的機碼可能 *input/log1.txt*。 實際上不 *輸入* 目錄存在，但索引鍵名稱的斜線字元的緣故，其檔案路徑的外觀。

###<a id="benefits"></a>Blob 儲存體的優點
計算叢集和儲存體叢集未並存於同處所隱含的效能損失，可經由將計算叢集建立到靠近 Azure 資料中心內的儲存體帳戶資源來彌補，其中的高速網路可讓計算節點非常有效率地存取 Azure Blob 儲存體內的資料。

將資料儲存在 Azure Blob 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用:** HDFS 中的資料位於運算叢集內。 只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。 可以存取 Azure Blob 儲存體中的資料，透過 HDFS Api 或透過 [Blob 儲存體 REST Api][blob-storage-restAPI]。 因此，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。
* **資料封存:** 將資料儲存在 Azure Blob 儲存體可不會遺失使用者資料安全地刪除用於計算的 HDInsight 叢集。
* **資料儲存成本:** 將資料儲存在 DFS 中的長期成本高於 Azure Blob 儲存體中儲存資料，因為運算叢集的成本高於 Azure Blob 儲存體容器的成本。 此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。
* **彈性向外延展:** 雖然 HDFS 提供向外延展的檔案系統、 小數位數取決於您建立叢集的節點數目。 變更縮放比例會變得更複雜的程序比彈性延展功能，就會自動在 Azure Blob 儲存體。
* **地理區域複寫:** Azure Blob 儲存容器可進行地理區域複寫。 雖然這樣可支援地理位置復原和資料備援，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。 因此，只有在資料的價值大於額外成本時，才建議您明智地選擇地理區域複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Azure Blob 儲存體中。 在此情況下，您仍可選擇將資料儲存在本機 HDFS。 事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。



## 建立 Blob 容器

若要使用 blob，您先建立 [Azure 儲存體帳戶][azure-storage-create]。 在這過程中，需要指定 Azure 資料中心來儲存您以此帳戶所建立的物件。 叢集與儲存體帳戶必須在相同資料中心內託管。 Hive 中繼存放區 SQL Server 資料庫和 Oozie 中繼存放區 SQL Server 資料庫也必須位在相同的資料中心內。

您所建立的每個 Blob 不論位於何處，都屬於 Azure 儲存體帳戶中的某個容器。 此容器可能是在 HDInsight 外建立的現有 Blob，也可能是為 HDInsight 叢集建立的容器。


預設 Blob 容器會儲存叢集特定資訊，例如工作歷程記錄和記錄檔。 不要與多個 HDInsight 叢集共用預設 Blob 容器。 這可能會損毀工作歷程記錄，而叢集將會行為異常。 建議您為每個叢集使用不同的容器，並在所有相關叢集的部署中指定的連結儲存體帳戶 (而不是預設儲存體帳戶) 上放置共用的資料。 如需有關如何設定連結的儲存體帳戶的詳細資訊，請參閱 [建立 HDInsight 叢集][hdinsight-creation]。 不過，在刪除原始的 HDInsight 叢集後，您可以重複使用預設儲存容器。 至於 HBase 叢集，您可以利用被刪除的 HBase 叢集使用的預設 Blob 儲存容器來建立一個新的 HBase 叢集，藉此實際保留 HBase 資料表結構描述和資料。


### 使用 Azure 入口網站

從入口網站建立 HDInsight 叢集時，您可以選擇使用現有的儲存體帳戶或建立新的儲存體帳戶：

![hdinsight hadoop 建立資料來源](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###使用 Azure CLI

如果您有 [安裝和設定 Azure CLI](../xplat-cli-install.md), ，下列命令可以用來儲存體帳戶和容器。

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE]  `--type` 參數表示儲存體帳戶複寫的方式。 如需詳細資訊，請參閱 [Azure 儲存體複寫](../storage-redundancy.md)。 請勿使用 ZRS，因為 ZRS 不支援分頁 Blob、檔案、資料表或佇列。

系統會提示您指定將放置儲存體帳戶的地理區域。 您應該在您計劃建立 HDInsight 叢集的相同區域中建立儲存體帳戶。

建立儲存體帳戶後，使用下列命令來抓取儲存體帳戶金鑰：

    azure storage account keys list <storageaccountname>

若要建立容器，請使用下列命令：

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### 使用 Azure PowerShell

如果您 [安裝和設定 Azure PowerShell][powershell-install], ，您可以使用下列 Azure PowerShell 提示字元來建立儲存體帳戶和容器:

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName |  %{ $_.Key1 }
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## 定址 Blob 儲存體中的檔案

從 HDInsight 存取 Blob 儲存體中的檔案的 URI 配置如下：

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] 儲存體模擬器 (在 HDInsight 模擬器上執行) 上定址檔案的語法是 <i>wasb: / / & l t;ContainerName & gt;@storageemulator</i>。



URI 配置提供未加密的存取 (使用 *wasb:* 前置詞) 和 SSL 加密存取 (使用 *wasbs*)。 我們建議使用 *wasbs* 可能的話，即使存取在 Azure 中相同的資料中心內的資料。

&lt;BlobStorageContainerName&gt; 是指 Azure Blob 儲存體中的容器名稱。
&lt;StorageAccountName&gt; 是指 Azure 儲存體帳戶名稱。 需要使用完整網域名稱 (FQDN)。

如果 &lt;BlobStorageContainerName&gt; 和 &lt;StorageAccountName&gt; 都未指定，則會使用預設檔案系統。 對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。 例如， *mapreduce-hadoop-examples.jar* 可以使用下列其中一種來參考 HDInsight 叢集隨附的檔案:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] 檔案名稱是 <i>hadoop-examples.jar</i> HDInsight 2.1 和 1.6 版叢集中。


&lt;path&gt; 是檔案或目錄 HDFS 路徑名稱。 因為 Azure Blob 儲存體中的容器僅是機碼值存放區，所以並沒有真正的階層式檔案系統。 Blob 機碼內的斜線字元 ( / ) 會被解釋為目錄分隔符號。 例如，blob 名稱 *mapreduce-hadoop-examples.jar* 是:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] 當外部使用 blob HDInsight，大部分的公用程式執行無法辨識 WASB 格式，改為預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

## 使用 Azure CLI 存取 Blob

請使用下列命令來列出 Blob 相關的命令：

    azure storage blob

**使用 Azure CLI 上傳檔案的範例**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 下載檔案的範例**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 刪除檔案的範例**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 列出檔案的範例**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## 使用 Azure PowerShell 存取 Blob

> [AZURE.NOTE] 本節中的命令會提供使用 PowerShell 來存取儲存在 blob 資料的基本範例。 自訂使用 HDInsight 的更完整範例，請參閱 [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools)。

請使用下列命令來列出 Blob 相關的 Cmdlet：

    Get-Command *blob*

![與 Blob 相關的 Power Shell Cmdlet 清單。][img-hdi-powershell-blobcommands]

###上傳檔案

請參閱 [資料上傳至 HDInsight][hdinsight-upload-data]。

###下載檔案

下列指令碼將區塊 Blob 下載至目前的資料夾。 執行指令碼之前，請將目錄變更為您具有寫入權限的資料夾。

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.key1 }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

提供資源群組名稱和叢集名稱，您可以使用下列程式碼：

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###刪除檔案


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###列出檔案

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###使用未定義的儲存體帳戶執行 Hive 查詢

這個範例示範如何列出儲存體帳戶建立的程序期間定義未定義的資料夾。
    $clusterName ="<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## 後續步驟

在本文中，您學到如何搭配 HDInsight 使用 HDFS 相容的 Azure Blob 儲存體，也了解 Azure Blob 儲存體是 HDInsight 的基本元件。 這可讓您建立可擴充、 長期封存資料取得解決方案，使用 Azure Blob 儲存體，並利用 HDInsight 來揭開儲存的結構化和非結構化資料內的資訊。

如需詳細資訊，請參閱：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [在 HDInsight 上使用 Hive][hdinsight-use-hive]
* [在 HDInsight 上使用 Pig][hdinsight-use-pig]

[powershell-install]: ../install-configure-powershell.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

