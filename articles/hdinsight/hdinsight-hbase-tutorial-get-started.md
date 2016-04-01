<properties
    pageTitle="HBase 教學課程：開始在 Hadoop 中使用 HBase |Microsoft Azure"
    description="遵循本 HBase 教學課程，開始在 HDInsight 中搭配 Hadoop 使用 Apache HBase。 使用 Hive 從 HBase Shell 建立資料表並加以查詢。"
    keywords="apache hbase,hbase,hbase shell,hbase tutorial"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/02/2015"
    ms.author="jgao"/>



# HBase 教學課程：開始在 HDInsight (Windows) 中搭配 Hadoop 使用 Apache HBase 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]


了解如何使用 Apache Hive 在 HDInsight 中建立 HBase 叢集、建立 HBase 資料表，以及查詢資料表。 如需一般 HBase 資訊，請參閱 [HDInsight HBase 概觀][hdinsight-hbase-overview]。

> [AZURE.NOTE] 這份文件中的資訊旨在說明 Windows 為基礎的 HDInsight 叢集。 如需使用以 Linux 為基礎的叢集，請參閱 [HBase 教學課程 ︰ 開始使用 HDInsight (Linux) 中的 Hadoop 使用 Apache HBase](hdinsight-hbase-tutorial-get-started-linux.md)。
>
> 以 Windows 為基礎的 HDInsight 上的 HBase (0.98.0 版) 只能與 HDInsight 3.1 叢集一起使用 (以 Apache Hadoop 和 YARN 2.4.0 為基礎)。 版本資訊，請參閱 [的 HDInsight 所提供的 Hadoop 叢集版本的新功能？][hdinsight-versions]

###必要條件

開始進行本 HBase 教學課程之前，您必須具備下列條件：

- **Microsoft Azure 訂閱**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **工作站** 用於 Visual Studio 2013 或更新版本 ︰ 如需指示，請參閱 [安裝 Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)。

