<properties 
    pageTitle="在 HDInsight 中搭配使用事件中樞和 Apache Spark 來處理串流資料 | Microsoft Azure" 
    description="說明如何將資料串流傳送到 Azure 事件中樞，接著再使用 Zeppelin Notebook 於 Spark 中接收這些事件的逐步指示" 
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


# Spark Streaming：在 HDInsight 上使用 Apache Spark 處理來自 Azure 事件中樞的事件

Spark Streaming 能擴充核心的 Spark API，建置可調整、高輸送量、容錯的串流處理應用程式。 資料能擷取自許多來源。 在本文章中，我們使用事件中樞來擷取資料。 事件中樞是可高度調整的擷取系統，每秒可以吸收數以百萬計的事件。 

在本教學課程中，您將學習如何建立 Azure 事件中樞、使用以 C# 撰寫的主控台應用程式將訊息擷取到事件中樞，以及使用針對 HDInsight 中 Apache Spark 設定的 Zeppelin Notebook 平行擷取它們。

> [AZURE.NOTE] 若要依照這篇文章中的指示，您必須使用兩個版本的 Azure 入口網站。 若要建立事件中心，您將使用 [Azure 入口網站](https://manage.windowsazure.com)。 若要使用 HDInsight Spark 叢集，您將使用 [Azure 預覽入口網站](https://ms.portal.azure.com/)。  

**必要條件：**

您必須滿足以下條件：

- Azure 訂閱。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。 如需指示，請參閱 [佈建 Apache Spark 叢集的 Azure HDInsight](hdinsight-apache-spark-provision-clusters.md)。
-  [Azure 事件中樞](service-bus-event-hubs-csharp-ephcs-getstarted.md)。
- 安裝 Microsoft Visual Studio 2013 的工作站。 如需指示，請參閱 [安裝 Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx)。

##<a name="createeventhub"></a>建立 Azure 事件中樞

1. 從 [Azure 入口網站](https://manage.windowsazure.com), ，請選取 **新增** > **服務匯流排** > **事件中心** > **自訂建立**。

2. 上 **新增新的事件中心** 畫面上，輸入 **事件中樞名稱**, ，請選取 **區域** 建立中樞，並建立新的命名空間或選取現有的 gpo。 按一下 [ **箭號** 以繼續。

    ![精靈頁面 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Create an Azure Event Hub")

    > [AZURE.NOTE] 您應該選取相同 **位置** 與您的 Apache Spark 叢集，以便降低延遲和成本的 HDInsight 中。

3. 在 **設定事件中樞** 畫面上，輸入 **分割計數** 和 **訊息保留** 值，然後再按一下核取記號。 在此範例中，資料分割計數使用 10，訊息保留使用 1。 請記下資料分割計數，因為您稍後會用到這個值。

    ![精靈頁面 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Specify partition size and retention days for Event Hub")

4. 按一下您所建立的事件中心，請按一下 **設定**, ，然後建立事件中心的兩個存取原則。

    <table>
    <tr><th>名稱</th><th>權限</th></tr>
    <tr><td>mysendpolicy</td><td>傳送</td></tr>
    <tr><td>myreceivepolicy</td><td>接聽</td></tr>
    </table>

    建立權限之後，請選取 **儲存** 在頁面底部的圖示。 這會建立共用的存取原則，將用來傳送 (**mysendpolicy**) 和接聽 (**myreceivepolicy**) 此事件中樞。

    ![原則](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Create Event Hub policies")

    
5. 在相同頁面上，記下針對這兩個原則產生的原則金鑰。 請儲存這些金鑰，因為稍後會用到。

    ![原則金鑰](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Save policy keys")

6. 在 **儀表板** 頁面上，按一下 **連接資訊** 從上至下擷取及儲存連接字串使用兩個原則的事件中心。

    ![原則金鑰](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Save policy connection strings")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>使用 Zeppelin 在 HDInsight 上的 Spark 中接收訊息

在本節中，您會建立 [Zeppelin](https://zeppelin.incubator.apache.org) notebook 以接收來自事件中樞的訊息到 HDInsight 中的 Spark 叢集。

### 將資源配置給 Zeppelin 以供串流處理應用程式使用

當您使用 Zeppelin 來建立串流處理應用程式時，必須考量下列事項：

* **事件中樞資料分割和核心配置給 Zeppelin**。 在先前步驟中，您建立了含有一些資料分割的事件中樞。 在您於下列內容建立的 Zeppelin 串流處理應用程式中，您將再次指定相同數目的資料分割。 若要使用 Zeppelin 成功串流處理來自事件中樞的資料，您配置給 Zeppelin 的核心數目必須是事件中樞上資料分割數目的兩倍。
* **最小數目的核心配置給 Zeppelin**。 在您於下列內容建立的 Zeppelin 串流處理應用程式中，您會建立一個暫存資料表來儲存應用程式進行串流處理時的訊息。 接著，您會使用 Spark SQL 陳述式，來讀取此暫存資料表中的訊息。 若要成功執行 Spark SQL 陳述式，您必須確定至少已配置兩個核心給 Zeppelin。

如果您結合了上述兩項需求，即會得到下列結果：

* 您必須配置給 Zeppelin 的核心數目下限為 2。
* 配置的核心數目永遠都必須是事件中樞上資料分割數目的兩倍。 

如需有關如何在 Spark 叢集的資源配置的指示，請參閱 [管理 HDInsight 中的 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)。

### 使用 Zeppelin 建立串流處理應用程式

1. 從 [Azure 入口網站](https://portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 （如果固定至開始面板）。 您也可以導覽至您的叢集下 **全部瀏覽** > **HDInsight 叢集**。   

2. 從 Spark 叢集刀鋒視窗中，按一下 [ **快速連結**, ，然後從 **叢集儀表板** 刀鋒視窗中，按一下 [ **Zeppelin Notebook**。 出現提示時，輸入叢集的系統管理員認證。

    > [AZURE.NOTE] 您也可能在您的瀏覽器中開啟下列 URL，為您的叢集到達 Zeppelin Notebook。 取代 __CLUSTERNAME__ 與您的叢集名稱 ︰
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭窗格中，按一下 **筆記本**, ，然後從下拉式清單中，按一下 [ **建立新 Note**。

    ![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Create a new Zeppelin notebook")

    在相同頁面上，在 **筆記本** 標題時，您應該會看到名稱開頭為新筆記型電腦 **Note XXXXXXXXX**。 按一下新的 Notebook。

3. 在新 Notebook 的網頁上按一下標題，需要的話可以變更 Notebook 的名稱。 按下 ENTER 以儲存名稱變更。 此外，請確定 notebook 標頭會顯示 **連線** 右上角中的狀態。

    ![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Zeppelin notebook status")

4. 將以下程式碼片段貼入新 Notebook 中預設建立的空白段落中，並取代預留位置以使用您的事件中樞組態。 在此片段中，從事件中心接收資料流，並將串流註冊到暫存資料表時，呼叫 **mytemptable**。 在下一節中，我們會啟動傳送者應用程式。 接著，您可以直接從資料表讀取資料。

    > [AZURE.NOTE] 在下列程式碼片段 **eventhubs.checkpoint.dir** 必須設為目錄，預設儲存容器中。 如果目錄不存在，串流處理應用程式即會建立它。 您可以指定類似目錄的完整路徑"**wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**」 只是目錄的相對路徑或例如"**/mycheckpointdir**」。

        import org.apache.spark.streaming.{Seconds, StreamingContext}
        import org.apache.spark.streaming.eventhubs.EventHubsUtils
        import sqlContext.implicits._
        
        val ehParams = Map[String, String](
          "eventhubs.policyname" -> "<name of policy with listen permissions>",
          "eventhubs.policykey" -> "<key of policy with listen permissions>",
          "eventhubs.namespace" -> "<service bus namespace>",
          "eventhubs.name" -> "<event hub in the service bus namespace>",
          "eventhubs.partition.count" -> "1",
          "eventhubs.consumergroup" -> "$default",
          "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
          "eventhubs.checkpoint.interval" -> "10"
        )
        
        val ssc =  new StreamingContext(sc, Seconds(10))
        val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
        
        case class Message(msg: String)
        stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

        stream.print
        ssc.start


##<a name="runapps"></a>執行應用程式

1. 從 Zeppelin Notebook 執行含有程式碼片段的段落。 按下 **SHIFT + ENTER** 或 **播放** 右上角的按鈕。

    段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕擷取畫面如下所示：

    ![程式碼片段的輸出](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Output of the snipet")

2. 執行 **寄件者** 專案並按 **Enter** 開始傳送訊息至事件中樞的主控台視窗中。

3. 在 Zeppelin Notebook 的新段落中，輸入以下程式碼片段以讀取在 Spark 中接收到的訊息。

        %sql 
        select * from mytemptable limit 10

    下列螢幕擷取畫面顯示中所收到的訊息 **mytemptable**。

    ![在 Zeppelin 中接收訊息](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Receive messages in Zeppelin notebook")

4. 重新啟動 Spark SQL 解譯器以結束應用程式。 按一下 [ **直譯器** 的頂端，然後針對 Spark 解譯索引標籤上，按一下 **重新啟動**。

    ![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Restart the Zeppelin intepreter")

##<a name="sparkstreamingha"></a>以高可用性執行串流應用程式

使用 Zeppelin 來接收串流資料並傳遞到 HDInsight 上的 Spark 叢集是塑造應用程式原型的好方法。 不過，若要以高可用性和恢復功能在實際執行設定中執行串流應用程式，您需要執行以下作業：

1. 將相依性 jar 複製到與叢集相關聯的儲存體帳戶。
2. 建置串流應用程式。
3. RDP 到叢集，並將應用程式 jar 複製到叢集的前端節點。
3. RDP 到叢集，並在叢集節點上執行應用程式。

指示如何執行這些步驟和範例串流應用程式可以從 GitHub 下載 [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples)。 


##<a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [快速入門：在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [執行 HDInsight 中使用 Spark 與 BI 工具的互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 


