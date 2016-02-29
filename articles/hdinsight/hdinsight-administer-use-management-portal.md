<properties
    pageTitle="使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集 | Microsoft Azure"
    description="了解如何管理 HDInsight 服務。 建立 HDInsight 叢集、開啟互動式 JavaScript 主控台，以及開啟 Hadoop 命令主控台。"
    services="hdinsight"
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
    ms.date="11/24/2015"
    ms.author="jgao"/>

# 使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

使用 [Azure 入口網站][azure-portal], ，您可以建立在 Azure HDInsight Hadoop 叢集、 變更 Hadoop 使用者密碼，並啟用遠端桌面通訊協定 (RDP)，以便存取叢集上的 Hadoop 命令主控台。

本文的資訊僅適用於以 Windows 為基礎的 HDInsight 叢集。 如需管理以 Linux 為基礎的叢集的詳細資訊，請按一下上面的索引標籤選取器。

按一下索引標籤選取器，以取得使用其他工具在 HDInsight 中建立 Hadoop 叢集的詳細資訊。 

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-management-portal-v1.md)

**必要條件**

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure 儲存體帳戶** -HDInsight 叢集使用 Azure Blob 儲存體容器做為預設檔案系統。 如需 Azure Blob 儲存體如何提供順暢 HDInsight 叢集使用體驗的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。 如需建立 Azure 儲存體帳戶的詳細資訊，請參閱 [如何建立儲存體帳戶](../storage-create-storage-account.md)。

##開啟入口網站

