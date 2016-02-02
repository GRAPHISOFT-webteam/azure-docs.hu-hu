<properties
    pageTitle="使用 PowerShell 管理 HDInsight 中的 Hadoop 叢集 | Microsoft Azure"
    description="了解如何使用 Azure PowerShell 對 HDInsight 中的 Hadoop 叢集執行管理工作。"
    services="hdinsight"
    editor="cgronlun"
    manager="paulettm"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="jgao"/>


# 使用 Azure PowerShell 管理 HDInsight 上的 Hadoop 叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。 在本文中，您將了解如何使用本機 Azure PowerShell 主控台，透過使用 Windows PowerShell 來管理 Azure HDInsight 上的 Hadoop 叢集。 如需 HDInsight PowerShell cmdlet 的清單，請參閱 [HDInsight cmdlet 參考 [hdinsight-powershell-][hdinsight-powershell-reference]。



**必要條件**

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 請安裝 Azure PowerShell 1.0 以上版本。

首先，您必須先解除安裝 0.9x 版本。

檢查已安裝的 PowerShell 版本：

    Get-Module *azure*

若要解除安裝較舊的版本，請在控制台中執行 [程式和功能]。

共有兩個安裝 Azure PowerShell 的主要選項。

- [PowerShell 組件庫](https://www.powershellgallery.com/)。 從提高權限的 PowerShell ISE 或提高權限的 Windows PowerShell 主控台執行下列命令：

      # Install the Azure Resource Manager modules from PowerShell Gallery
      Install-Module AzureRM
      Install-AzureRM
    
      # Install the Azure Service Management module from PowerShell Gallery
      Install-Module Azure
    
      # Import AzureRM modules for the given version manifest in the AzureRM module
      Import-AzureRM
    
      # Import Azure Service Management module
      Import-Module Azure

  如需詳細資訊，請參閱 [PowerShell 組件庫](https://www.powershellgallery.com/)。

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps)。 如果您已安裝 Azure PowerShell 0.9.x，系統將提示您解除安裝 0.9.x。 如果您是從 PowerShell 資源庫安裝 Azure PowerShell 模組，必須在安裝安裝程式之前先移除模組，以確保 Azure PowerShell 環境保持一致。 如需指示，請參閱 [透過 WebPI 安裝 Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

WebPI 每個月都會更新。 PowerShell 資源庫將持續更新。 若您想要透過 PowerShell 資源庫安裝，則此為取得最新最優異 Azure PowerShell 的首要管道。

## 建立叢集

HDInsight 叢集需要 Azure 儲存體帳戶上的 Azure 資源群組和 Blob 容器：

- Azure 資源群組是 Azure 資源的邏輯容器。 Azure 資源群組與 HDInsight 叢集不一定要在相同的位置。 如需詳細資訊，請參閱 [使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md)。
- HDInsight 會使用 Azure 儲存體帳戶的 Blob 容器做為預設檔案系統。 必須要有 Azure 儲存體帳號和儲存容器，您才能建立 HDInsight 叢集。 預設儲存體帳戶必須與 HDInsight 叢集並存於相同位置。

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**連接到 Azure**

    Login-AzureRmAccount
    Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
    Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

如果您多個 Azure 訂用帳戶時會呼叫 **Select-AzureRMSubscription**。

**建立新的資源群組**

    New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**建立 Azure 儲存體帳戶**

    New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
    
    Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]


如需使用 Azure 入口網站建立 Azure 儲存體帳戶資訊，請參閱 [關於 Azure 儲存體帳戶](storage-create-storage-account.md)。

如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列命令來擷取資訊：

    # List Storage accounts for the current subscription
    Get-AzureRmStorageAccount
    # List the keys for a Storage account
    Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

如需使用入口網站中，以取得資訊的詳細資訊，請參閱 「 檢視、 複製和重新產生儲存體存取金鑰 」 一節 [關於 Azure 儲存體帳戶](storage-create-storage-account.md)。

**建立 Azure 儲存容器**

Azure PowerShell 無法在 HDInsight 建立程序期間建立 Blob 容器。 您可以使用下列指令碼來建立 Blob 容器：

    $resourceGroupName = "<AzureResoureGroupName>"
    $storageAccountName = "<Azure Storage Account Name>"
    $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
    $containerName="<AzureBlobContainerName>"
    
    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**建立叢集**

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

    $resourceGroupName = "<AzureResoureGroupName>"
    
    $storageAccountName = "<Azure Storage Account Name>"
    $containerName = "<AzureBlobContainerName>"
    
    $clusterName = "<HDInsightClusterName>"
    $location = "<AzureDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>
    
    # Get the Storage account key
    $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
    
    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes $clusterNodes

## 列出叢集

使用下列命令列出目前訂用帳戶中的所有叢集：

    Get-AzureRmHDInsightCluster

## 顯示叢集

使用下列命令顯示目前訂用帳戶中特定叢集的詳細資料：

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

## 刪除叢集

使用下列命令刪除叢集：

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

## 調整叢集

叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的背景工作節點數目，而不需要重新建立叢集。
>[AZURE.NOTE] 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。 請參閱 [熟悉叢集入口網站介面](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)。

變更 HDInsight 支援的每一種叢集所用的資料節點數目會有何影響：

- Hadoop

    您可以順暢地增加正在執行的 Hadoop 叢集中背景工作節點數目，而不會影響任何擱置或執行中的工作。 您也可以在作業進行當中提交新工作。 系統會順暢處理失敗的調整作業，讓叢集永保正常運作狀態。

    減少資料節點數目以縮減 Hadoop 叢集時，系統會重新啟動叢集中的部分服務。 這會導致所有執行中和擱置的工作在調整作業完成時失敗。 但您可以在作業完成後重新提交這些工作。

- HBase

    您可以順暢地在 HBase 叢集運作時對其新增或移除資料節點。 區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。 但是，您也可以手動平衡區域伺服器，方法是登入叢集的前端節點，然後從命令提示字元視窗執行下列命令：

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    如需有關使用 HBase shell 的詳細資訊，請參閱]
- Storm

  您可以順暢地在 Storm 叢集運作時對其新增或移除資料節點。 但在調整作業順利完成後，您需要重新平衡拓撲。

  您可以使用兩種方式來完成重新平衡作業：

  * Storm Web UI
  * 命令列介面 (CLI) 工具

