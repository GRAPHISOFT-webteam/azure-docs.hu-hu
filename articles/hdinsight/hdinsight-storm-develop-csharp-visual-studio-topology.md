<properties
   pageTitle="使用 Visual Studio 和 C# 的 Apache Storm 拓撲 |Microsoft Azure"
   description="了解如何使用 HDInsight Tools for Visual Studio 在 Visual Studio 中建立簡單的字數拓撲，以使用 C# 建立 Storm 拓撲。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# 使用 Visual Studio 的 Hadoop 工具開發 Apache Storm on HDInsight 的 C# 拓撲

了解如何使用 HDInsight Tools for Visual Studio 來建立 C# Storm 拓撲。 本教學課程逐步解說如何在 Visual Studio 中建立新的 Storm 專案、如何在本機測試該專案，以及如何部署該專案至 Apache Storm on HDInsight 叢集。

您也將學習如何建立使用 C# 和 Java 元件的混合式拓撲。

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## 必要條件

-   下列其中一個 Visual Studio 版本

    -   Visual Studio 2012 含 [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    -   Visual Studio 2013 [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) 或 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

    -   Visual Studio 2015 或 [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

-   Azure SDK 2.5.1 或更新版本

-   HDInsight Tools for Visual Studio: 請參閱 [開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) 安裝及設定 HDInsight tools for Visual Studio。
    > [AZURE.NOTE] Visual Studio Express 不支援 HDInsight Tools for Visual Studio

-   Apache Storm on HDInsight 叢集: 請參閱 [開始使用 Apache Storm on HDInsight](hdinsight-storm-getting-started.md) 的步驟來建立叢集。
    > [AZURE.NOTE] 目前 HDInsight Tools for Visual Studio 只支援 Storm on HDInsight 3.2 版叢集。

## 範本

HDInsight Tools for Visual Studio 提供下列範本::

| 專案類型| 示範|
| ------------ | ------------- |
| Storm 應用程式| 空白 Storm 拓樸專案|
| Storm Azure SQL 寫入器範例| 如何寫入至 Azure SQL Database|
| Storm DocumentDB 讀取器範例| 如何從 Azure DocumentDB 讀取|
| Storm DocumentDB 寫入器範例| 如何寫入至 Azure DocumentDB|
| Storm EventHub 讀取器範例| 如何從 Azure 事件中樞讀取|
| Storm EventHub 寫入器範例| 如何寫入至 Azure 事件中樞|
| Storm HBase 讀取器範例| 如何從 HDInsight 叢集上的 HBase 讀取|
| Storm HBase 寫入器範例| 如何寫入至 HDInsight 叢集上的 HBase|
| Storm 混合式範例| 如何使用 Java 元件|
| Storm 範例| 基本的字數統計拓撲|

> [AZURE.NOTE] HBase 讀取器和寫入器範例會使用 HBase REST API 與 HDInsight 叢集上的 HBase 通訊，而不是 HBase Java API。

在這份文件的步驟中，您將使用基本 Storm 應用程式專案類型來建立新的拓樸。

## 建立 C# 拓撲

1.  如果您有尚未安裝最新版的 HDInsight Tools for Visual Studio，請參閱 [開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

2.  開啟 Visual Studio，並依序選取 [檔案]**** > [新增]**** 和 [專案]****。

3.  從 [新增專案]**** 畫面，展開 [已安裝]**** > [範本]****，然後選取 [HDInsight]****。 從範本清單中，選取 [Storm 應用程式]****。 在畫面底部，輸入 **WordCount** 做為應用程式名稱。

    ![image](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.  建立專案之後，您應該會有下列檔案：

    -   **Program.cs**：這會定義您專案的拓撲。 請注意，預設會建立含有一個 Spout 和一個 Bolt 的預設拓撲。

    -   **Spout.cs**：發出亂數的範例 Spout。

    -   **Bolt.cs**：保留 Spout 所發出數字之計數的範例 Bolt。

    建立專案時，最新的一部分 [SCP.NET 封裝](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) 會從 NuGet 下載。

在下列各節中，您會將此專案修改成基本 WordCount 應用程式。

### 實作 Spout

1.  開啟 **Spout.cs**。 Spout 是用來讀取來自外部來源之拓撲中的資料。 Spout 的主要元件如下：

    -   **NextTuple**：允許 Spout 發出新的 Tuple 時，由 Storm 所呼叫。

    -   **Ack** (僅限交易式拓撲)：針對從此 Spout 傳送的 Tuple，處理拓撲中其他元件所起始的認可。 認可 Tuple 可讓 Spout 知道下游元件已順利處理 Tuple。

    -   **Fail** (僅限交易式拓撲)：處理無法處理拓撲中之其他元件的 Tuple。 這提供機會來重新發出 Tuple，以重新處理 Tuple。

2.  將 **Spout** 類別的內容取代為下列內容。 這會建立將句子隨機發出至拓撲的 Spout。

    ```
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };


    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;


        Context.Logger.Info("Generator constructor called");


        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }


    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }


    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;


        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));


        Context.Logger.Info("NextTuple exit");
    }


    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }


    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

    請用一些時間閱讀註解，以了解此程式碼的作用。

### 實作 Bolt

1.  刪除專案中的現有 **Bolt.cs** 檔。

2.  在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [加入]**** > [新項目]****。 從清單中，選取 [Storm Bolt]****，並輸入 **Splitter.cs** 做為名稱。 重複此動作，以建立名為 **Counter.cs** 的第二個 Bolt。

    -   **Splitter.cs**：將會實作 Bolt，該Bolt 會將句子分成個別單字，並發出一串新文字。

    -   **Counter.cs**：將會實作 Bolt，該Bolt 會計算每個單字的數目，並發出一串新文字和每個單字的計數。

    > [AZURE.NOTE] 這些 Bolt 只會讀取和寫入資料流，但是您也可以使用 Bolt 與資料庫或服務等來源進行通訊。

3.  開啟 **Splitter.cs**。 請注意，它預設只有一個方法：**Execute**。 這是在 Bolt 收到要處理的 Tuple 時所呼叫。 此時，您可以讀取和處理內送 Tuple，以及發出輸出 Tuple。

4.  將 **Splitter** 類別的內容取代為下列程式碼：

    ```
    private Context ctx;


    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;


        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }


    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }


    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");


        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }


        Context.Logger.Info("Execute exit");
    }
    ```

    請用一些時間閱讀註解，以了解此程式碼的作用。

5.  開啟 **Counter.cs**，並將類別內容取代為下列內容：

    ```
    private Context ctx;


    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();


    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;


        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });


        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }


    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }


    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");


        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;


        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));


        Context.Logger.Info("Execute exit");
    }
    ```

    請用一些時間閱讀註解，以了解此程式碼的作用。

### 定義拓撲

Spout 和 Bolt 是以圖形方式排列，用以定義資料在元件之間的流動方式。 此拓撲的圖形如下：

![元件排列方式的影像](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

句子是從 Spout 發出，並分散到 Splitter Bolt 執行個體。 Splitter Bolt 會將句子分成多個單字，並將這些單字分散到 Counter Bolt。

因為字數會本機保留在 Counter 執行個體中，所以我們想要確保特定單字流向相同的 Counter Bolt 執行個體，因此只能有一個執行個體追蹤特定單字。 但是針對 Splitter Bolt，哪個 Bolt 收到哪個句子並不重要，因此我們只想要將句子負載平衡至那些執行個體。

開啟 **Program.cs**。 重要的方法是 **ITopologyBuilder**，其用來定義提交至 Storm 的拓撲。 將 **ITopologyBuilder** 的內容取代為下列程式碼，以實作先前所述的拓撲：

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

    // Add the spout to the topology.
    // Name the component 'sentences'
    // Name the field that is emitted as 'sentence'
    topologyBuilder.SetSpout(
        "sentences",
        Spout.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
        },
        1);
    // Add the splitter bolt to the topology.
    // Name the component 'splitter'
    // Name the field that is emitted 'word'
    // Use suffleGrouping to distribute incoming tuples
    //   from the 'sentences' spout across instances
    //   of the splitter
    topologyBuilder.SetBolt(
        "splitter",
        Splitter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
        },
        1).shuffleGrouping("sentences");

    // Add the counter bolt to the topology.
    // Name the component 'counter'
    // Name the fields that are emitted 'word' and 'count'
    // Use fieldsGrouping to ensure that tuples are routed
    //   to counter instances based on the contents of field
    //   position 0 (the word). This could also have been
    //   List<string>(){"word"}.
    //   This ensures that the word 'jumped', for example, will always
    //   go to the same instance
    topologyBuilder.SetBolt(
        "counter",
        Counter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
        },
        1).fieldsGrouping("splitter", new List<int>() { 0 });

    // Add topology config
    topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
    {
        {"topology.kryo.register","[\"[B\"]"}
    });

    return topologyBuilder;
```

