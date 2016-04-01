<properties 
   pageTitle="在 HDInsight 中自訂佈建 Hadoop 叢集 |Microsoft Azure" 
   description="了解如何使用 Azure 傳統入口網站、Azure PowerShell、命令列或 .NET SDK 自訂佈建適用於 Azure HDInsight 的叢集。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2015"
   ms.author="jgao"/>

#在 HDInsight 中佈建 Hadoop 叢集 (英文)

了解如何規劃 HDInsight 叢集佈建作業。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [在 HDInsight 中佈建 Hadoop 叢集 (英文)](hdinsight-provision-clusters.md) 

**必要條件：**

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂閱。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


## 基本組態選項


- **叢集名稱**

    叢集名稱用來識別叢集。 叢集名稱必須遵循下列方針：
    
    - 欄位必須是包含 3 到 63 個字元的字串。
    - 欄位只能包含字母、數字和連字號。

- **訂用帳戶名稱**

    一個 HDInsight 叢集與一個 Azure 訂用帳戶綁定。
 
- **作業系統**

    您可以將 HDInsight 叢集佈建在下列兩個作業系統上：
    - **Windows (Windows Server 2012 R2 Datacenter) 上的 HDInsight**:
    - **Linux (Ubuntu 12.04 LTS 適用於 Linux) 上的 HDInsight （預覽）**: HDInsight 提供在 Azure 上設定 Linux 叢集的選項。 如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。 如需詳細資訊，請參閱 [開始在 HDInsight 中的 Linux 上使用 Hadoop](hdinsight-hadoop-linux-get-started.md)。 


