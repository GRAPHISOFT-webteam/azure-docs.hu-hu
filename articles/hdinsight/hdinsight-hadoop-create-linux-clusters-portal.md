<properties
    pageTitle="使用入口網站在 HDInsight 中的 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
    description="了解如何在適用於 HDInsight 的 Linux 上，使用 Web 瀏覽器和 Azure 入口網站，來建立 Hadoop、HBase 或 Storm 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="11/16/2015"
    ms.author="nitinme"/>


#使用 Azure 入口網站建立 HDInsight 上的 Linux 型叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 利用本文件中的資訊，使用入口網站和您的 Web 瀏覽器來建立 Linux 架構的新 HDInsight 叢集

##必要條件

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __現代網頁瀏覽器__。 Azure 入口網站會使用 HTML5 和 Javascript，而且可能無法在舊版 Web 瀏覽器中正確運作。

##建立叢集

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [ **新增**, ，按一下 [ **資料分析**, ，然後按一下 [ **HDInsight**。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")

3. 輸入 **叢集名稱**, ，請選取 **Hadoop** 的 **叢集類型**, ，以及從 **叢集作業系統** 下拉式清單中，選取 **Ubuntu**。 如果該叢集可用，其名稱旁會出現綠色核取記號。

    > [AZURE.NOTE] 若要佈建 HBase 或 Storm 叢集，請選取適當的值 **叢集類型** 下拉式清單。

    ![輸入叢集名稱和類型](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Enter cluster name and type")

4. 如果您有一個以上的訂閱，請按一下 [ **訂閱** 選取將用於叢集的 Azure 訂閱的項目。

5. 按一下 [ **資源群組** ，查看現有的資源群組的清單，然後選取 [建立叢集的一個。 或者，您可以按一下 **新建** ，然後輸入新的資源群組的名稱。 出現綠色核取記號即表示新群組的名稱可用。

    > [AZURE.NOTE] 如果有的話，這個項目將會預設為其中一個現有的資源群組。

6. 按一下 [ **認證** ，然後輸入 [系統管理使用者的密碼。 您也必須輸入 **SSH 使用者名稱** 和 **密碼** 或 **公開金鑰**, ，這將用來驗證 SSH 使用者。 建議使用公開金鑰的方法。 按一下 [ **選取** 底部要儲存認證的設定。

    ![提供叢集認證](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Provide cluster credentials")

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)


7. 按一下 [ **資料來源** 選擇現有的資料來源的叢集，或建立一個新。

    ![資料來源刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Provide data source configuration")

    目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 **資料來源** 刀鋒視窗。

    - **選取方法**︰ 設為 **從所有的訂閱** 才能瀏覽儲存體帳戶從您的訂閱。 請設為 **便捷鍵** 如果您想要輸入 **儲存體名稱** 和 **便捷鍵** 現有的儲存體帳戶。

    - **選取儲存體帳戶建立新的 /**︰ 按一下 **選取儲存體帳戶** 瀏覽並選取您要與叢集相關聯的現有儲存體帳戶。 或者，按一下 [ **新建** 來建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可用，將會出現綠色核取記號。

    - **選擇預設容器**︰ 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

    - **位置**︰ 儲存體帳戶，或將中建立的地理區域。

        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。

    按一下 [ **選取** 儲存資料來源設定。

8. 按一下 [ **節點定價層** 顯示將建立此叢集節點的相關資訊。 設定該叢集所需的背景工作角色節點數目。 該叢集的預估成本將會顯示在此刀鋒視窗內。

    ![節點定價層刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Specify number of cluster nodes")
    
    > [AZURE.IMPORTANT] 如果您打算在 32 個以上的背景工作節點，在建立叢集，或藉由調整叢集在建立之後，您必須選取至少 8 個核心和 14 GB ram 的前端節點大小。
    >
    > 如需有關在節點大小和相關聯的成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

    按一下 [ **選取** 儲存定價組態的節點。

9. 按一下 [ **選擇性組態** 選取叢集的版本，以及設定其他選擇性設定，例如聯結 **虛擬網路**, 、 設定 **外部中繼存放區** 若要保留 Hive 和 Oozie 的資料，使用指令碼動作自訂叢集來安裝自訂元件，或使用其他儲存體帳戶與叢集。

    * 按一下 [ **HDInsight 版本** 下拉式清單並選取您想要在叢集中使用的版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

    * **虛擬網路**︰ 選取 Azure 虛擬網路和子網路，如果您想要放置到虛擬網路的叢集。  

        ![虛擬網路刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Specify virtual network details")

        >[AZURE.NOTE] Windows 型的 HDInsight 叢集只能放到傳統的虛擬網路。

    * 按一下 [ **外部中繼存放區** 來指定您想要用來儲存與叢集相關聯的 Hive 和 Oozie 中繼資料的 SQL 資料庫。
    
        > [AZURE.NOTE] 中繼存放區組態不適用於 HBase 叢集類型。

        ![自訂中繼存放區刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Specify external metastores")

        如 **hive 使用現有的 SQL DB** 中繼資料中，按一下 **是**, 選取 SQL 資料庫，然後提供資料庫的使用者名稱/密碼。 如果您想要重複這些步驟 **針對 Oozie 中繼資料使用現有的 SQL DB**。 按一下 [ **選取** 直到您會回到 **選擇性組態** 刀鋒視窗。

        >[AZURE.NOTE] 用於 metastore 的 Azure SQL database 必須能夠連線至其他 Azure 服務，包括 Azure HDInsight。 在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。 這是指執行 SQL Database 執行個體的伺服器。 一旦進入伺服器檢視，按一下 [ **設定**, ，然後 **Azure 服務**, ，按一下 [ **是**, ，然後按一下 [ **儲存**。

    * **編寫指令碼動作** 如果您想要使用自訂指令碼以自訂叢集，與叢集正在建立。 如需指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。 請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

        ![指令碼動作刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Specify script action")

    * 按一下 [ **Azure 儲存體金鑰** 來指定與叢集相關聯的其他儲存體帳戶。 在 **Azure 儲存體金鑰** 刀鋒視窗中，按一下 [ **新增儲存體金鑰**, ，然後選取現有的儲存體帳戶，或建立新的帳戶。

        ![其他儲存體刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Specify additional storage accounts")

        按一下 [ **選取** 直到您會回到 **新的 HDInsight 叢集** 刀鋒視窗。

10. 在 **新的 HDInsight 叢集** 刀鋒視窗中，確定 **釘選到開始面板** 已選取，然後按一下 [ **建立**。 這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。 該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

  	| 佈建期間 | 佈建完成 |
  	| ------------------ | --------------------- |
  	| ![「開始面板」上的佈建指示器](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

    > [AZURE.NOTE] 需要花一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 **通知** 佈建程序檢查頁面左邊的項目。

11. 佈建完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。 此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

    ![叢集刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Cluster properties")

    使用下列命令以了解在此刀鋒視窗，並在最上方圖示 **Essentials** 區段 ︰

    * **設定** 和 **所有設定**︰ 顯示 **設定** 叢集中，可讓您存取叢集的詳細的組態資訊的分頁。

    * **儀表板**, ，**叢集儀表板**, ，和 **URL**︰ 這些是所有的方式來存取叢集儀表板，這是入口網站，以在叢集上執行工作。

    * **安全殼層**︰ 存取使用 SSH 的叢集所需的資訊。

    * **刪除**︰ 刪除 HDInsight 叢集。

    * **快速入門** (![定域機組和雷電圖示 = 快速入門](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): 顯示資訊可協助您開始使用 HDInsight。

    * **使用者** (![使用者圖示](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): 可讓您設定的權限 _入口網站的管理_ 的其他使用者此叢集上您的 Azure 訂閱。

        > [AZURE.IMPORTANT] 這 _只_ 影響存取，以及在 Azure 入口網站中，此叢集的權限，以及誰可以連線到，或將工作提交至 HDInsight 叢集上沒有作用。

    * **標記** (![標籤圖示](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): 標記可讓您設定來定義自訂分類您的雲端服務的索引鍵/值組。 例如，您可以建立名為 __專案__, ，然後使用共通的值與特定專案相關聯的所有服務。

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

###Hadoop 叢集

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [將 HDInsight 與 MapReduce 搭配使用](hdinsight-use-mapreduce.md)

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-stared-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