請參閱 [Apache Storm 文件](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) 如需詳細資訊。

HDInsight 叢集上有提供 Storm Web UI：

![hdinsight storm scale rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

以下是如何使用 CLI 命令重新平衡 Storm 拓撲的範例：

    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


若要使用 Azure PowerShell 變更 Hadoop 叢集大小，請從用戶端電腦執行下列命令：

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>

## 授與/撤銷存取權

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


預設會授與這些服務的存取權。 您可以撤銷/授與存取權。 撤銷：

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

授與：

    $clusterName = "<HDInsight Cluster Name>"
    
    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "Pass@word123"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)
    
    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] 透過授與/撤銷存取權，您將重設叢的使用者名稱和密碼。

這也可以透過入口網站完成。 請參閱 [使用 Azure 入口網站 ][hdinsight-admin-portal]。

## 更新 HTTP 使用者認證

它是相同的程序 [授與/撤銷 HTTP 存取](#grant/revoke-access)。如果已授與叢集 HTTP 存取，您必須先撤銷。 然後再使用新的 HTTP 使用者認證授與存取權。


## 尋找預設的儲存體帳戶

下列 Powershell 指令碼示範如何取得叢集的預設儲存體帳戶名稱和預設儲存體帳戶金鑰。

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

## 尋找資源群組

在 ARM 模式中，每個 HDInsight 叢集皆屬於一個 Azure 資源群組。 尋找資源群組：

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup

## 提交工作

**提交 MapReduce 作業**

請參閱 [以 Windows 為基礎的 HDInsight 執行 Hadoop MapReduce 範例](hdinsight-run-samples.md)。

**提交 Hive 作業**

請參閱 [使用 PowerShell 執行 Hive 查詢](hdinsight-hadoop-use-hive-powershell.md)。

**提交 Pig 作業**

請參閱 [使用 PowerShell 執行 Pig 工作](hdinsight-hadoop-use-pig-powershell.md)。

**提交 Sqoop 作業**

請參閱 [搭配 HDInsight 使用 Sqoop](hdinsight-use-sqoop.md)。

**提交 Oozie 作業**

請參閱 [來定義並執行工作流程在 HDInsight Hadoop 上使用 Oozie](hdinsight-use-oozie.md)。

## 將資料上傳至 Azure Blob 儲存體

請參閱 [資料上傳至 HDInsight ][hdinsight-upload-data]。


## 另請參閱

* [HDInsight cmdlet 參考文件 ][hdinsight-powershell-reference]
* [使用 Azure 入口網站 ][hdinsight-admin-portal]
* [使用 ][hdinsight-admin-cli]
* [建立 HDInsight 叢集的 ][hdinsight-provision]
* [將資料上傳至 HDInsight ][hdinsight-upload-data]
* [以程式設計方式提交 Hadoop 工作 ][hdinsight-submit-jobs]
* [開始使用 Azure HDInsight ][hdinsight-get-started]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[hdinsight-get-started]: ../hdinsight-get-started.md 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md 
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md 
[hdinsight-storage]: ../hdinsight-use-blob-storage.md 
[hdinsight-use-hive]: hdinsight-use-hive.md 
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md 
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx 
[powershell-install-configure]: ../install-configure-powershell.md 
[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png 