- **HDInsight 版本**

    這可用來決定要用於此叢集的 HDInsight 版本。 如需詳細資訊，請參閱 [Hadoop 叢集版本和在 HDInsight 中的元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **叢集類型** 和 **叢集大小 （亦稱為資料節點）**

    HDInsight 可讓客戶部署各種不同的叢集類型，用於不同的資料分析工作負載。 目前提供的叢集類型包括：
    
    - Hadoop 叢集：適用於查詢和分析工作負載
    - HBase 叢集 ︰ NoSQL 工作負載
    - Storm 叢集：適用於即時事件的處理工作負載
    - Spark 叢集 (預覽)：適用於記憶體內部處理、互動式查詢、資料流和機器學習工作負載。

    ![HDInsight 叢集](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
    > [AZURE.NOTE] *Azure HDInsight 叢集* 也稱為 *HDInsight 中的 Hadoop 叢集*, ，或 *hdinsight*。 某些情況下，它搭配交替 *Hadoop 叢集*。 它們都代表裝載於 Microsoft Azure 環境中的 Hadoop 叢集。      

    在特定叢集類型中，各節點有不同的角色，可讓客戶針對特定角色，根據適合其工作負載的詳細資料來調整節點的大小。 舉例來說，若執行的分析作業類型會耗用大量記憶體，Hadoop 叢集將可使用大量記憶體來佈建背景工作節點。

    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png) 

    HDInsight 的 Hadoop 叢集利用兩個角色部署：

    - 前端節點 (2 個節點)
    - 資料節點 (至少 1 個節點)

    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png) 

    Hdinsight 的 HBase 叢集利用三個角色部署：
    - 前端伺服器 (2 個節點)
    - 區域伺服器 (至少 1 個節點)
    - 主要/Zookeeper 節點 (3 個節點)
    
    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

    HDInsight 的 Storm 叢集利用三個角色部署：
    - Nimbus 節點 (2 個節點)
    - 監督員伺服器 (至少 1 個節點)
    - Zookeeper 節點 (3 個節點)
    

    ![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

    Spark clusters for HDInsight are deployed with three roles:
    - Head node (2 nodes)
    - Worker node (at least 1 node)
    - Zookeeper nodes (3 nodes) (Free for A1 Zookeepers)

    Customers are billed for the usage of those nodes for the duration of the cluster’s life. Billing starts once a cluster is created and stops when the cluster is deleted (clusters can’t be de-allocated or put on hold). The cluster size affects the cluster price. For learning purposes, it is recommended to use 1 data node. For more information about HDInsight pricing, see [HDInsight pricing](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


    >[AZURE.NOTE] The cluster size limit varies among Azure subscriptions. Contact billing support to increase the limit.
    
- **區域/虛擬網路 （又稱為 位置）**

    ![Azure 區域](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

    如需支援的地區清單，按一下 [ **區域** 下拉式清單上的 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。

- **節點大小**

    ![hdinsight vm 節點大小](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

    選取 VM 的節點大小。 如需詳細資訊，請參閱 [雲端服務的大小](cloud-services-sizes-specs.md)

    根據選擇的 VM ，您的成本可能會有所不同。 HDInsight 針對叢集節點會使用所有標準層 VM。 如需 VM 大小對您價格影響的相關資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 定價</a>。


- **HDInsight 使用者**

    HDInsight 叢集可讓您在佈建期間設定兩個使用者帳戶：

    - HTTP 使用者。 使用 Azure 傳統入口網站的基本組態時，預設的使用者名稱為 admin。
    - RDP 使用者 (Windows 叢集)：用來連線到使用 RDP 的叢集。 當您建立帳戶時，必須將到期日設為從今天算起的 90 天內。 
    - SSH 使用者 (Linux 叢集)：用來連線到使用 SSH 的叢集。 依照步驟建立叢集之後，您可以建立其他 SSH 使用者帳戶 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Linux、 Unix 或 OS X 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-unix.md)。
  
 

- **Azure 儲存體帳戶**


    The original HDFS uses of many local disks on the cluster. HDInsight uses Azure Blob storage instead for data storage. Azure Blob storage is a robust, general-purpose storage solution that integrates seamlessly with HDInsight. Through a Hadoop distributed file system (HDFS) interface, the full set of components in HDInsight can operate directly on structured or unstructured data in Blob storage. Storing data in Blob storage enables you to safely delete the HDInsight clusters that are used for computation without losing user data. 
    
    During configuration, you must specify an Azure storage account and an Azure Blob storage container on the Azure storage account. Some provision process requires the Azure storage account and the Blob storage container created beforehand.  The Blob storage container is used as the default storage location by the cluster. Optionally, you can specify additional Azure Storage accounts (linked storage) that will be accessible by the cluster. In addition, the cluster can also access any Blob containers that are configured with full public read access or pulic read access for blobs only.  For more information on the restrict access, see [Manage Access to Azure Storage Resources](storage-manage-access-to-resources.md).

    ![HDInsight storage](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
    
    >[AZURE.NOTE] A Blob storage container provides a grouping of a set of blobs as shown in the image:
    
    ![Azure blob storage](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
      
    
    >[AZURE.WARNING] Don't share one Blob storage container for multiple clusters. This is not supported. 
    
    For more information on using secondary Blob stores, see [Using Azure Blob Storage with HDInsight](hdinsight-use-blob-storage.md).

- **Hive/Oozie 中繼存放區**

    中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。 使用中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在佈建新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。 根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。 刪除叢集時，嵌入的資料庫將無法保留中繼資料。 舉例來說，您使用 Hive 中繼存放區來佈建叢集。 您已建立一些 Hive 資料表。 當您刪除叢集並透過相同的 Hive 中繼存放區重建叢集之後，便可以看到您在原始的叢集中建立的 Hive 資料表。 

## 進階組態選項

>[AZURE.NOTE] 本節目前僅適用於 Windows 基礎的 HDInsight 叢集。

### 使用 HDInsight 叢集自訂功能來自訂叢集

有時候，您可能需要設定組態檔。  以下是一些需要設定的組態檔。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

叢集無法保留重新製作映像所造成的變更。  如需詳細資訊，請參閱 [角色執行個體重新啟動因作業系統升級而](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。 若要在叢集存留期間保留變更，您可以在佈建程序期間使用 HDInsight 叢集自訂。

以下是自訂 Hive 組態的 Azure PowerShell 指令碼範例：

    # hive-site.xml configuration 
    $hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
    
    $config = New-AzureHDInsightClusterConfig `
                -ClusterSizeInNodes $clusterSizeInNodes `
                -ClusterType $clusterType `
              | Set-AzureHDInsightDefaultStorage `
                -StorageAccountName $defaultStorageAccount `
                -StorageAccountKey $defaultStorageAccountKey `
                -StorageContainerName $defaultBlobContainer `
              | Add-AzureHDInsightConfigValues `
                -Hive $hiveConfigValues 
    
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

以下是更多自訂其他組態檔的範例：

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
    
    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
    
    # mapred-site.xml configuration
    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
    $MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
    
    # oozie-site.xml configuration
    $OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
    $OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
    
如需詳細資訊，請參閱標題為 Azim （英文） 部落格 [自訂 HDInsight 叢集佈建](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)。




### 使用指令碼動作來自訂叢集

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼會透過叫用 **指令碼動作**, ，這是組態選項，可用來從 Azure 傳統入口網站、 HDInsight Windows PowerShell cmdlet 或 HDInsight.NET SDK。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。


### 使用 Azure 虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/) 可讓您建立安全、 持續的網路，上面有您解決方案所需的資源。 虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

    ![diagram of cloud-only configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

    站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

    ![diagram of site-to-site configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

    點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

    ![diagram of point-to-site configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

如需有關虛擬網路功能、 優點和功能的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

> [AZURE.NOTE] 佈建 HDInsight 叢集之前，您必須建立 Azure 虛擬網路。 如需詳細資訊，請參閱 [虛擬網路組態工作](../services/virtual-machines/)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。 使用 Azure PowerShell Cmdlet Get-AzureVNetConfig 來檢查現有的 Azure 虛擬網路是否以位置為基礎。 如果您的虛擬網路並非以位置為基礎，您會有下列選項：
>
> - 將現有的虛擬網路組態匯出，然後建立新的虛擬網路。 所有新的虛擬網路是以預設的位置。
> - 移轉到以位置為基礎的虛擬網路。  請參閱 [現有服務移轉到區域範圍](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)。
>
> 強烈建議您一個叢集只指定一個子網路。

## 佈建工具

- Azure 傳統入口網站
- Azure PowerShell
- .NET SDK
- CLI

### 使用 Azure 傳統入口網站

您可以參考 [基本組態選項] 和 [進階組態選項] 以取得有關欄位的說明。 

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 傳統入口網站][azure-management-portal]。
2. 按一下 [ **+ 新增** 在頁面底部，按一下 [ **DATA SERVICES**, ，按一下 [ **HDINSIGHT**, ，然後按一下 [ **自訂建立**。
3. 在 **叢集詳細資料** 頁面上，輸入或選擇下列值 ︰

    - 叢集名稱
    - 訂用帳戶名稱
    - 叢集類型
    - 作業系統
    - HDInsight 版本

4. 在 **設定叢集** 頁面上，輸入或選取下列值 ︰

    - 資料節點
    - 區域/虛擬網路
    - 前端節點大小
    - 資料節點大小

5. 在 **Configure Cluster User** 頁面上，提供下列值 ︰

    - HTTP 使用者名稱
    - HTTP 密碼/確認密碼
    - 為叢集啟用遠端桌面
    - 輸入 Hive/Oozie 中繼存放區

6. 如果您選擇在前一個畫面中，輸入 Hive/Oozie metastore **設定 Hive/Oozie Metastore** 頁面上，提供下列值 ︰

    - Hive 中繼存放區資料庫
    - 資料庫使用者
    - 資料庫使用者密碼
    - Oozie 中繼存放區資料庫
    - 資料庫使用者
    - 資料庫使用者密碼

7. 在 **儲存體帳戶** 頁面上，提供下列值 ︰

    - 儲存體帳戶
    - 帳戶名稱
    - 預設容器
    - 其他儲存體帳戶

8. 在 **指令碼動作** 頁面上，按一下 **加入指令碼動作** 以提供有關您想要在建立叢集時自訂叢集，執行自訂指令碼的詳細資料。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

    - 名稱：指定指令碼動作的名稱。
    - 指令碼 URI：針對自訂叢集時所叫用的指令碼，指定統一資源識別項 (URI)。
    - 節點類型：指定執行自訂指令碼的節點。 您可以選擇 [所有節點]<b></b>、[僅限前端節點]<b></b> 或 [僅限資料節點]<b></b>。
    - 參數 ︰ 指定參數，如果所需的指令碼。</td></tr>

    您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。 加入指令碼之後，請按一下核取記號以開始佈建叢集。

### 使用 Azure PowerShell
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。 本節提供如何使用 Azure PowerShell 佈建 HDInsight 叢集的指示。 如需設定工作站以執行 HDInsight Windows PowerShell cmdlet 的詳細資訊，請參閱 [安裝和設定 Azure PowerShell](../install-configure-powershell.md)。 如需有關如何使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱 [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。 如需 HDInsight Windows PowerShell cmdlet 的清單，請參閱 [HDInsight cmdlet 參考文件](https://msdn.microsoft.com/library/azure/dn858087.aspx)。

> [AZURE.NOTE] 雖然本節中的指令碼可以用來設定 Azure 虛擬網路上的 HDInsight 叢集，但不會建立 Azure 虛擬網路。 如需建立 Azure 虛擬網路的詳細資訊，請參閱 [虛擬網路組態工作](../services/virtual-machines/)。

以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

您可以使用 Windows PowerShell 主控台或 Windows PowerShell 整合式指令碼環境 (ISE) 來執行指令碼。

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。 必須要有 Azure 儲存體帳戶和儲存體容器，才能夠建立 HDInsight 叢集。 儲存體帳戶必須與 HDInsight 叢集位於相同的資料中心。

**連接到您的 Azure 帳戶**

    Add-AzureAccount

系統會提示您輸入 Azure 帳號認證。

**建立 Azure 儲存體帳戶**

    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    $location = "<MicrosoftDataCenter>"             # For example, "West US"

    # Create an Azure Storage account
    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

如果您已有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，可以使用下列 Windows PowerShell 命令來擷取資訊：

    # List Storage accounts for the current subscription
    Get-AzureStorageAccount

    # List the keys for a Storage account
    Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure Blob 儲存體容器**

    $storageAccountName = "<StorageAccountName>"    # Provide the Storage account name
    $containerName="<ContainerName>"                # Provide a container name

    # Create a storage context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

> [AZURE.NOTE] Azure PowerShell cmdlet 是唯一建議用來變更在 HDInsight 叢集中的組態變數的方法。 透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。 透過 Azure PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Azure PowerShell 主控台視窗執行下列命令：

        $subscriptionName = "<AzureSubscriptionName>"     # The Azure subscription used for the HDInsight cluster to be created
    
        $storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
    
        $clusterName = "<HDInsightClusterName>"           # The name for the HDInsight cluster to be created
        $clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"
    
        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
    
        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $containerName = $clusterName               # Azure Blob container that is used as the default file system for the HDInsight cluster
    
        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
    
        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

    >[AZURE.NOTE] $HadoopUserName 和 $hadoopUserPassword 命令用來建立叢集的 Hadoop 使用者帳戶。 您將使用此帳戶來連接到叢集並執行工作。 如果您從 Azure 傳統入口網站使用 [快速建立] 選項佈建叢集，預設的 Hadoop 使用者名稱將為「admin」。 請不要將此帳戶與遠端桌面通訊協定 (RDP) 使用者帳戶相混淆。 RDP 使用者帳戶必須與 Hadoop 使用者帳戶不同。 如需詳細資訊，請參閱 [使用 Azure 傳統入口網站管理 HDInsight 中的 Hadoop 叢集][hdinsight-admin-portal]。

    叢集佈建可能需要幾分鐘的時間才會完成。

    ![HDI.CLI.Provision][image-hdi-ps-provision]

**使用自訂組態選項佈建 HDInsight 叢集**

佈建叢集時，您可以使用其他組態選項，例如連接至多個 Azure Blob 儲存體容器、使用虛擬網路，或使用 Azure SQL Database 來處理 Hive 和 Oozie 中繼存放區。 這可讓您區隔資料和中繼資料的存留期與叢集的存留期。

> [AZURE.NOTE] Windows PowerShell cmdlet 是唯一建議用來變更在 HDInsight 叢集中的組態變數的方法。 透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。 透過 Azure PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Windows PowerShell 視窗執行下列命令：

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the virtual network ID and subnet name
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"

        # Get the Storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

    >[AZURE.NOTE] 用於 metastore 的 Azure SQL database 必須能夠連線至其他 Azure 服務，包括 Azure HDInsight。 在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。 這是指執行 SQL Database 執行個體的伺服器。 一旦進入伺服器檢視，按一下 [ **設定**, ，然後 **Azure 服務**, ，按一下 [ **是**, ，然後按一下 [ **儲存**。

**列出 HDInsight 叢集**

- 從 Azure PowerShell 主控台視窗執行下列命令，以列出 HDInsight 叢集並確認是否已成功佈建叢集：

        Get-AzureHDInsightCluster -Name <ClusterName>


### 使用 Azure CLI

> [AZURE.NOTE] 截至 2014/8/29，Azure CLI 無法用於叢集與 Azure 虛擬網路建立關聯。

另一個佈建 HDInsight 叢集的選項是 Azure CLI。 Azure CLI 會在 Node.js 中實作。 此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。 您可以從下列位置安裝 CLI：

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Azure CLI** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

如需如何使用 Azure CLI 的一般指引，請參閱 [適用於 Mac、 Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md)。

上述指示將引導您如何在 Linux 和 Windows 上安裝跨平台命令列，以及接著如何使用命令列來佈建叢集。

- [設定適用於 Linux 的 Azure CLI](#clilin)
- [設定適用於 Windows 的 Azure CLI](#cliwin)
- [使用 Azure CLI 佈建 HDInsight 叢集](#cliprovision)

#### <a id="clilin"></a>設定適用於 Linux 的 Azure CLI

執行下列程序來設定您的 Linux 電腦使用 Azure 命令列工具 (Azure CLI)：

- 使用 Node.js 套件管理員 (NPM) 安裝 Azure CLI
- 連線到您的 Azure 訂用帳戶

**使用 NPM 安裝 Azure CLI**

1.  在 Linux 電腦上開啟終端機視窗，然後執行下列命令：

        sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.  執行下列命令以驗證安裝：

        azure hdinsight -h

    您可以使用 *-h* 切換顯示說明資訊的不同層級。 例如：

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**連線到您的 Azure 訂用帳戶**

使用 Azure CLI 之前，您必須先設定工作站與 Azure 之間的連線。 Azure CLI 會使用您的 Azure 訂閱資訊來連線至您的帳戶。 這項資訊可從 Azure 的發佈設定檔案取得。 接著可以匯入發佈設定檔成為 Azure CLI 後續作業所用的永久本機組態設定。 您只需匯入一次發佈設定。

> [AZURE.NOTE] 發行設定檔案包含敏感資訊。 Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。 在 Windows 中，修改資料夾屬性或使用 BitLocker Drive Encryption。


1.  開啟終端機視窗。
2.  執行下列命令，以登入您的 Azure 訂用帳戶：

        azure account download

    ![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

    此命令會開啟可下載發行設定檔案的網頁。 如果網頁未開啟，請按一下終端機視窗中的連結以開啟瀏覽器頁面並登入入口網站。

3.  將發行設定檔案下載至電腦。
5.  在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

        azure account import <path/to/the/file>


#### <a id="cliwin"></a>設定適用於 Windows 的 Azure CLI

執行下列程序來設定您的 Windows 電腦使用 Azure CLI：

- 安裝 Azure CLI (使用 NPM 或 Windows Installer)
- 下載及匯入 Azure 帳戶發佈設定


Azure CLI 可使用 NPM 或 Windows Installer 進行安裝。 Microsoft 建議您僅使用下列兩個選項之一來進行安裝。

**使用 NPM 安裝 Azure CLI**

1.  瀏覽至 **www.nodejs.org**。
2.  按一下 [ **安裝** 並依照指示使用預設設定。
3.  開啟 **命令提示字元** (或 *Azure 命令提示字元*, ，或 *vs2012 開發人員命令提示字元*) 從您的工作站。
4.  在命令提示字元視窗中執行下列命令：

        npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

    > [AZURE.NOTE] 如果您收到錯誤，指出找不到 NPM 命令，請驗證下列路徑是否在 PATH 環境變數 ︰ <i>C:\Program Files (x86) \nodejs;C:\Users\[username]\AppData\Roaming\npm</i> 或 <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.  執行下列命令以驗證安裝：

        azure hdinsight -h

    您可以使用 *-h* 切換顯示說明資訊的不同層級。 例如：

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**使用 Windows Installer 安裝 Azure CLI**

1.  瀏覽至 **http://azure.microsoft.com/downloads/**。
2.  向下捲動至 **命令列工具** 區段，然後再按一下 **Azure 命令列介面** 並依照 Web Platform Installer 精靈操作。

**下載及匯入發佈設定**

使用 Azure CLI 之前，您必須先設定工作站與 Azure 之間的連線。 Azure CLI 會使用您的 Azure 訂閱資訊來連線至您的帳戶。 這項資訊可從 Azure 的發佈設定檔案取得。 接著可以匯入發佈設定檔成為 Azure CLI 後續作業所用的永久本機組態設定。 您只需匯入一次發佈設定。

> [AZURE.NOTE] 發行設定檔案包含敏感資訊。 Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。 在 Windows 中，修改資料夾屬性或使用 BitLocker。


1.  開啟 **命令提示字元**。
2.  執行下列命令來下載發佈設定檔案：

        azure account download

    ![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

    此命令會開啟可下載發行設定檔案的網頁。

3.  在提示時儲存檔案，請按一下 [ **儲存** 並提供檔案必須儲存的位置。
5.  在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

        azure account import <path/to/the/file>

#### <a id="cliprovision"></a>使用 Azure CLI 佈建 HDInsight 叢集

以下是使用 Azure CLI 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 佈建叢集

**建立 Azure 儲存體帳戶**

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。 必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。 儲存體帳戶必須位於相同的資料中心內。

- 在命令提示字元視窗中執行下列命令，以建立 Azure 儲存體帳戶：

        azure storage account create [options] <StorageAccountName>

    出現位置提示時，請選取可佈建 HDInsight 叢集的位置。 儲存體必須與 HDInsight 叢集位於相同的位置。 目前，只有 **東亞**, ，**東南亞**, ，**北歐**, ，**西歐**, ，**美國東部**, ，**美國西部**, ，**美國中北部**, ，和 **中南部** 地區可代管 HDInsight 叢集。  

如需使用 Azure 傳統入口網站建立 Azure 儲存體帳戶的資訊，請參閱 [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account.md)。

如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則可使用下列命令來擷取資訊：

    -- Lists Storage accounts
    azure storage account list

    -- Shows information for a Storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a Storage account
    azure storage account keys list <StorageAccountName>

如需使用 Azure 傳統入口網站來取得資訊的詳細資訊，請參閱 *How to ︰ 檢視、 複製和重新產生儲存體存取金鑰* 區段 [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account.md)。

HDInsight 叢集同時要求儲存體帳戶內含有容器。 如果您提供的儲存體帳戶沒有容器， *azure hdinsight 叢集建立* 命令會提示您輸入容器名稱，並建立容器。 不過，如果您想要預先建立容器，您可以使用下列命令：

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

- 在命令提示字元視窗中執行下列命令：

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

    ![HDI.CLIClusterCreation][image-cli-clustercreation]


**使用組態檔佈建 HDInsight 叢集**

一般而言，您會佈建 HDInsight 叢集、執行工作，然後將此叢集刪除，以降低成本。 Azure CLI 可讓您選擇將組態儲存至檔案，以便在每次佈建叢集時重複使用。

- 在命令提示字元視窗中執行下列命令：


        #Create the config file
        azure hdinsight cluster config create <file>

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

        #If required, include commands to use additional Blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster by using the config file
        azure hdinsight cluster create --config <file>

    >[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.


    ![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**列出並顯示叢集詳細資料**

- 使用下列命令，以列出並顯示叢集詳細資料：

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][image-cli-clusterlisting]


**刪除叢集**

- 使用下列命令刪除叢集：

        azure hdinsight cluster delete <ClusterName>



### 使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時必須執行的程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以下載最新發佈的 sdk，從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)。 下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。 如需指示，請參閱 [建立自我簽署的憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013 或 2015。

2. 從 **檔案** ] 功能表上，按一下 [ **新增**, ，然後按一下 [ **專案**。

3. 從 **新的專案**, ，輸入或選取下列值 ︰

  	|屬性|值|
  	|--------|-----|
  	|範本|Templates/Visual C#/Windows/Console Application|
  	|名稱|CreateHDICluster|

4. 按一下 [ **確定** 以建立專案。

5. 從 **工具** ] 功能表上，按一下 [ **Nuget 封裝管理員**, ，然後按一下 [ **Package Manager Console**。

6. 在主控台中執行下列命令，以安裝套件：

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

7. 從 [方案總管] 中，按兩下 **Program.cs** 以開啟它。
8. 使用下列程式碼來取代此程式碼：

        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;

        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
                private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

                private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
                private const int NewClusterNumNodes = <NUMBER OF NODES>;
                private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
                private const OSType NewClusterOSType = OSType.Windows;
                private const string NewClusterVersion = "3.2";

                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<HTTP USER PASSWORD>";
                private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
                private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
                private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
        
        
                private static void Main(string[] args)
                {
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    CreateCluster();
                }
        
                public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
        
                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
        
        
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        Location = NewClusterLocation,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,

                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,

                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingContainer
                    };
        
                    _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
                }
            }
        }
        
10. 取代類別成員值。

**執行應用程式**

Visual Studio 中開啟應用程式時，請按 **F5** 執行應用程式。 主控台視窗會開啟並顯示應用程式的狀態。 建立 HDInsight 叢集可能需要幾分鐘的時間。


##<a id="nextsteps"></a> 後續步驟
在本文中，您學到幾種佈建 HDInsight 叢集的方法。 若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-get-started.md) -了解如何開始使用 HDInsight 叢集
* [搭配 HDInsight 使用 Sqoop](hdinsight-use-sqoop.md) -了解如何在 HDInsight 與 SQL Database 或 SQL Server 之間複製資料
* [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md) -了解如何搭配 HDInsight 使用 Azure PowerShell
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) -了解如何以程式設計方式提交工作至 HDInsight
* [Azure HDInsight SDK 文件] [hdinsight-sdk-documentation] -探索 HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com