## 建立 HBase 叢集

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**使用 Azure 入口網站建立 HBase 叢集**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下 [ **新增** 或 **+** 右上方角，然後 **資料 + 分析**, ，**HDInsight**。
3. 輸入下列值：

    - **叢集名稱** -輸入要識別此叢集的名稱。
    - **叢集類型** -選取 **HBase**。
    - **叢集作業系統** -選取 **Windows**。  建立以 Linux 為基礎的 HBase 叢集，請參閱  [HBase 教學課程 ︰ 開始使用 HDInsight (Linux) 中的 Hadoop 使用 Apache HBase](hdinsight-hbase-tutorial-get-started-linux.md)。
    - **版本** -選取 HBase 版本。
    - **訂閱** -選取您用來建立此叢集的 Azure 訂閱。
    - **資源群組** -建立新的 Azure 資源群組，或選取現有的 gpo。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)
    - **認證** -適用於 Windows 叢集，您可以建立叢集使用者 （又稱為 HTTP 使用者、 HTTP web 服務使用者） 和遠端桌面使用者。 按一下 [ **啟用遠端桌面** 將遠端桌面使用者認證。 我們在下一節將需要使用 RDP。
    - **資料來源** -建立新的 Azure 儲存體帳戶，或選取現有的 Azure 儲存體帳戶作為叢集預設檔案系統。 預設儲存體帳戶的位置，會決定叢集的位置。 預設儲存體帳戶和叢集必須位於同一個資料中心。
    - **請注意定價層** -選取區域的 HBase 叢集的伺服器數目

        > [AZURE.WARNING] HBase 服務的高可用性，您必須建立至少包含叢集 **三個** 節點。 這可確保如果一個節點故障，仍可在其他節點上使用 HBase 資料區域。

        > 若仍處於 HBase 的學習階段，請務必選擇 1 做為叢集大小，並在每次使用叢集後予以刪除，以降低成本。

    - **選擇性設定** -設定 Azure 虛擬網路、 設定指令碼動作，並新增其他儲存體帳戶。

4. 按一下 [ **建立**。

>[AZURE.NOTE] 刪除 HBase 叢集之後，您可以使用相同的預設儲存體帳戶和預設 blob 容器建立另一個 HBase 叢集。 這個新叢集將選取您在原始叢集中建立的 HBase 資料表。

## 使用 HBase Shell
目前，存取 HBase 有兩種方式。 本節將說明如何使用 HBase Shell。 下一節將說明如何使用 .NET SDK。

對大多數人而言，資料會以表格形式出現：

![hdinsight hbase 表格式資料][img-hbase-sample-data-tabular]

在實作 BigTable 的 HBase 中，相同的資料看起來像：

![hdinsight hbase bigtable 資料][img-hbase-sample-data-bigtable]

在您完成下一個程序後，就會更有意義。  

**使用 HBase Shell**

1. 使用 RDP 連接至 HDInsight 中的 HBase 叢集。 如需 RDP 指示，請參閱 [HDInsight 使用 Azure 入口網站中的管理 Hadoop 叢集][hdinsight-manage-portal]。
2. 在 RDP 工作階段中，按一下 [ **Hadoop 命令列** 位於桌面上的捷徑。
3. 開啟 HBase Shell：

        cd %HBASE_HOME%\bin
        hbase shell

4. 使用兩個資料行系列建立 HBase：

        create 'Contacts', 'Personal', 'Office'
        list
5. 插入一些資料：

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. 取得單一資料列

        get 'Contacts', '1000'

    您會看到與使用掃描命令相同的結果，因為只有一個資料列。

    如需 Hbase 資料表結構描述的詳細資訊，請參閱 [HBase 結構描述設計簡介][hbase-schema]。 如需其他 HBase 命令，請參閱 [Apache HBase 參考指南][hbase-quick-start]。


6. 結束 Shell

        exit

**將資料大量載入連絡人 HBase 資料表中**

HBase 包含數個將資料載入資料表的方法。 如需詳細資訊，請參閱 [大量載入](http://hbase.apache.org/book.html#arch.bulk.load)。


範例資料檔案已上傳至公用 blob 容器 wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt。 資料檔案的內容：

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

您可以建立文字檔，並將檔案上載至自己的儲存體帳戶 (如果您要的話)。 如需指示，請參閱 [hdinsight 的資料上傳][hdinsight-upload-data]。

> [AZURE.NOTE] 此程序會使用您在上一個程序中建立的連絡人 HBase 資料表。

1. 在 RDP 工作階段中，按一下 [ **Hadoop 命令列** 位於桌面上的捷徑。
2. 變更目錄：

        cd %HBASE_HOME%\bin

3. 執行下列命令，將資料檔案轉換成 StoreFiles 並存放在 Dimporttsv.bulk.output 所指定的相對路徑：

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. 執行下列命令，將資料從 /example/data/storeDataFileOutput 上傳至 HBase 資料表：

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. 您可以開啟 HBase Shell，並使用掃描命令來列出資料表內容。



## 使用 Hive 查詢 HBase 資料表

您可以使用 Hive 來查詢儲存在 HBase 中的資料。 本節將建立對應至 HBase 資料表的 Hive 資料表，並用以查詢您 HBase 資料表中的資料。

**開啟叢集儀表板**

1. 瀏覽至 **https://<HDInsight Cluster Name>.azurehdinsight.net/**。
5. 輸入 Hadoop 使用者帳戶的使用者名稱和密碼。 預設使用者名稱為 **admin** ，密碼是您輸入在建立程序。 隨即開啟新的瀏覽器索引標籤。
6. 按一下 [ **Hive 編輯器** 頁面的頂端。 Hive 編輯器外觀如下：

    ![HDInsight 叢集儀表板。][img-hdinsight-hbase-hive-editor]

**執行 Hive 查詢**

1. 輸入下列 HiveQL 指令碼至 Hive 編輯器，然後按一下 **提交** 建立對應至 HBase 資料表的 Hive 資料表。 在執行此陳述式前，請確定您已使用 HBase Shell 建立參考先前本教學課程的範例資料表。

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    等到 **狀態** 更新 **完成**。

2. 輸入下列 HiveQL 指令碼至 Hive 編輯器，然後再按一下 **提交**。 Hive 查詢會查詢 HBase 資料表中的資料：

        SELECT count(*) FROM hbasecontacts;

4. 若要擷取 Hive 查詢的結果，請按一下 [ **檢視詳細資料** 中連結 **作業工作階段** 視窗的工作執行完成時。 只會有一個工作輸出檔案，因為您將一個記錄放置到 HBase 資料表。




**瀏覽輸出檔案**

1. 在 [查詢主控台中，按一下 [ **檔案瀏覽器**。
2. 按一下做為 HBase 叢集之預設檔案系統的 Azure 儲存體帳戶。
3. 按一下 HBase 叢集名稱。 預設 Azure 儲存體帳戶容器會使用叢集名稱。
4. 按一下 [ **使用者**, ，然後按一下 [ **管理員**。 (此為 Hadoop 使用者名稱。)
6. 按一下 [工作名稱為 **上次修改** 符合 SELECT Hive 查詢執行的時間的時間。
4. 按一下 [ **stdout**。 儲存檔案並以記事本開啟檔案。 將會有一個輸出檔案。

    ![HDInsight HBase Hive 編輯器檔案瀏覽器][img-hdinsight-hbase-file-browser]

## 使用 .NET HBase REST API 用戶端程式庫

您必須從 GitHub 下載適用於 .NET 的 HBase REST API 用戶端程式庫並建置專案，才能使用 HBase .NET SDK。 下列程序包括此工作的指示。

1. 建立新的 C# Visual Studio Windows 桌面主控台應用程式。
2. 開啟 [NuGet 封裝管理員主控台，依序按一下 **工具** > **NuGet 封裝管理員** > **Package Manager Console**。
3. 在主控台中，執行下列 NuGet 命令：

        Install-Package Microsoft.HBase.Client

5. 新增下列 **使用** 陳述式，在檔案頂端 ︰

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 取代 **Main** 函式取代為下列 ︰

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. 設定中的前三個變數 **Main** 函式。
8. 按下 **F5** 執行應用程式。

## 檢查叢集狀態

HDInsight 中的 HBase 隨附於 Web UI，以供監視叢集。 使用 Web UI，您可要求關於區域的統計資料或資訊。

若要開啟 Web UI，您必須 RDP 到叢集中，然後按一下桌面上的 HMaster 資訊 Web UI 捷徑，或在網頁瀏覽器中使用下列 URL：

    http://zookeeper[0-2]:60010/master-status

在高可用性叢集中，您會找到目前使用中之 HBase 主要節點 (其正在主控 WebUI) 的連結。




## 後續步驟
在 HDInsight 的本 HBase 教學課程中，您已了解如何佈建 HBase 叢集，以及如何建立資料表，並從 HBase Shell 檢視這些資料表中的資料。 您同時也了解到如何使用 Hive 查詢 HBase 資料表中的資料，以及如何使用 HBase C# REST API 建立 HBase 資料表，並擷取其資料表中的資料。

如需詳細資訊，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]。
HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。
- [在 Azure 虛擬網路上建立 HBase 叢集][hdinsight-hbase-provision-vnet]。
由於 HBase 叢集已與虛擬網路整合，因此能夠部署到與您應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md)。 了解如何跨兩個 Azure 資料中心設定 HBase 複寫。
- [利用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]。
了解如何執行即時 [情感分析](http://en.wikipedia.org/wiki/Sentiment_analysis) 使用 HDInsight 之 Hadoop 叢集中的 HBase 針對巨量資料。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png