請用一些時間閱讀註解，以了解此程式碼的作用。

## 提交拓撲

1.  在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [提交至 Storm on HDInsight]****。
    > [AZURE.NOTE] 如果出現提示，請輸入您 Azure 訂用帳戶的登入認證。 如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

2.  從 [Storm 叢集]**** 下拉式清單中選取 Storm on HDInsight 叢集，然後選取 [提交]****。 您可以使用 [輸出]**** 視窗監視提交是否成功。

3.  順利提交拓撲時，應該會出現叢集的 [Storm 拓撲]****。 從清單中選取 [WordCount]**** 拓撲，以檢視執行中拓撲的詳細資訊。
    > [AZURE.NOTE] 您也可以從**伺服器總管**檢視 **Storm 拓撲**：依序展開 [Azure]**** > [HDInsight]****，以滑鼠右鍵按一下 Storm on HDInsight 叢集，然後選取 [檢視 Storm 拓撲]****。

    使用 Spout 或 Bolt 的連結以檢視這些元件的資訊。 將會針對每個選取的項目開啟新的視窗。

4.  從 [拓撲摘要]**** 檢視中，按一下 [終止]**** 停止拓撲。
    > [AZURE.NOTE] 除非停用 Storm 拓撲或刪除叢集，否則 Storm 拓撲會繼續執行。

