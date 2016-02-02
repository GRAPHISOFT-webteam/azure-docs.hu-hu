<properties 
    pageTitle="在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark | Microsoft Azure" 
    description="說明如何搭配使用 Notebook 和 Apache Spark 以根據原始資料建立結構描述、將它們儲存成 Hive 資料表，然後針對 Hive 資料表使用 BI 工具來分析資料等作業的逐步指示。" 
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
    ms.date="09/30/2015" 
    ms.author="nitinme"/>



# 在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark

了解如何在 Azure HDInsight 中使用 Apache Spark 來執行下列作業：

* 取得原始範例資料並儲存成 Hive 資料表
* 使用 Power BI 和 Tableau 之類的 BI 工具來分析及視覺化資料。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。 如需指示，請參閱 [佈建 Apache Spark 叢集的 Azure HDInsight](hdinsight-apache-spark-provision-clusters.md)。
- 一台已安裝 Microsoft Spark ODBC 驅動程式的電腦 (HDInsight 上的 Spark 需要此電腦才能使用 Tableau)。 您可以安裝驅動程式從 [這裡](http://go.microsoft.com/fwlink/?LinkId=616229)。
- BI 工具，例如 [Power BI](http://www.powerbi.com/) 或 [Tableau Desktop](http://www.tableau.com/products/desktop)。 您可以取得免費預覽版本的訂閱從 Power BI [http://www.powerbi.com/](http://www.powerbi.com/)。

## <a name="hivetable"></a>將原始資料儲存成 Hive 資料表

在本節中，我們使用 [Jupyter](https://jupyter.org) HDInsight 來執行作業來處理原始範例資料，並將它儲存成 Hive 資料表中的 Apache Spark 叢集相關聯的筆記本。 範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。

一旦將資料儲存成 Hive 資料表之後，下一節我們將使用 Power BI 和 Tableau 等 BI 工具連接 Hive 資料表。

1. 從 [Azure 入口網站](https://portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 (如果固定至開始面板)。 您也可以按一下 [瀏覽全部]**** > [HDInsight 叢集]****，瀏覽至您的叢集。

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ****，然後在 [叢集儀表板]**** 刀鋒視窗中按一下 [Jupyter Notebook]****。 出現提示時，輸入叢集的系統管理員認證。
    > [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。 使用您叢集的名稱取代 __CLUSTERNAME__：
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。 按一下 [新增]****，然後按一下 [Python 2]****。

    ![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "Create a new Jupyter notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。 在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

    ![提供 Notebook 的名稱](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "Provide a name for the notebook")

4. 匯入所需的模組，然後建立 Spark 和 Hive 內容。 將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

     from pyspark import SparkContext
     from pyspark.sql import *
     from pyspark.sql import HiveContext
    
     # Create Spark and Hive contexts
     sc = SparkContext('spark://headnodehost:7077', 'pyspark')
     hiveCtx = HiveContext(sc)

 每當您在 Jupyter 中執行工作時，網頁瀏覽器視窗標題會顯示 Notebook 的標題和 **(忙碌)** 狀態。 您也會在右上角的 **Python 2** 文字旁看到實心圓。 工作完成後，實心圓將會變成空心圓。

  ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "Status of a Jupyter notebook job")

4. 將範例資料載入暫存資料表。 當您在 HDInsight 中佈建 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶，其路徑為 **\HdiSamples\SensorSampleData\hvac**。

 將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。 此程式碼片段會將資料註冊到名為 **hvac** 的 Hive 資料表中。

     # Create an RDD from sample data
     hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
     # Parse the data and create a schema
     hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
     hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
    
     # Infer the schema and create a table       
     hvacTable = hiveCtx.inferSchema(hvac)
     hvacTable.registerAsTable("hvactemptable")
     hvacTable.saveAsTable("hvac")

5. 確認資料表已成功建立。 將以下程式碼片段貼入 Notebook 中的空白儲存格，然後按下 **SHIFT + ENTER**。

        hiveCtx.sql("SHOW TABLES").show()

    您應該會看到如下的輸出：

        tableName       isTemporary
        hvactemptable   true       
        hivesampletable false      
        hvac            false

    只有 **isTemporary** 資料行為 false 的資料表，為將會儲存在中繼存放區，且可以從 BI 工具存取的 Hive 資料表。 在此教學課程中，我們會連線到剛剛建立的 **hvac** 資料表。

6. 請確認資料表包含預期的資料。 將下列程式碼片段貼到 Notebook 的空白儲存格中，然後按下 **SHIFT + ENTER**。

        hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()

7. 您現在可以重新啟動核心以結束 Notebook。 按一下頂端功能表列中的 [核心]****，按一下 [重新啟動]****，然後在提示出現時再按一下 [重新啟動]****。

    ![重新啟動 Jupyter 核心](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "Restart the Jupyter Kernel")

## <a name="powerbi"></a>使用 Power BI 分析 Hive 資料表中的資料

將資料儲存成 Hive 資料表後，您可以使用 Power BI 來連接資料並以視覺化方式呈現，以便建立報表、儀表板等。

1. Sign in to [Power BI](http://www.powerbi.com/).

2. 在 [歡迎使用] 畫面中，按一下 [資料庫和其他資訊]****。

    ![將資料送入 Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "Get data into Power BI")

3. 在下一個畫面中，按一下 [Spark]****，然後按一下 [連線]****。

4. 在 Azure HDInsight 上的 Spark 頁面中，提供連線您 Spark 叢集所需的值，然後按一下 [連線]****。

    ![連接 HDInsight 上的 Spark 叢集](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "Connect to a Spark cluster on HDInsight")

    建立連線之後，Power BI 會開始從 Spark 叢集將資料匯入 HDInsight。。

5. Power BI 會匯入資料並顯示新的儀表板。 [資料集]**** 標頭下方也會出現新的資料集。 按一下儀表板中的 Spark 磚，開啟視覺化資料所需的工作表。

    ![Power BI 儀表板上的 Spark 磚](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Spark tile on Power BI dashboard")

6. 請注意，右側的 [欄位]**** 清單會列出您先前建立的 **hvac** 資料表。 展開資料表，查看資料表中的欄位是否與稍早在 Notebook 中定義的欄位相同。

      ![列出 Hive 資料表](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "List Hive tables")

7. 建置視覺效果，顯示每棟建築物之目標溫度和實際溫度間的差異。 若要這樣做，拖放 **BuildingID** 欄位底下 **軸**, ，和 **ActualTemp**/**TargetTemp** 欄位底下 **值**。

    ![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "Create visualizations")

    此外，請選取**區域對應圖**圖示 (在紅色方框中) 來將資料視覺化。

8. 根據預設，視覺效果會顯示 **ActualTemp** 和 **TargetTemp** 的總和。 對於這兩個欄位，請選取下拉式清單中的 [平均]****，來取得這兩棟建築物的實際和目標溫度的平均值。

    ![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "Create visualizations")

9. 資料的視覺效果應該與以下範例相似。 在視覺效果上移動游標可取得相關資料的工具提示。

    ![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "Create visualizations")

10. 按一下頂端功能表的 [儲存]****，並提供報告名稱。 您也可以釘選視覺效果。 釘選視覺效果時，系統會將它儲存在儀表板上，以便您追蹤最的新值。

    您可以針對同一個資料集加入滿足需求數量的視覺效果，並將它們釘選在儀表板上以取得資料快照。 此外，HDInsight 上的 Spark 叢集已透過直接連線連接 Power BI。 這表示 Power BI 隨時能取得叢集的最新資訊，因此您不需要排程資料集重新整理。

## <a name="tableau"></a>使用 Tableau Desktop 分析 Hive 資料表中的資料

1. 啟動 Tableau Desktop。 在左窗格上可連線的伺服器清單中，按一下 [Spark SQL]****。

2. 在 Spark SQL 連線對話方塊中，提供下圖所示的值，然後按一下 [確定]****。

    ![連接 Spark 叢集](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "Connect to a Spark cluster")

    [驗證] 下拉式清單 **Windows** **Azure HDInsight 服務** 選項，只有當您安裝了 [Microsoft Spark ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229) 電腦上。

3. 在下一個畫面中，按一下 [結構描述]**** 下拉式清單的尋找****圖示，然後按一下 [預設]****。

    ![尋找結構描述](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "Find schema")

4. 對於 [資料表]**** 欄位，再次按一下尋找****圖示以列出叢集中所有可用的 Hive 資料表。 您應該會看到自己先前使用 Notebook 建立的 **hvac** 資料表。

    ![尋找資料表](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "Find tables")

5. 將資料表拖放到右側頂端的方塊。 Tableau 會匯入資料，並以紅色方塊反白顯示結構描述。

    ![將資料表加入 Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "Add tables to Tableau")

6. 按一下左下方的 [Sheet1]**** 索引標籤。 針對每個日期，製作出顯示所有建築物之平均目標溫度和實際溫度的視覺效果。 拖放到 **日期** 和 **建置識別碼** 至 **資料行** 和 **實際溫度**/**目標 Temp** 至 **列**。 請選取 [標記]**** 下方的 [區域]****，以使用區域對應圖的視覺效果。

     ![新增視覺效果的欄位](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "Add fields for visualization")

7. 根據預設，溫度欄位以彙總形式顯示。 如果您想要改為顯示平均溫度，可以從下拉式清單執行操作，如下所示。

    ![採取平均溫度](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "Take average of temperature")

8. 您也可以將溫度對應加諸在其他對應之上，凸顯目標溫度和實際溫度之間的差異。 將滑鼠移動到下方區域對應的角落，直到控點圖形上方出現反白顯示的紅色圓圈為止。 將對應拖曳到上方的其他對應上，當您看到圖形出現反白顯示的紅色矩形時放開滑鼠。

    ![合併對應](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "Merge maps")

     資料視覺效果應該出現如下所示的變更：

    ![視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "Visualization")

9. 按一下 [儲存]**** 以儲存工作表。 您可以建立儀表板，並在儀表板中加入一個或多個工作表。

## <a name="seealso"></a>另請參閱

* [概觀: Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [快速入門: 佈建 Apache Spark HDInsight 和使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [管理 Azure HDInsight 中的 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)



[hdinsight-versions]: ../hdinsight-component-versioning/ 
[hdinsight-upload-data]: ../hdinsight-upload-data/ 
[hdinsight-storage]: ../hdinsight-use-blob-storage/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[azure-management-portal]: https://manage.windowsazure.com/ 
[azure-create-storageaccount]: ../storage-create-storage-account/ 

