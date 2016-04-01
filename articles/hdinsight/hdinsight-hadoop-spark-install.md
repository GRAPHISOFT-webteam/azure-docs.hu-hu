<properties
    pageTitle="使用指令碼動作在 Hadoop 叢集上安裝 Spark | Microsoft Azure"
    description="了解如何使用指令碼動作來以 Spark 自訂 HDInsight 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="nitinme"/>

# 使用指令碼動作在 HDInsight Hadoop 叢集上安裝和使用 Spark

> [AZURE.IMPORTANT] 這篇文章目前已被取代。 現在 HDInsight 為以 Windows 為基礎的叢集，提供 Spark 做為第一級叢集類型，這表示您現在可以使用指令碼動作，直接建立 Spark 叢集而不必修改 Hadoop 叢集。 使用 Spark 叢集類型時，您會取得具備 Spark 1.3.1 版的 HDInsight 3.2 版叢集。  若要安裝不同版本的 Spark，您可以使用指令碼動作。 HDInsight 提供指令碼動作指令碼的範例。

了解如何使用指令碼動作在以 Windows 為基礎的 HDInsight 上安裝 Spark，以及如何在 HDInsight 叢集上執行 Spark 查詢。


**相關文章**
- [在以 Linux 為基礎的 HDInsight 叢集上安裝 Spark](hdinsight-hadoop-spark-install-linux.md)。

- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊。

- [開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)︰ 在 Windows 作業系統上建立 spark 型別。

- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼動作的一般資訊。

- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。

## 什麼是 Spark？

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。 Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。

Spark 也可用來執行傳統的磁碟型資料處理。 Spark 以避免在中繼階段寫入磁碟的方式，改善傳統的 MapReduce 架構。 此外，Spark 與 Hadoop 分散式檔案系統 (HDFS) 和 Azure Blob 儲存體相容，因此可以輕鬆地透過 Spark 來處理現有的資料。

本主題說明如何自訂 HDInsight 叢集以安裝 Spark。

## 使用 Azure 入口網站安裝 Spark

在 HDInsight 叢集上安裝 Spark 的範例指令碼是可以從唯讀的 Azure 儲存體 blob 位於 [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1)。 根據您建立的 HDInsight 叢集版本，此指令碼可安裝 Spark 1.2.0 或 Spark 1.0.2。

- 如果您在建立時使用的指令碼 **HDInsight 3.2** 叢集中，它會安裝 **Spark 1.2.0**。
- 如果您在建立時使用的指令碼 **HDInsight 3.1** 叢集中，它會安裝 **Spark 1.0.2**。

您可以修改此指令碼或建立自有指令碼，以安裝其他版本的 Spark。

> [AZURE.NOTE] 範例指令碼只適用於 HDInsight 3.1 和 3.2 叢集。 如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