## 交易式拓撲

先前的拓撲為非交易式。 如果拓撲中某個元件的處理失敗，則拓撲內的元件不會實作任何重新執行訊息的功能。 針對範例交易式拓撲，請建立新的專案，然後選取 [Storm 範例]**** 做為專案類型。

交易式拓撲會實作下列項目來支援重新執行資料：

-   **中繼資料快取**：Spout 必須儲存所發出資料的中繼資料，這樣一來，失敗時，就可以重新擷取和發出資料。 此範例所發出的資料太少，因此為了重新執行，每個 Tuple 的原始資料都會儲存在字典中。

-   **Ack**: 拓撲中的每個 bolt 都可以呼叫 `this.ctx.Ack(tuple)` 來認可它已順利處理 tuple。 所有 bolt 都已認可 tuple `Ack` 叫用 spout 的方法。 這可讓 Spout 移除用於重新執行的快取資料，因為已完全處理資料。

-   **失敗**: 每個 bolt 都可以呼叫 `this.ctx.Fail(tuple)` 表示處理失敗的 tuple。 失敗會傳播至 `失敗` spout，其中重播 tuple 所使用的方法快取中繼資料。

-   **序列識別碼**：發出 Tuple 時，可以指定序列識別碼。 這應該是識別用於重新執行 (Ack 和 Fail) 處理之 Tuple 的值。 例如，發出資料時，[Storm 範例]**** 專案中的 Spout 會使用下列項目：

    ```
    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
    ```

    這會發出含有預設資料流之句子的新 Tuple，以及 **lastSeqId** 中所含的序列識別碼值。 在此範例中，只會遞增每個發出的 Tuple 的 **lastSeqId**。

如 [Storm 範例]**** 專案中所示，在執行階段可以根據組態來設定元件是否為交易式。

## 混合式拓撲

HDInsight Tools for Visual Studio 也可以用來建立混合式拓撲，其中有些元件是 C#，有些則是 Java。

針對範例混合式拓撲，請建立新的專案，然後選取 [Storm 混合式範例]****。 這會建立完整加上註解的範例，而此範例包含示範下列項目的數種拓撲：

