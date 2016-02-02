<properties
    pageTitle="安裝並使用以 Linux 為基礎之 HDInsight (Hadoop) 上的 Giraph | Microsoft Azure"
    description="在本主題中，您將學習如何使用指令碼動作在以 Linux 為基礎的 HDInsight 叢集上安裝 Giraph。透過變更叢集組態或自訂安裝服務與公用程式，指令碼動作可讓您在叢集建立期間自訂叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>


# 在 HDInsight Hadoop 叢集上安裝 Giraph，以及使用 Giraph 來處理大規模圖形

您也可以使用**指令碼動作**叢集自訂，在 Azure HDInsight 上 Hadoop 中的任何一種叢集上安裝 Giraph。 指令碼動作可讓您執行指令碼來自訂叢集，但只能在建立叢集時進行。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

在本主題中，您將學習如何使用指令碼動作來安裝 Giraph。 一旦您已安裝 Giraph，您也將了解如何將 Giraph 用於大部分的一般應用程式，也就是處理大型的圖形。
> [AZURE.NOTE] 本文的資訊是以 Linux 為基礎的 HDInsight 叢集的特定資訊。 如需使用 windows 叢集的詳細資訊，請參閱 [HDInsight Hadoop 叢集 (Windows) 上的安裝 Giraph](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>什麼是 Giraph?

[Apache Giraph](http://giraph.apache.org/) 可讓您執行圖形處理使用 Hadoop，並搭配 Azure HDInsight。 圖形可以模型化物件之間的關聯，例如大型網路 (像是網際網路) 上的路由器之間的連線，或社交網路上的人際關係 (有時稱為社交圖形)。 圖形處理可讓您分析圖形中物件之間的關聯，例如：

- 根據目前的人際關係找出可能的朋友。
- 識別網路中兩台電腦之間的最短路線。
- 計算網頁的頁面排名。

## 指令碼會執行哪些作業

此指令碼會執行下列動作：

* 安裝 Giraph 至 `/usr/hdp/current/giraph`
* 複製 `giraph examples.jar` 您叢集預設儲存體 (WASB) 檔案: `/example/jars/giraph-examples.jar`

## <a name="install"></a>安裝 Giraph 使用指令碼動作

在 HDInsight 叢集上安裝 Giraph 的範例指令碼是可以從唯讀的 Azure 儲存體 blob 位於 [https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh)。 本節提供有關如何在使用 Azure 傳統入口網站建立叢集時使用範例指令碼的指示。
> [AZURE.NOTE] 您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。 如需有關如何使用這些方法的詳細資訊，請參閱 [指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 開始使用中的步驟建立叢集 [建立 Linux 型 HDInsight 叢集](hdinsight-provision-linux-clusters.md#portal), ，但是不完成建立。

2. 在 [選用設定]**** 刀鋒視窗中，選取 [指令碼動作]****，並提供下列資訊：

    * __名稱__：輸入指令碼動作的易記名稱。
    * __指令碼 URI__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __HEAD__：勾選此選項
    * __WORKER__：勾選此選項
    * __ZOOKEEPER__：勾選此選項以在 Zookeeper 節點上安裝。
    * __參數__：將此欄位保留空白

3. 在 [指令碼動作]**** 底部，使用 [選取]**** 按鈕以儲存組態。 最後，使用 [選用組態]**** 刀鋒視窗底部的 [選取]**** 按鈕，儲存選用組態資訊。

4. 繼續建立叢集中所述 [建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="usegiraph"></a>如何在 HDInsight 中使用 Giraph?

一旦完成建立叢集之後，請使用下列步驟以執行 Giraph 隨附的 SimpleShortestPathsComputation 範例。 這會實作基本 <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 來尋找圖形中物件之間的最短路徑實作。

1. 使用 SSH 連線到 HDInsight 叢集

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [使用 SSH 與以 Linux 為基礎從 Linux、 Unix 或 OS X 在 HDInsight 上的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Windows 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-windows.md)

1. 使用以下命令建立名為 __tiny_graph.txt__ 的新檔案：

        nano tiny_graph.txt

    使用下列項目做為此檔案的內容：

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    此資料描述有向圖形，請使用格式中的物件之間的關聯性 [source\_id、 source\_value、 [[dest\_id]，[edge\_value...等]...]]。每一行代表之間的關聯性 **source\_id** 物件和一或多個 **dest\_id** 物件。  **Edge\_value** (或權數) 可以視為強度或距離之間的連線 **source_id** 和 **dest\_id**。

    如果使用值 (或權數) 當做物件之間的距離繪製出來，上述資料可能如下圖所示：

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. 若要儲存檔案，依序使用 __Ctrl+X__、__Y__，最後按 __Enter__ 以接受檔案名稱。

3. 使用下列項目以將資料儲存至您的 HDInsight 叢集的主要儲存體：

        hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

3. 使用下列項目以取得叢集前端節點的完整網域名稱 (FQDN)：

        hostname -f

4. 使用下列命令執行 SimpleShortestPathsComputation 範例。 以上一個步驟所傳回的 FQDN 取代 __HEADNODE__：

         hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=HEADNODE:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    與此命令搭配使用的參數會在下表中說明：

   | 參數| 作用|
   | --------- | ------------ |
   | `jar /usr/hdp/current/giraph/giraph-examples.jar`| 包含範例的 jar 檔案。|
   | `org.apache.giraph.GiraphRunner`| 用於啟動範例的類別。|
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation`| 將會執行的範例。在此案例中，它會計算圖形中識別碼 1 和所有其他識別碼之間的最短路徑。|
   | `ca mapred.job.tracker=HEADNODE:9010`| 叢集的前端節點。|
   | `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat`| 用於輸入資料的輸入格式。|
   | `-vip /example/data/tiny_graph.txt`| 輸入資料檔案。|
   | `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat`| 輸出格式。在此案例中，識別碼和值是純文字。|
   | `-op /example/output/shortestpaths`| 輸出位置。|
   | `-2 w`| 要使用的背景工作角色數目。在此案例中為 2。|

    如需有關這些功能和使用 Giraph 範例與其他參數的詳細資訊，請參閱 [Giraph 快速入門](http://giraph.apache.org/quick_start.html)。

5. 當工作完成之後時，將結果儲存在 __wasb: / 範例/out/shotestpaths__ 目錄。 建立的檔案會以 __part-m-__ 開頭，結束的數字表示是第一個、第二個檔案，依此類推。 使用下列項目以檢視輸出：

        hadoop fs -text /example/output/shortestpaths/*

    輸出應該如下所示：

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    SimpleShortestPathComputation 範例已刻意設計成從物件識別碼 1 開始，尋找前往其他物件的最短路徑。 因此，輸出應該會做為 `destination_id distance`, ，其中 distance 是物件識別碼 1 與目標識別碼之間經過的邊緣值 (或權數)

    顯現為圖形後，您可以走過識別碼 1 與其他所有物件之間的最短路徑來驗證結果。 請注意，識別碼 1 和識別碼 4 之間的最短路徑為 5。 這是總距離 <span style="color:orange">識別碼 1 和 3</span>, ，然後 <span style="color:red">識別碼 3 和 4</span>。

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## 後續步驟

- [色調在 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-hue-linux.md)。 色調是 Web UI，可讓您更輕鬆地建立、執行及儲存 Pig 和 Hive 工作，以及瀏覽您的 HDInsight 叢集的預設儲存體。

- [安裝和使用 Spark HDInsight 叢集上](hdinsight-hadoop-spark-install-linux.md): 指示如何使用叢集自訂，以安裝和使用 Spark HDInsight Hadoop 叢集上。 Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。

- [HDInsight 叢集上安裝的 R](hdinsight-hadoop-r-scripts-linux.md): 指示如何使用叢集自訂，以安裝和使用 R HDInsight Hadoop 叢集上。 R 是一個用於統計計算的開放原始碼語言和環境。 它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。

- [HDInsight 叢集上的安裝 Solr](hdinsight-hadoop-solr-install-linux.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。 Solr 可讓您在儲存的資料上執行功能強大的搜尋作業。