1. 開始建立叢集使用 **自訂建立** 選項述 [建立 Hadoop 叢集的 HDInsight](hdinsight-provision-clusters.md#portal)。 根據下列原則挑選叢集版本：

    - 如果您想要安裝 **Spark 1.2.0**, ，建立 HDInsight 3.2 叢集。
    - 如果您想要安裝 **Spark 1.0.2**, ，建立 HDInsight 3.1 叢集。


2. 在 **指令碼動作** 頁面的精靈中，按一下 [ **加入指令碼動作** 提供有關指令碼動作，詳細資料，如下所示 ︰

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。 例如， <b>安裝 Spark</b>。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定為了自訂叢集所叫用之指令碼的統一資源識別項 (URI)。 例如， <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定執行自訂指令碼的節點。 您可以選擇 <b>所有節點</b>, ，<b>僅限前端節點</b>, ，或 <b>僅限背景工作節點</b>。
        <tr><td>參數</td>
            <td>如果指令碼要求，請指定參數。 用來安裝 Spark 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
    </table>

    您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。 加入指令碼之後，請按一下核取記號以開始建立叢集。

您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上使用指令碼安裝 Spark。 本主題稍後會提供這些程序的指示。

## 在 HDInsight 中使用 Spark
Spark 提供以 Scala、Python 及 Java 撰寫的 API。 您也可以使用互動式 Spark 殼層來執行 Spark 查詢。 本節說明如何透過不同方法來使用 Spark：

- [使用 Spark 殼層來執行互動式查詢](#sparkshell)
- [使用 Spark 殼層來執行 Spark SQL 查詢](#sparksql)
- [使用獨立 Scala 程式](#standalone)

###<a name="sparkshell"></a>使用 Spark 殼層來執行互動式查詢
請執行下列步驟以從互動式 Spark 殼層執行 Spark 查詢。 在本節中，我們將對 HDInsight 叢集上預設提供的範例資料檔案 (/example/data/gutenberg/davinci.txt) 執行 Spark 查詢。

1. 從 Azure 入口網站，針對您所建立且已安裝 Spark 的叢集啟用遠端桌面，然後遠端登入到叢集。 如需指示，請參閱[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)。

2. 在遠端桌面通訊協定 (RDP) 工作階段中，從桌面開啟 Hadoop 命令列 （從桌面捷徑），並瀏覽至 Spark 的安裝; 位置例如， **C:\apps\dist\spark-1.2.0**。


3. 執行下列命令以啟動 Spark 殼層：

         .\bin\spark-shell --master yarn

    在命令完成執行之後，您應該會看到 Scala 提示：

         scala>

5. 出現 Scala 提示時，輸入下方所示的 Spark 查詢。 此查詢會計算 davinci.txt 檔案中每個單字的出現次數，該檔案位於與叢集關聯之 Azure Blob 儲存體上的 /example/data/gutenberg/ 位置中。

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. 輸出應該如以下所示：

    ![在 HDInsight 叢集中執行 Scala 互動式殼層所得到的輸出](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. 輸入 :q 以結束 Scala 提示。

        :q

###<a name="sparksql"></a>使用 Spark 殼層來執行 Spark SQL 查詢

Spark SQL 可讓您使用 Spark 來執行以結構化查詢語言 (SQL)、HiveQL 或 Scala 表示的關聯式查詢。 在本節中，我們要來看看如何使用 Spark 對範例 Hive 資料表執行 Hive 查詢。 使用本節中的 Hive 資料表 (稱為 **hivesampletable**) 時，預設可以在建立叢集。

>[AZURE.NOTE] 下列範例針對建立 **Spark 1.2.0**, ，如果您執行指令碼動作在建立 HDInsight 3.2 叢集時一併安裝。

1. 從 Azure 入口網站，針對您所建立且已安裝 Spark 的叢集啟用遠端桌面，然後遠端登入到叢集。 如需指示，請參閱[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)。

2. 在 RDP 工作階段，從桌面開啟 Hadoop 命令列 （從桌面捷徑），並瀏覽至 Spark 的安裝; 位置例如， **C:\apps\dist\spark-1.2.0**。


3. 執行下列命令以啟動 Spark 殼層：

         .\bin\spark-shell --master yarn

    在命令完成執行之後，您應該會看到 Scala 提示：

         scala>

4. 在 Scala 提示設定 Hive 內容。 必須執行此動作，才能透過 Spark 使用 Hive 查詢。

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    請注意， **sc** 是當您啟動 Spark 殼層時設定的預設 Spark 內容。

5. 使用 Hive 內容執行 Hive 查詢，並將輸出列印到主控台。 查詢會擷取特定型號裝置的資料，並將所擷取的記錄數限制在 20 個內。

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 您應該會看到如下的輸出：

    ![在 HDInsight 叢集中執行 Spark SQL 所得到的輸出](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. 輸入 :q 以結束 Scala 提示。

        :q

### <a name="standalone"></a>使用獨立 Scala 程式

在本節中，我們將撰寫一個 Scala 應用程式，用來計算 HDInsight 叢集上預設提供的範例資料檔案 (/example/data/gutenberg/davinci.txt) 中含有字母 'a' 和 'b' 的行數。 若要撰寫獨立 Scala 程式並與已安裝 Spark 的自訂叢集搭配使用，您必須執行下列步驟：

- 撰寫 Scala 程式
- 建置 Scala 程式以取得 .jar 檔案
- 在叢集上執行工作

#### 撰寫 Scala 程式
在本節中，您將撰寫一個 Scala 應用程式，用來計算範例資料檔案中含有 'a' 和 'b' 的行數。

1. 開啟文字編輯器，並貼上下列程式碼：


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. 儲存檔案名稱 **SimpleApp.scala**。

#### 建置 Scala 程式
在本節中，您使用 <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">簡單建置工具</a> （或稱 sbt） 來建置 Scala 程式。 sbt 需要 Java 1.6 或更新版本，因此請確定使用正確的安裝，才能繼續進行本節的 Java 版本。

1. 從 http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html 安裝 sbt。
2. 建立一個稱為 **SimpleScalaApp**, ，在此資料夾中，建立名為的檔案和 **simple.sbt**。 這是包含 Scala 版本、程式庫相依性等相關資訊的組態檔。將下列程式碼貼到 simple.sbt 檔案中並加以儲存：


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] Make sure you retain the empty lines in the file.


3. 在 **SimpleScalaApp** 資料夾中，建立目錄結構 **\src\main\scala** 並貼上的 Scala 程式 (**SimpleApp.scala**) 您稍早建立的 \src\main\scala 資料夾下。
4. 開啟命令提示字元，瀏覽至 SimpleScalaApp 目錄，並輸入下列命令：


        sbt package


    Once the application is compiled, you will see a **simpleapp_2.10-1.0.jar** file created under the **\target\scala-2.10** directory within the root SimpleScalaApp folder.


#### 在叢集上執行工作
在本節中，您將從遠端連線至已安裝 Spark 的叢集，然後複製 SimpleScalaApp 專案的目標資料夾。 然後使用 **spark 提交** 命令，以在叢集上提交工作。

1. 從遠端連線至已安裝 Spark 的叢集。 從您撰寫並建置 SimpleApp.scala 程式的電腦複製 **SimpleScalaApp\target** 資料夾將它貼到叢集上的位置。
2. 在 RDP 工作階段，從桌面開啟 Hadoop 命令列中，並瀏覽至您貼上的位置 **目標** 資料夾。
3. 輸入下列命令來執行 SimpleApp.scala 程式：


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 當程式執行完成時，輸出會顯示在主控台上。


        Lines with a: 21374, Lines with b: 11430

## 使用 Azure PowerShell 安裝 Spark

在本節中，我們使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-azurehdinsightscriptaction</a>** 指令程式可使用指令碼動作以自訂叢集所叫用指令碼。 在繼續之前，請確認您已安裝和設定 Azure PowerShell。 如需設定工作站以執行 HDInsight PowerShell cmdlet 之資訊，請參閱 [安裝和設定 Azure PowerShell][powershell-install-configure]。

執行下列步驟：

1. 開啟 Azure PowerShell 視窗，並宣告下列變數：

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. 指定設定值，例如要使用的叢集中節點和預設儲存體。

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. 使用 **Add-azurehdinsightscriptaction** 指令程式可將指令碼動作加入叢集組態。 稍後在建立叢集時，將會執行指令碼動作。

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    **Add-azurehdinsightscriptaction** cmdlet 可接受下列參數 ︰

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">參數</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">設定</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">要在其中新增指令碼動作資訊的設定物件。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行自訂指定碼的節點。 有效值為 HeadNode (在前端節點上安裝) 或 DataNode (在所有資料節點上安裝)。 您可以使用其中一個或兩個值。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定所執行之指令碼的 URI。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼所需的參數。 本主題中使用的範例指令碼不需要任何參數，因此您在上述程式碼片段中看不到此參數。
    </td></tr>
    </table>

4. 最後，開始建立已安裝 Spark 的自訂叢集。  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

出現提示時，請輸入叢集的認證。 建立叢集可能需要幾分鐘的時間。

## 使用 PowerShell 安裝 Spark

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)。

## 使用 .NET SDK 安裝 Spark

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)。


## 另請參閱

- [在以 Linux 為基礎的 HDInsight 叢集上安裝 Spark](hdinsight-hadoop-spark-install-linux.md)︰ 安裝 Linux 上的 Spark 基礎的 HDInsight 叢集。
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集。
- [開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)︰ 開始使用 HDInsight 上的 Spark。
- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]︰ 來自訂 HDInsight 叢集使用指令碼動作。
- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)︰ 開發指令碼動作指令碼。
- [HDInsight 叢集上安裝 R][hdinsight-install-r] 提供如何使用叢集自訂，以安裝和使用 R HDInsight Hadoop 叢集上的指示。 R 是一個用於統計計算的開放原始碼語言和環境。 它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。
- [HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。 Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。
- [HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。 Solr 可讓您對儲存的資料執行功能強大的搜尋作業。

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md