-   **Java Spout** 和 **C# Bolt**：定義於 **HybridTopology_javaSpout_csharpBolt**

    -   交易式版本定義於 **HybridTopologyTx_javaSpout_csharpBolt**

-   **C# Spout** 和 **Java Bolt**：定義於 **HybridTopology_csharpSpout_javaBolt**

    -   交易式版本定義於 **HybridTopologyTx_csharpSpout_javaBolt**
        > [AZURE.NOTE] 這個版本也會示範如何使用文字檔中的 Clojure 程式碼做為 Java 元件。

若要切換提交專案時所使用的拓撲，只要移動 `[active (true)]` 陳述式，以您想要使用之前提交至叢集的拓撲。
> [AZURE.NOTE] 在 **JavaDependency** 資料夾中，所需的所有 Java 檔案都會提供為此專案的一部分。

建立和提交混合式拓撲時，請考慮下列項目：

-   **JavaComponentConstructor** 必須用來建立 Spout 或 Bolt 之 Java 類別的新執行個體。

-   **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** 應該用來將進入或離開 Java 元件的資料從 Java 物件序列化至 JSON。

-   提交拓撲至伺服器時，您必須使用 [其他組態]**** 選項指定 [Java 檔案路徑]****。 指定的路徑應該是含有 JAR 檔案的目錄，而 JAR 檔案包含您的 Java 類別。

### Azure 事件中心

SCP.Net 版本 0.9.4.203 引進了專用於事件中樞 Spout (從事件中心讀取的 Java spout) 的新類別和方法。 建立採用此 spout 的拓撲時，請使用下列方法：

-   **EventHubSpoutConfig** 類別：建立一個物件，其中包含 spout 元件的組態

-   **TopologyBuilder.SetEventHubSpout** 方法：將事件中樞 Spout 元件加入至拓撲

> [AZURE.NOTE] 相較於其他 Java 元件，雖然這些可讓您更輕鬆地使用事件中樞 Spout，您仍然必須使用 CustomizedInteropJSONSerializer 來序列化 spout 所產生的資料。

## 如何更新 SCP.NET

最新版 SCP.NET 支援透過 NuGet 進行封裝升級。 當新的更新可用時，您會收到升級通知。 若要手動檢查升級，請執行下列步驟：

1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案並選取 [管理 NuGet 封裝]****。

2. 從封裝管理員，選取 [更新]****。 如果有可用的更新，它會列出。 按一下 [更新]**** 按鈕讓封裝來安裝它。

> [AZURE.IMPORTANT] 如果您的專案是利用其中一個未使用 NuGet 進行封裝更新的舊版 SCP.NET 建立，您必須執行下列步驟更新為新的版本：
>
> 1.在 [方案總管]**** 中，以滑鼠右鍵按一下專案並選取 [管理 NuGet 封裝]****。
> 2.使用 [搜尋]**** 欄位，搜尋 **Microsoft.SCP.Net.SDK** 然後將其加入專案。

## 疑難排解

### 在本機測試拓撲

雖然很容易就可以將拓撲部署至叢集，但是在某些情況下，您可能需要在本機測試拓撲。 使用下列步驟，在開發環境上以本機執行和測試本教學課程中的範例拓撲。
> [AZURE.WARNING] 本機測試只適用於僅限 C# 的基本拓樸。 您不得將本機測試使用於混合式拓撲或使用多個資料流的拓撲，因為您將收到錯誤。

1.  在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [屬性]****。 在專案屬性中，將 [輸出類型]**** 變更為 [主控台應用程式]****。

    ![輸出類型](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)
    > [AZURE.NOTE] 請記得先將 [輸出類型]**** 變更回 [類別庫]****，再將拓撲部署至叢集。

2.  在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [加入]**** > [新項目]****。 選取 [類別]****，並輸入 **LocalTest.cs** 做為類別名稱。 最後按一下 [加入]****。

3.  開啟 **LocalTest.cs**，並在頂端加入下列 **using** 陳述式：

    ```
    using Microsoft.SCP;
    ```

