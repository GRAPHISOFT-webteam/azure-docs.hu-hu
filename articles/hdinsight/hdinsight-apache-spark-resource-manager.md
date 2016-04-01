<properties 
    pageTitle="在 HDInsight 中使用資源管理員將資源配置給 Apache Spark 叢集 | Microsoft Azure" 
    description="了解如何在 HDInsight 上使用資源管理員，以便提升 Spark 叢集的效能。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="paulettm" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="nitinme"/>


# 在 Azure HDInsight 中管理 Apache Spark 叢集的資源

資源管理員是 Spark 叢集儀表板的元件，它能讓您管理諸如在叢集上執行之每個應用程式所使用的核心和 RAM 等資源。

## <a name="launchrm"></a>如何啟動資源管理員？

1. 從 [Azure 入口網站](https://ms.portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 （如果固定至開始面板）。 您也可以導覽至您的叢集下 **全部瀏覽** > **HDInsight 叢集**。 
 
2. 從 Spark 叢集刀鋒視窗中，按一下 [ **儀表板**。 出現提示時，輸入 Spark 叢集的系統管理員認證。

    ![啟動資源管理員](./media/hdinsight-apache-spark-resource-manager/HDI.Cluster.Launch.Dashboard.png "Start Resource Manager")   

##<a name="scenariosrm"></a>如何使用資源管理員修正這些問題？

以下是一些可能會發生在 Spark 叢集上的常見案例，以及如何使用資源管理員因應的指示。

### HDInsight 上的 Spark 叢集速度很慢

HDInsight 中的 Apache Spark 叢集是專為多租用戶所設計，因此資源會分割成多個元件 (Notebook、作業伺服器等)。 這可讓您同時使用所有 Spark 元件，而不必擔心有元件會無法取得執行所需的資源，但由於資源已分散，因此每個元件都會變慢。  您可以根據需求進行調整。 


### 我只利用 Spark 叢集使用 Jupyter Notebook。 該如何將所有資源配置給它？

1. 從 **Spark 儀表板**, ，按一下 [ **Spark UI** 索引標籤，以找出核心和 RAM 上限可配置給應用程式的數目上限。

    ![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "Find resources allocated to a Spark cluster")

    依照上述螢幕擷取畫面，您可以配置的核心數目上限為 7 個 (總共 8 個，其中有 1 正在使用中)，可配置的 RAM 上限為 9 GB (總共 12 GB RAM，其中 2 GB 必須保留供系統使用，有 1 GB 正供給其他應用程式使用)。

    您也應該將所有執行中的應用程式納入考量。 您可以查看執行的應用程式從 **Spark UI** ] 索引標籤。

    ![執行中的應用程式](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "Applications running on the cluster")

    
2. 從 HDInsight Spark 儀表板中，按一下 [ **資源管理員** ] 索引標籤上，指定的值 **預設應用程式核心計數** 和 **預設每個背景工作節點的執行程式記憶體**。 將其他屬性設定為 0。

    ![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "Allocate resources to your applications")

### 我沒有搭配使用 BI 工具和 Spark 叢集。 該如何回收資源？ 

將 Thrift 伺服器核心計數和 Thrift 伺服器執行程式記憶體指定為 0。 沒有核心或配置記憶體，thrift 伺服器將會進入 **等候** 狀態。

![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "No resources to the thrift server")

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [在 HDInsight 叢集上佈建 Spark](hdinsight-apache-spark-provision-clusters.md)
* [執行 HDInsight 中使用 Spark 與 BI 工具的互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 