1. 登入 [https://portal.azure.com](https://portal.azure.com)。
2. 開啟入口網站之後，您可以：

    - 按一下 [ **新增** 從左邊的功能表上，建立新的叢集:
    
        ![新的 HDInsight 叢集按鈕](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
    - 按一下 [ **HDInsight 叢集** 從左側的功能表。
    
        ![Azure 入口網站 HDInsight 叢集按鈕](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

    如果 **HDInsight** 不會出現在左窗格中，按一下 **瀏覽**。 

    ![Azure 入口網站瀏覽叢集按鈕](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##建立叢集

使用入口網站建立指示，請參閱 [建立 HDInsight 叢集](hdinsight-provision-clusters.md#create-using-the-preview-portal)。

HDInsight 可以與很多 Hadoop 元件搭配使用。 已驗證和支援的元件清單 
請參閱 [Azure HDInsight 是 Hadoop 的什麼版本](hdinsight-component-versioning.md)。 您可以使用下列其中一個選項來自訂 HDInsight：

- 使用 [指令碼動作] 來執行可自訂叢集的自訂指令碼，以變更叢集組態或安裝自訂元件 (如 Giraph 或 Solr)。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。
- 在叢集建立期間，使用 HDInsight .NET SDK 或 Azure PowerShell 中的叢集自訂參數。 即會在叢集的存留期保留這些組態變更，而且它們不受叢集節點重新製作映像的影響，而 Azure 平台會定期執行重新製作映像以進行維護。 如需有關如何使用叢集自訂參數的詳細資訊，請參閱 [建立 HDInsight 叢集](hdinsight-provision-clusters.md)。
- 您可以使用 JAR 檔案形式在叢集上執行一些原生 Java 元件 (例如 Mahout 和 Cascading)。 這些 JAR 檔案可以配送至 Azure Blob 儲存體，並透過 Hadoop 工作提交機制提交至 HDInsight 叢集。 如需詳細資訊，請參閱 [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。

    >[AZURE.NOTE] 如果您有 JAR 檔案部署至 HDInsight 叢集的問題，或呼叫 JAR 檔案上的 HDInsight 叢集，請連絡 [Microsoft 支援服務](http://azure.microsoft.com/support/options/)。

    > Cascading 不受 HDInsight 支援，而且不符合「Microsoft 支援」的資格。 如需支援元件的清單，請參閱 [的 HDInsight 所提供叢集版本的新功能?](hdinsight-component-versioning.md)。

不支援使用遠端桌面連線在叢集上安裝自訂軟體。 您應該避免在前端節點的磁碟機上儲存任何檔案，因為這些檔案會在您需要重新建立叢集時遺失。 建議您將檔案儲存至 Azure Blob 儲存體。 Blob 儲存體是持續性的。

##列出和顯示叢集

1. 登入 [https://portal.azure.com](https://portal.azure.com)。
2. 按一下 [ **HDInsight 叢集** 從左側的功能表:
    
    ![Azure 入口網站 HDInsight 叢集按鈕](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)
        
    如果 **HDInsight** 不會出現在左窗格中，按一下 **瀏覽**: 

    ![Azure 入口網站瀏覽叢集按鈕](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

    您應該會看見現有的叢集清單 (如果有的話)：
    
    ![Azure 入口網站叢集清單](./media/hdinsight-administer-use-management-portal/hdinsight-list-clusters.png)

3. 使用 **篩選項目** 和 「 訂閱 」，若要縮小清單。
4. 按兩下清單中的叢集以顯示詳細資料。

    **功能表和 essentials**:

    ![Azure 入口網站 hdinsight 叢集基本功能](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
    
    - 若要自訂 memu，] 功能表上按一下滑鼠右鍵，然後按一下 [ **自訂**。
    - **設定** 和 **所有設定**: 顯示 **設定** 叢集中，可讓您存取叢集的詳細的組態資訊的分頁。
    - **儀表板**, ，**叢集儀表板** 和 * * URL: 這些是所有的方式來存取叢集儀表板，其為 Linux 型叢集的 Ambari Web。
    - **遠端桌面**: 連接到使用 RDP 的叢集。
    - **調整叢集**: 可讓您變更 workder 此叢集的節點數目。
    - **刪除**: 刪除叢集。
    - **快速入門 (![定域機組和雷電圖示 = 快速入門](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: 顯示資訊可協助您開始使用 HDInsight。
    - **使用者 (![使用者圖示](./media/hdinsight-administer-use-portal-linux/users.png))**: 可讓您設定的權限 _入口網站的管理_ 的其他使用者此叢集上您的 Azure 訂閱。
    
        > [AZURE.IMPORTANT] 這 _只_ 影響存取和 Azure preview 入口網站，此叢集的權限，以及誰可以連線到，或將工作提交至 HDInsight 叢集上沒有作用。
    - **標記 (![標籤圖示](./media/hdinsight-administer-use-portal-linux/tags.png))**: 標記可讓您設定來定義自訂分類您的雲端服務的索引鍵/值組。 例如，您可以建立名為 __專案__, ，然後使用共通的值與特定專案相關聯的所有服務。
    - **文件**: Azure hdinsight 的文件的連結。
    
    > [AZURE.IMPORTANT] 若要管理的 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。 如需有關如何使用 Ambari 的詳細資訊，請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

    **使用**:
    
    ![Azure 入口網站 hdinsight 叢集使用量](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
    
5. 按一下 [ **設定**。

    ![Azure 入口網站 hdinsight 叢集使用量](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

    - **屬性**: 檢視叢集內容。
    - **叢集 AAD 身分識別**: 
    - **Azure 儲存體金鑰**: 檢視預設儲存體帳戶和其索引鍵。 儲存體帳戶是叢集建立程序期間的組態。
    - **叢集登入**: 變更叢集 HTTP 使用者名稱和密碼。
    - **外部中繼存放區**: 檢視 Hive 和 Oozie metastore。 中繼存放區只可以在叢集建立程序期間進行設定。
    - **調整叢集**: 增減叢集背景工作節點數目。
    - **遠端桌面**: 啟用和停用遠端桌面 (RDP) 存取和設定 RDP 使用者名稱。  RDP 使用者名稱必須與 HTTP 使用者名稱不同。
    - **資料錄的夥伴**:
    
    > [AZURE.NOTE] 這是可用的設定; 一般清單並非所有將會出現的所有叢集類型。

6. 按一下 [ **屬性**:

    屬性如下所列：
    
    - **主機名稱**: 叢集名稱。
    - **叢集 URL**。
    - **狀態**: 包括中止、 接受，ClusterStorageProvisioned、 AzureVMConfiguration、 HDInsightConfiguration，正常運作，執行時，錯誤，刪除，刪除，逾時、 DeleteQueued、 DeleteTimedout、 DeleteError、 PatchQueued、 CertRolloverQueued、 ResizeQueued、 ClusterCustomization
    - **區域**: Azure 位置。 如需支援的 Azure 位置，請參閱 **區域** 上的下拉式清單方塊 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。
    - **建立資料**。
    - **作業系統**: 可能是 **Windows** 或 **Linux**。
    - **型別**: Hadoop、 HBase、 Storm、 Spark。 
    - **版本**。 請參閱 [HDInsight 版本](hdinsight-component-versioning.md)
    - **Subscriotion**: 訂用帳戶名稱。
    - **Embed-detail 識別碼**。
    - **主要資料來源**。 用來做為預設 Hadoop 檔案系統的 Azure Blob 儲存體帳戶。
    - **背景工作節點定價層**。
    - **前端節點定價層**。

##刪除叢集

刪除叢集時，並不會刪除預設的儲存體帳戶或任何連結的儲存體帳戶。 您可以使用相同的儲存體帳戶和相同的中繼存放區重新建立叢集。

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **刪除** 從頂端功能表中，並依照指示進行。

另請參閱 [暫停/關閉叢集](#pauseshut-down-clusters)。

##調整叢集
叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的背景工作節點數目，而不需要重新建立叢集。

>[AZURE.NOTE] 只有叢集的 HDInsight 3.1.3 版或更高版本才支援。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。  請參閱 [清單和顯示叢集](hdinsight-adminster-use-management-portal/#list-and-show-clusters)。

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


**調整叢集**

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **設定** 從頂端功能表中，然後按一下 [ **延展叢集**。
4. 輸入 **數字的背景工作節點**。 叢集節點的數目限制會因 Azure 訂用帳戶而有所不同。 請連絡帳務支援提高限制。  成本資訊會反映您對節點數目所做的變更。

    ![hdinsight hadoop hbase storm spark scale](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##暫停/關閉叢集

大部分 Hadoop 工作是只會偶爾執行的批次工作。 對於大部分的 Hadoop 叢集而言，叢集長時間並未用於處理。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。
您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

有許多方法可以設計程序：

- 使用 Azure Data Factory。 請參閱 [Azure HDInsight 連結服務](../data-factory-compute-linked-services.md/#azure-hdinsight-linked-service) 和 [轉換和分析使用 Azure Data Factory](../data-factory-data-transformation-activities.md) 點播與自行定義 hdinsight 連結服務。
- 使用 Azure PowerShell。  請參閱 [分析航班延誤資料](hdinsight-analyze-flight-delay-data.md)。
- 使用 Azure CLI。 請參閱 [使用 Azure CLI 管理 HDInsight 叢集](hdinsight-administer-use-command-line.md)。
- 使用 HDInsight .NET SDK。 請參閱 [提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。

定價資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。 若要從入口網站刪除叢集，請參閱 [刪除叢集](#delete-clusters)

##變更叢集使用者名稱

HDInsight 叢集可以有兩個使用者帳戶。 HDInsight 叢集使用者帳戶是在建立程序期間所建立。 您也可以建立 RDP 使用者帳戶，以透過 RDP 來存取叢集。 請參閱 [啟用遠端桌面](#connect-to-hdinsight-clusters-by-using-rdp)。

**變更 HDInsight 叢集使用者名稱和密碼**

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **設定** 從頂端功能表中，然後按一下 [ **叢集登入**。
4. 如果 **叢集登入** 已啟用，您必須按一下 **停用**, ，然後按一下 [ **啟用** 才能變更使用者名稱和密碼...
4. 變更 **叢集登入名稱** 和/或 **叢集登入密碼**, ，然後按一下 [ **儲存**。

    ![hdinsight change cluster user username password http user](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##授與/撤銷存取權

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

預設會授與這些服務的存取權。 您可以從 Azure 入口網站撤銷/授與存取權。

>[AZURE.NOTE] 透過授與/撤銷存取權，您將會重設叢集使用者名稱和密碼。

**授與/撤銷 HTTP Web 服務存取**

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **設定** 從頂端功能表中，然後按一下 [ **叢集登入**。
4. 如果 **叢集登入** 已啟用，您必須按一下 **停用**, ，然後按一下 [ **啟用** 才能變更使用者名稱和密碼...
6. 如 **叢集登入使用者名稱** 和 **叢集登入密碼**, ，輸入新的使用者名稱和密碼 (分別) 叢集。
7. 按一下 [ **儲存**。

    ![hdinsight grand remove http web service access](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##尋找預設的儲存體帳戶

每個 HDInsight 叢集都有預設的儲存體帳戶。 預設儲存體帳戶和其金鑰在叢集 apears **設定**/**屬性**/**Azure 儲存體金鑰**。 請參閱 [清單和顯示叢集](#list-and-show-clusters)。

    
##尋找資源群組 

在 ARM 模式中，每個 HDInsight 叢集是隨著 Azure 資源群組一起建立。 叢集所屬的 Azure 資源群組會出現於：

- 叢集清單中有 **資源群組** 資料行。
- 叢集 **基本** 並排顯示。  

請參閱 [清單和顯示叢集](#list-and-show-clusters)。
   
##開啟 HDInsight 查詢主控台

HDInsight 查詢主控台包括下列功能：

- **開始使用組件庫**: 若要使用組件庫，請參閱 [使用 Azure HDInsight Getting Started Gallery 了解的 Hadoop](hdinsight-learn-hadoop-use-sample-gallery.md)。
- **Hive 編輯器**: 提交 Hive 工作的 GUI 網頁介面。  請參閱 [使用查詢主控台執行 Hive 查詢](hdinsight-hadoop-use-hive-query-console.md)。

    ![hdinsight 入口網站 hive 編輯器](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **作業歷程記錄**: 監視 Hadoop 工作。  

    ![hdinsight 入口網站工作歷程記錄](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    按一下 [ **查詢名稱** 以顯示詳細資料，包括作業屬性 **工作查詢**, ，和 * * 工作輸出。 您也可以將查詢和輸出下載至您的工作站。

- **檔案瀏覽器**: 瀏覽預設儲存體帳戶和連結的儲存體帳戶。

    ![hdinsight 入口網站檔案瀏覽器瀏覽](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    螢幕擷取畫面， **<Account>** 類型表示的項目是 Azure 儲存體帳戶。  按一下帳戶名稱以瀏覽檔案。
    
- **Hadoop UI**。

    ![hdinsight 入口網站 Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
    
    從 **Hadoop UI*, ，您可以瀏覽檔案，並查看記錄檔。 

- **Yarn UI**。

    ![hdinsight 入口網站 YARN UI](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##執行 Hive 查詢

若要從入口網站執行 Hive 工作，按一下 [ **Hive 編輯器** HDInsight 查詢主控台中。 請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。

##監視工作

若要監控從入口網站的工作，請按一下 [ **工作歷程記錄** HDInsight 查詢主控台中。 請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。

##瀏覽檔案

若要瀏覽檔案儲存在預設儲存體帳戶和連結的儲存體帳戶，請按一下 [ **檔案瀏覽器** HDInsight 查詢主控台中。 請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。

您也可以使用 **瀏覽檔案系統** 公用程式，透過 **Hadoop UI** HDInsight 主控台中。  請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。



##監視叢集使用量

 __使用量__ 區段的 HDInsight 叢集] 刀鋒視窗中顯示與 HDInsight 搭配使用的訂閱可用的核心數目，以及配置給此叢集和此叢集中節點的配置方式的核心數目的相關資訊。 請參閱 [清單和顯示叢集](#list-and-show-clusters)。

> [AZURE.IMPORTANT] 若要監視 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。 如需有關如何使用 Ambari 的詳細資訊，請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)


##開啟 Hadoop UI

若要監視的叢集，瀏覽檔案系統中，並檢查記錄檔，請按一下 **Hadoop UI** HDInsight 查詢主控台中。 請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。

##開啟 Yarn UI

若要使用 Yarn 使用者介面，請按一下 [ **Yarn UI** HDInsight 查詢主控台中。 請參閱 [開啟 HDInsight 查詢主控台](#open-hdinsight-query-console)。

##使用 RDP 連接到叢集

您在建立叢集時所提供的叢集認證可以存取叢集上的服務，但是無法透過遠端桌面來存取叢集本身。 當您佈建叢集時或叢集佈建完成後，可以開啟遠端桌面存取。 在建立時啟用遠端桌面的相關指示，請參閱 [建立 HDInsight 叢集](hdinsight-provision-clusters.md)。

**啟用遠端桌面**

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **設定** 從頂端功能表中，然後按一下 [ **遠端桌面**。
4. 輸入 **過期於**, ，**遠端桌面使用者名稱** 和 **遠端桌面密碼**, ，然後按一下 [ **啟用**。

    ![hdinsight enable disable configure remote desktop](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    [到期日] 的預設值是一週。
> [AZURE.NOTE] 您也可以使用 HDInsight.NET SDK 在叢集上啟用遠端桌面。 使用 **EnableRdp** 以下列方式在 HDInsight 用戶端物件上的方法: **用戶端。EnableRdp (clustername，location，"rdpuser"，"rdppassword"，DateTime.Now.AddDays(6))**。 同樣地，若要在叢集上，停用遠端桌面，您可以使用 **用戶端。DisableRdp (clustername，location)**。 如需有關這些方法的詳細資訊，請參閱 [HDInsight.NET SDK 參考](http://go.microsoft.com/fwlink/?LinkId=529017)。 這僅適用於在 Windows 上執行的 HDInsight 叢集。

**使用 RDP 連線到叢集**

1. 登入 [入口網站][azure-portal]。
2. 按一下 [ **全部瀏覽** 從左窗格中，按一下 [ **HDInsight 叢集**, ，按一下您的叢集名稱。
3. 按一下 [ **設定** 從頂端功能表中，然後按一下 [ **遠端桌面**。
4. 按一下 [ **連接** 並遵循指示。 如果 [連接] 已停用，您必須先加以啟用。 請務必使用遠端桌面使用者的使用者名稱和密碼。  不可使用叢集使用者的認證。


##開啟 Hadoop 命令列

若要使用遠端桌面連線到叢集，並使用 Hadoop 命令列，則您必須先啟用對叢集的遠端桌面存取 (如上一節所述)。

**開啟 Hadoop 命令列**

1. 使用遠端桌面連接到叢集。
8. 從桌面上，按兩下 **Hadoop 命令列**。

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令參考](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)。

在前一個螢幕擷取畫面上，資料夾名稱已內嵌 Hadoop 版本號碼。 版本號碼會根據叢集上所安裝的 Hadoop 元件版本而變更。 您可以使用 Hadoop 環境變數來參照那些資料夾。 例如：

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%
    
##後續步驟
在本文中，您已了解如何使用入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。 若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [建立 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 使用者入門](../hdinsight-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop command line"