4.  使用下列項目做為 **LocalTest** 類別的內容：

    ```
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();


        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);


            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion


        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion


        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);


            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    請用一些時間閱讀程式碼註解。 此程式碼會使用 **LocalContext**，在開發環境中執行元件，並將元件之間的資料流保存至本機磁碟機上的文字檔。

5.  開啟 **Program.cs** ，並將下列程式碼加入 **Main** 方法：

    ```
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();


    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

6.  儲存變更，然後按一下 **F5** 或選取 [偵錯]**** > [開始偵錯]**** 啟動專案。 應該會出現主控台視窗，並記錄測試進行的狀態。 出現 [測試已完成]**** 時，請按任意鍵關閉視窗。

7.  使用 **Windows 檔案總管** 找到的目錄，其中包含您的專案，例如，* * C:\Users\<your_user_name>\Documents\Visual Studio 2013 \ projects\wordcount\wordcount**。在此目錄中，開啟 * * Bin**, ，然後按一下 [ **偵錯**。您應該會看到執行測試時所產生的文字檔：sentences.txt、counter.txt 和 splitter.txt。開啟每個文字檔，並檢查資料。
    > [AZURE.NOTE] 字串資料會保存為這些檔案中的十進位值的陣列。 例如，\[[97,103,111]] 在 **splitter.txt** 檔案是 word '和'。

雖然在本機測試基本字數應用程式相當瑣細，但是當您具有與外部資料來源進行通訊或執行複雜資料分析的複雜拓撲時，就極具價值。 處理這類專案時，您可能需要在元件中設定中斷點並逐步執行程式碼，以隔離問題。
> [AZURE.NOTE] 請一定要先將 [專案類型]**** 設回 [類別庫]****，再部署至 Storm on HDInsight 叢集。

### 記錄資訊

您可以輕鬆地記錄資訊拓撲元件中使用 `Context.Logger`。 例如，以下會建立一個參考性記錄項目：

```
Context.Logger.Info("Component started");
```

您可以從 [Hadoop 服務記錄]**** (位於**伺服器總管中**) 檢視記錄的資訊。 展開 Storm on HDInsight 叢集的項目，然後展開 [Hadoop 服務記錄]****。 最後，選取要檢視的記錄檔。
> [AZURE.NOTE] 記錄會儲存在您叢集所使用的 Azure 儲存體帳戶中。 如果此訂用帳戶與您用來登入 Visual Studio 的訂用帳戶不同，則必須登入含有儲存體帳戶的訂用帳戶，才能檢視這項資訊。

### 檢視錯誤資訊

若要檢視執行中拓撲中所發生的錯誤，請使用下列步驟：

1.  從**伺服器總管**中，於 Storm on HDInsight 叢集上按一下滑鼠右鍵，然後選取 [檢視 Storm 拓撲]****。

2.  針對 **Spout** 和 **Bolt**，[最後一個錯誤]**** 資料行會有發生之最後一個錯誤的詳細資訊。

3.  選取發生錯誤之元件的 [Spout ID]**** 或 [Bolt ID]****。 在顯示的詳細資料頁面上，其他錯誤資訊將會列在頁面底部的 [錯誤]**** 區段中。

4.  若要取得詳細資訊，請從頁面的 [執行程式]**** 區段中選取 [連接埠]****，以查看最後幾分鐘的 Storm 背景工作記錄。

## 後續步驟

既然您已了解如何開發和部署 Storm 拓撲，HDInsight tools for Visual Studio，了解如何 [處理來自 Azure 事件中心與 Storm on HDInsight 事件](hdinsight-storm-develop-csharp-event-hub-topology.md)。

如需將資料流資料分割成多個資料流的 C# 拓撲的範例，請參閱 [C# Storm 範例](https://github.com/Blackmist/csharp-storm-example)。

若要探索建立 C# 拓撲的詳細資訊，請造訪 [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)。

如需更多使用 HDInsight 的方式，或更多 Storm on HDinsight 範例，請參閱下列內容：

**Apache Storm on HDInsight**

-   [部署和監視使用 Apache Storm on HDInsight 拓撲](hdinsight-storm-deploy-monitor-topology.md)

-   [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

**Apache Hadoop on HDInsight**

-   [使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

-   [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

-   [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

-   [開始使用 HBase on HDInsight](hdinsight-hbase-get-started.md)





