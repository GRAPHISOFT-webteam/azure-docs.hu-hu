<properties 
    pageTitle="Cortana 分析程序實務 - 在 1 TB Criteo 資料集上使用 HDInsight Hadoop 叢集 | Microsoft Azure" 
    description="對採用 HDInsight Hadoop 叢集來建置和部署使用大型 (1 TB) 公開可用資料集模型的端對端案例使用進階分析程序和技術 (ADAPT) 使用" 
    services="machine-learning,hdinsight" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2015" 
    ms.author="ginathan;bradsev" /> 

# Cortana 分析程序實務 - 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集

在本逐步解說中，我們會示範使用 Cortana 分析程序中端對端與 [Azure HDInsight Hadoop 叢集](http://azure.microsoft.com/services/hdinsight/) 來儲存、 探索、 號召工程人員，以及縮減取樣資料，從其中一個公開可用 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集。 我們使用 Azure Machine Learning 在此資料上建置二進位的分類模型。 我們也會顯示如何將其中一個模型發佈為 Web 服務。

此外，也可以使用 IPython Notebook 來完成此逐步解說中說明的工作。 使用者想要嘗試這種方法，應該查閱 [使用 Hive ODBC 連線的 Criteo 逐步解說](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 主題。


## <a name="dataset"></a>Criteo 資料集說明

Criteo 資料是點選預測的資料集，大約是 370 GB 的 gzip 壓縮 TSV 檔案 (~1.3 TB 未壓縮)，包含超過 43 億筆記錄。 取自的 24 天點選資料即可 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/)。 為了方便資料科學家，我們已將可供試驗的資料解壓縮。

在此資料集中的每一筆記錄包含 40 個資料行： 

- 第一個資料行是標籤資料行，指出使用者是否按一下加入 (值 1) 或未按一下 (值 0) 
- 接下來 13 個資料行是數字，以及 
- 最後 26 個資料行是類別資料行 

資料行為匿名，並使用一系列的列舉名稱："Col1" (針對標籤資料行) 至 "Col40" (針對最後一個分類資料行)。            

以下是來自這個資料集的兩個觀察 (資料列) 的前 20 個資料行的摘錄：

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10   Col11   Col12   Col13   Col14   Col15           Col16           Col17           Col18           Col19       Col20   

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

此資料集中的數值及分類資料行中有遺漏值。 我們在以下說明簡單的方法，用來處理遺漏的值。 資料的其他詳細資料會在以下將它們儲存成 Hive 資料表時加以探究。

**定義 ︰** *點選率 (CTR):* 這是資料中點選的百分比。 在此 Criteo 資料集中，CTR 是大約 3.3%或 0.033。

## <a name="mltasks"></a>預測工作的範例
本逐步解說將討論兩個範例預測問題：

1. **二進位分類**︰ 預測使用者是否按下加入 ︰
    - 類別 0：未按一下
    - 類別 1：按一下

2. **迴歸**︰ 預測使用者功能按一下廣告機率。


## <a name="setup"></a>為資料科學設定 HDInsight Hadoop 叢集

**注意 ︰** 這通常是 **Admin** 工作。

設定 Azure 資料科學環境，用於使用 HDInsight 叢集以三個步驟建置預測性的分析解決方案：

1. [建立儲存體帳戶](storage-whatis-account.md)︰ 此儲存體帳戶用來將資料儲存在 Azure Blob 儲存體。 HDInsight 叢集中使用的資料會儲存在這裡。

2. [自訂適用於資料科學的 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)︰ 這個步驟會建立所有節點上安裝 64 位元 Anaconda Python 2.7 的 Azure HDInsight Hadoop 叢集。 自訂 HDInsight 叢集時有兩個需完成的重要步驟 (如本主題所述)。

    * 建立時您必須將步驟 1 中建立的儲存體帳戶與 HDInsight 叢集連結。 此儲存體帳戶用於存取可以在叢集內處理的資料。

    * 建立後，您必須對叢集的前端節點啟用遠端存取。 請記住您在此處指定的遠端存取認證 (與建立時指定叢集的不同)：您在以下會需要該資訊。

3. [建立 Azure ML 工作區](machine-learning-create-workspace.md)︰ 此 Azure Machine Learning 工作區用來建置機器學習模型，在初始資料瀏覽之後，並且 HDInsight 叢集上下載取樣。

## <a name="getdata"></a>取得並從公用來源取用資料

 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集可以透過按一下連結、 接受使用條款，並提供名稱存取。 其外觀的快照如下所示：

![接受 Criteo 條款](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

按一下 [ **繼續下載** 要閱讀更多有關資料集和它的可用性。

資料位於公用 [Azure blob 儲存體](storage-dotnet-how-to-use-blobs.md) 位置 ︰ wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/。 "wasb" 指的是 Azure Blob 儲存體位置。 

1. 這個公用 Blob 儲存體中的資料是由所解壓縮資料的三個子資料夾所組成。
        
    1. 子資料夾 *raw/count/* 包含前 21 天的資料-從 day\_20 的 day\_00
    2. 子資料夾 *raw/train/* 組成的資料，day\_21 某一天
    3. 子資料夾 *raw/test/* 組成兩天的資料、 day\_22 和 day\_23

2. 對於使用者想要以原始 gzip 資料開始，這些也都有可用的主要資料夾 *原始 /* 為 day_NN.gz，其中 NN 從 00 到 23。

本逐步解說中稍後會在我們建立 Hive 資料表時說明存取、瀏覽和模型化此資料而不需要任何本機下載的另一種方法。

## <a name="login"></a>登入到叢集前端節點

若要登入叢集前端節點，請使用 [Azure 管理](manage.windowsazure.com) 入口網站，以找出叢集。 在左側按一下 HDInsight 大象圖示，然後按兩下叢集的名稱。 瀏覽至 **組態** 索引標籤，在頁面底部的 [連接] 圖示上連按兩下，然後輸入您的遠端存取認證提示。 這會帶您前往叢集的前端節點。 

以下是典型的第一次登入叢集前端節點時看起來的外觀：

![登入到叢集](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


在左邊，我們會看到「Hadoop 命令列」，這將是我們進行資料瀏覽的主要位置。 我們也會看到兩個實用的 URL - "Hadoop Yarn Status" 和 "Hadoop Name Node"。 Yarn 狀態 URL 會顯示工作進度，而名稱節點 URL 則會提供叢集組態的相關詳細資料。 

我們現在已設定，並已準備好開始逐步解說的第一個部分：使用 Hive 的資料瀏覽和為 Azure Machine Learning 備妥資料。 

## <a name="hive-db-tables"></a> 建立 Hive 資料庫和資料表

若要建立我們的 Criteo 資料集的 Hive 資料表，請開啟 ***Hadoop 命令列*** 桌面上的前端節點，並輸入以下命令來進入 Hive 目錄

    cd %hive_home%\bin

**重要事項**: **執行本逐步解說中的所有 Hive 命令，從上述的 Hive bin / 目錄提示字元。如此可自動處理路徑相關問題。我們將使用詞彙 「 Hive 目錄提示 」、 「 Hive bin / 目錄提示 」，和 「 Hadoop 命令列 」 交替。** 

**重要事項 2**: **執行任何 Hive 查詢，一律可以執行下列** 
        cd %hive_home%\bin
        hive

Hive REPL "hive >" 出現記號後，只需剪下並貼上查詢即可執行。

下列程式碼會建立資料庫 "criteo"，然後產生 4 個資料表： 


*  *資料表來產生計數* 上天 day\_00 day\_20，來建置 
*  *作為訓練資料集的資料表* 上 day\_21，建置和 
* 兩個 *做為測試資料集的資料表* day\_22 和 day\_23 上分別建立。 

因為其中一天是假日，而且我們想要判斷模型是否可以偵測到假日與非假日之間點選率的差異，我們將我們的測試資料集分割成兩個不同資料表。

指令碼 [範例 #95; hive & #95; 建立 & #95; criteo & #95; 資料庫 & #95; 和 （& s) #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) 會顯示下面方便 ︰

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

請注意，這所有資料表都是外部的，因為我們只是指向 Azure Blob 儲存體 (wasb) 位置。 

**有兩種方式可以執行我們現在提及的任何 Hive 查詢。**

1. **使用 Hive REPL 命令列**: 第一個是發出"hive"命令，複製並貼上上述查詢，在 Hive REPL 命令列。 若要這樣做，請執行：

        cd %hive_home%\bin
        hive

    現在，在 REPL 命令列，剪下和貼上查詢即可執行。

2. **將查詢儲存至檔案，並執行命令**: 第二個是將查詢儲存為.hql 檔案 ([範例 #95; hive & #95; 建立 （& s) #95; criteo & #95; 資料庫 & #95; 與 & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) 然後發出下列命令來執行查詢 ︰

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### 確認資料庫和資料表的建立

接下來，我們從 Hive bin/ 目錄提示發出以下命令，確認資料庫的建立：

        hive -e "show databases;"

這會提供：

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

這會確認建立新資料庫，也就是 "criteo"。

若要查看我們建立的資料表，我們只要從 Hive bin/ 目錄提示發出以下命令：

        hive -e "show tables in criteo;"

我們會看到下列輸出：

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> 在 Hive 中的資料瀏覽

現在我們已準備好在 Hive 中執行一些基本的資料瀏覽。 我們從計算訓練中的範例數目和測試資料的資料表開始。

### 訓練範例的數目

內容 [範例 #95; hive & #95; 計數 & #95; 訓練 & #95; 資料表 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) 如下所示 ︰

        SELECT COUNT(*) FROM criteo.criteo_train;

這會產生：

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

或者，使用者也可以從 Hive bin/ 目錄提示發出下列命令：

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### 兩個測試資料集中測試範例的數目

我們現在會計算兩個測試資料集中的範例數目。 內容 [範例 & #95; hive & #95; 計數 & #95; criteo & #95; 測試 & #95; 天 & #95; 22 & #95; 資料表和 #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) 如下 ︰

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

這會產生：
    
        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

如往常，我們也可以透過發出下列命令，從 Hive bin/ 目錄提示呼叫指令碼：

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

最後，我們會探討 day\_23 為基礎的測試資料集中測試範例的數目。

要執行這項操作的命令是類似於上述程式碼 (請參閱 [範例 #95; hive & #95; 計數 & #95; criteo & #95; 測試 & #95; 天 & #95; 23 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

這會提供：
    
        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### 訓練資料集中的標籤分佈

訓練資料集中的標籤分佈值得一提。 確認這一點，我們將說明內容的 [範例 #95; hive & #95; criteo & #95; 標籤 & #95; 散發與 #95; 訓練 & #95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

這會產生標籤分佈：

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

請注意，正數標籤的百分比為大約 3.3% (與原始資料集一致)。
        
### 訓練資料集中一些數值變數的長條圖分佈

我們可以使用 Hive 的原生 「 histogram\_numeric 」 函式，以找出的數值變數分佈的外觀。 內容 [範例 #95; hive & #95; criteo & #95; 長條圖 & #95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) 如下所示 ︰

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

這會產生下列：

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

橫向檢視 - Hive 中的切割組合是用來產生類似 SQL 的輸出，而不是一般的清單。 請注意，在上述資料表中，第一個資料行對應至 bin 中心，而第二個對應至 bin 頻率。

### 訓練資料集中一些數值變數的近似百分比

數值變數是近似百分比的計算也值得一提。 Hive 的原生 「 percentile\_approx 」 會為我們。 內容 [範例 #95; hive & #95; criteo & #95; 近似 & #95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) 是 ︰

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

這會產生：

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

我們注意到百分比的分佈與通常任何數值變數的長條圖分佈相關。        

### 尋找訓練資料集中的某些類別資料行的唯一值數目

繼續進行資料瀏覽，我們現在發現，對於某些類別資料行，他們所採取的唯一值數目。 若要這樣做，我們顯示的內容 [範例 #95; hive & #95; criteo & #95; 唯一 & #95; 值 & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

這會產生：

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

我們注意到 Col15 有 1900 萬個唯一值！ 使用貝氏方法，像是「一個有效編碼」來編碼這類高維度類別變數不可行。 特別是，我們將說明並示範功能強大又穩健的技術，稱為 [以計數學習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) 有效率地解決此問題。 

我們在這個子節的最後來看一下一些其他類別資料行的唯一值數目。 內容 [範例 #95; hive & #95; criteo & #95; 唯一 & #95 值 & #95; 多 （& s) #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) 是 ︰

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

這會產生： 

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

我們看到，除了 Col20 以外，所有其他資料行都有許多唯一值。 

### 訓練資料集中成對類別變數的共生計數

訓練資料集中成對類別變數的共生計數也很有趣。 這可以使用中的程式碼來決定 [範例 #95; hive & #95; criteo & #95 配對; & #95; 類別 （& s) #95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

我們將依其發生計數反轉順序，並在此情況下查看前 15 個。 這會提供：

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> 對 Azure Machine Learning 的資料集縮減取樣

探索資料集並示範了我們如何可對任何變數 (包括組合) 的這類探索進行的動作之後，我們現在要對資料集縮減取樣，讓我們可以在 Azure Machine Learning 中建置模型。 回顧一下我們著重的問題在於：提供一組範例屬性 (從 Col2 到 Col40 的功能值)，我們預測 Col1 是否為 0 (未按一下) 或 1 (按一下)。 

為了對我們的訓練和測試資料集縮減取樣至原始大小的 1%，我們使用 Hive 的原生 RAND() 函式。 下一個指令碼， [範例 #95; hive & #95; criteo & #95; 可以 & #95; 訓練 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) 會為訓練資料集 ︰

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

這會產生：

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

指令碼 [範例 #95; hive & #95; criteo & #95; 可以 & #95; 測試 & #95; 天 & #95; 22 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) 會為測試資料，day\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

這會產生：

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


最後，指令碼 [範例 #95; hive & #95; criteo & #95; 可以 & #95; 測試 & #95; 天 & #95; 23 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) 會為測試資料，day\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

這會產生：

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

以此方式，我們已準備好使用縮減取樣的訓練和測試資料集，在 Azure Machine Learning 模型中建置模型。

在我們繼續 Azure Machine Learning 之前，還有最後一個重要元件是考量計數資料表。 在下一個子節中，我們將就此討論一些細節。

##<a name="count"></a> 計數資料表的簡短討論

如我們所見，數個類別變數有極高的維度性。 在我們的逐步解說中，我們會提供功能強大的技術，稱為 [以計數學習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) 有效率又穩健的方式編碼這些變數。 提供的連結中說明此技術的詳細資訊。 

**注意 ︰** 在此逐步解說中，我們著重在使用計數資料表來產生高維度類別功能的精簡表示法。 這當然不是編碼類別功能; 唯一的方法如需有關其他技術的詳細資訊，有興趣的使用者可以查看 [一個熱-編碼](http://en.wikipedia.org/wiki/One-hot) 和 [特徵雜湊](http://en.wikipedia.org/wiki/Feature_hashing)。 

為了對計數資料建置計數資料表，我們使用 raw/count 資料夾中的資料。 在模型建構區段中，我們告訴使用者如何從頭建置類別功能的這些計數資料表，或是可以為其瀏覽使用預先建置的計數資料表。 在下面，我們提到「預先建置的計數資料表」時，表示使用我們提供的計數資料表。 有關如何存取這些資料表的方式在以下詳細的說明。

## <a name="aml"></a> 使用 Azure Machine Learning 建置模型

在 Azure Machine Learning 建置模型的程序將遵循下列步驟：

1. [從 Hive 資料表取得資料到 Azure Machine Learning](#step1)
2. [建立實驗：清除資料、選擇學習者並使用計數資料表特性化](#step2)
3. [訓練模型](#step3)
4. [對測試資料進行評分模型](#step4)
5. [評估模型](#step5)
6. [將模型發佈為 Web 服務以供使用](#step6)

現在我們已經準備好在 Azure Machine Learning Studio 中建置模型。 我們縮減取樣的資料會在叢集中儲存為 Hive 資料表。 我們將使用 Azure Machine Learning **讀取器** 模組讀取此資料。 可存取此叢集之儲存體帳戶的認證如下。

### <a name="step1"></a> 步驟 1：使用讀取器模組從 Hive 資料表取得資料到 Azure Machine Learning，並選取它進行機器學習實驗

選取 [啟動 **+ 新增** ]-> [ **實驗** ]-> [ **的空白實驗**。 然後，從 **搜尋** 左上角，「 讀取器 」 的搜尋方塊。 將拖放 **讀取器** 模組實驗畫布 （螢幕的中間部分） 進行資料存取使用的模組。

這是什麼 **讀取器** 從 Hive 資料表取得資料時，如所示 ︰

![讀取器取得資料](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

如 **讀取器** 模組，圖形中所提供的參數值只是範例值，您必須提供排序。 以下是一些一般指引如何填寫之參數集的 **讀取器** 模組。

1. 選擇 「 Hive 查詢 」 **資料來源**
2. 在 **Hive 資料庫查詢** 方塊中，簡單的 SELECT * FROM < your\_database\_name.your\_table\_name >-就已經足夠。
3. **Hcatalog 伺服器 URI**︰ 如果您的叢集是"abc"，則這就是 ︰ https://abc.azurehdinsight.net
4. **Hadoop 使用者帳戶名稱**︰ 選擇委任叢集時的使用者名稱。 (非遠端存取使用者名稱！)
5. **Hadoop 使用者帳戶密碼**︰ 選擇委任叢集時上述使用者名稱的密碼。 (非遠端存取密碼！)
6. **輸出資料的位置**︰ 選擇"Azure"
7. **Azure 儲存體帳戶名稱**︰ 叢集相關聯的儲存體帳戶
8. **Azure 儲存體帳戶金鑰**︰ 叢集相關聯的儲存體帳戶金鑰。
9. **Azure 容器名稱**︰ 如果叢集名稱是"abc"，則這通常是只是"abc"。 


一次 **讀取器** 完成取得的資料 （您將看到在模組綠色勾號），將此資料儲存為資料集 （以您選擇的名稱）。 看起來像這樣：

![讀取器儲存資料](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

以滑鼠右鍵按一下輸出連接埠 **讀取器** 模組。 這會顯示 **做為資料集儲存** 選項和 **視覺化** 選項。  **視覺化** 選項，如果按下，會顯示 100 個資料列的資料，以及在右側面板適用於某些摘要統計資料。 若要將資料儲存，只要選取 **做為資料集儲存** 並遵循指示。

若要選取已儲存的資料集用於機器學習實驗，找出資料集使用 **搜尋** 方塊，如下所示。 然後只要輸入您提供給資料集的部分名稱即可存取它，並拖曳至主面板中。 將它拖放到主面板中，選取它來用於機器學習模型建構。

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

***重要事項 ︰*** **訓練和測試資料集執行這項操作。此外，請記住要使用您為此目的提供的資料庫名稱和資料表名稱。在圖中所使用的值僅供說明之用。**
 
### <a name="step2"></a> 步驟 2：在 Azure Machine Learning 中建立簡單的實驗來預測按一下/未按一下

我們的 Azure ML 實驗看起來如下所示：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

現在，我們會檢視這項實驗的關鍵元件。 提醒您，我們需要先將已儲存的訓練和測試資料集拖曳至我們的實驗畫布上。

#### 清除遺漏的資料

 **清除遺漏資料** 模組會以其名稱所示 ︰ 清除遺漏的資料可以是使用者指定的方式。 查看此模組，我們看到這個：

![清除遺漏的資料](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

在這裡，我們選擇將所有的遺漏值取代為 0。 還有其他選項，可以藉由查看模組中的下拉式清單看到。

#### 資料上的功能工程

大型資料集的部分類別功能可以有數百萬的唯一值。 使用像是一個有效編碼的單純方法來表示高維度類別功能是完全不可行的。 在本逐步解說中，我們會示範如何透過内建的 Azure Machine Learning 模組來使用計數功能產生這些高維度類別變數的壓縮表示法。 最後結果是較小的模型、更快速的定型時間與相當於使用其他技術的效能度量。

##### 建置計數轉換

若要建置計數功能，我們使用 **建置計數轉換** 是 Azure Machine Learning 中可用的模組。 此模組如下所示：


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png)
![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**重要事項** ︰ 在 **計算資料行** ] 方塊中，我們會輸入我們想要在執行計數的資料行。 一般來說，這些是 (如上所述) 高維度類別資料行。 一開始，我們曾經提及 Criteo 資料集有 26 個類別資料行：從 Col15 至 Col40。 在這裡，我們依據這些資料行，並為其提供索引 (從 15 到 40，並以逗號分隔，如下所示)。

若要使用 MapReduce 模式中的模組 (適用於大型資料集)，我們必須存取 HDInsight Hadoop 叢集 (用於上述功能探索的叢集可以重複用於此目的) 和它的認證。 上圖說明填入的值看起來如何 (將提供做說明用途的值取代為您自己使用案例的相關值)。 

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

在上圖中，我們說明輸入 blob 位置的輸入方式。 這個位置會保留資料以在其中建置計數資料表。


此模組完成執行之後，我們可以儲存轉換稍後在模組上按一下滑鼠右鍵，然後選取 **儲存為轉換** 選項 ︰

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

在如上所示的實驗架構中，資料集 "ytransform2" 會精確地對應已儲存的計數轉換。 這項實驗中的其餘部分，我們假設讀取器使用 **建置計數轉換** 模組的特定資料以產生計數，並接著可以使用這些計數來產生計數功能的訓練和測試資料集上。

##### 選擇要將哪些計數功能納為訓練和測試資料集的一部分

一旦我們備妥計數轉換，使用者可以選擇哪些功能納入其訓練和測試資料集使用 **修改計數資料表參數** 模組。 為了完整性，我們會在下方顯示這個模組，但為了簡單起見，請不要真的在實驗中使用它。

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

在此情況下，如您所見，我們選擇只使用對數機率，並忽略撤退資料行。 我們也可以設定參數，例如記憶體回收 bin 臨界值，要加入多少虛擬優先範例才能平滑處理，以及是否使用任何 Laplacian 雜訊。 這些都是進階的功能，而且要注意的是對此類功能產生的新手使用者而言，預設值是個好起點。

##### 產生計數功能之前的資料轉換

現在我們要專注於一個重點，在實際產生計數功能之前要先轉換我們的訓練和測試資料。 請注意，有兩個 **執行 R 指令碼** 之前，我們將計數轉換套用到我們的資料使用的模組。

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

以下是第一個 R 指令碼：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


在這個 R 指令碼中，我們將資料行重新命名為 "Col1" 到 "Col40" 等名稱。 這是因為計數轉換必須要有這種格式的名稱。

在第二個 R 指令碼中，我們藉由降低取樣負類別來平衡正和負類別之間的散發 (分別為類別 1 和 0)。 下面的 R 指令碼會示範如何執行這個動作：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

在這個簡單的 R 指令碼，我們使用"pos\_neg\_ratio"設定正與負類別之間取得平衡。 這是很重要的動作，因為改善類別失衡通常會具有效能優勢，可處理類別散發扭曲的分類問題 (請記得在我們的案例中，我們有 3.3% 正類別和 96.7% 負類別)。

##### 在我們的資料上套用計數轉換

最後，我們可以使用 **套用轉換** 套用計數轉換我們的訓練和測試資料集的模組。 此模組會將儲存的計數轉換當成輸入，將訓練或測試資料集當成其他輸入，然後利用計數功能傳回資料。 如下所示：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### 計數功能的摘錄

它對於查看我們的案例中有什麼樣的計數功能很有啟發性。 以下示範該摘錄：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

在這個摘錄中，我們說明對於依賴的資料行，除了任何相關輪詢之外，我們還會取得計數和對數機率。 

現在，我們已準備好使用這些已轉換的資料集建置 Azure Machine Learning 模型。 在下一節中，我們會說明如何完成此作業。

#### Azure Machine Learning 模型建置

##### 選擇學習者

首先，我們必須選擇學習者。 我們要使用兩個類別推進式決策樹做為我們的學習者。 以下是這個學習者的預設選項：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

針對我們的試驗，我們只需選擇預設值。 我們注意到預設值通常是有意義的好方法，可以在效能上取得快速的基準。 一旦您有了基準之後，如果您選擇整理參數，您就可以藉此改善效能。

#### 訓練模型

對於訓練，我們只叫用 **訓練模型** 模組。 它的兩個輸入是兩個類別推進式決策樹和我們的訓練資料集。 如下所示：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### 評分模型

一旦我們擁有定型模型，我們就可以在測試資料集上評分，並評估其效能。 執行此作業，使用 **評分模型** 模組如下所示，以及與 **評估模型** 模組 ︰

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a> 步驟 5：評估模型

最後，我們想要分析模型的效能。 通常，針對兩個類別 (二進位) 分類的問題，AUC 是良好的測量方式。 若要將此情況視覺化，我們設定 **評分模型** 模組 **評估模型** 這個模組。 按一下 **視覺化** 上 **評估模型** 模組會產生圖形類似如下 ︰

![評估模組 BDT 模型](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

在二進位檔 (或兩個類別) 分類的問題中，曲線下面積 (AUC) 是預測準確度良好的測量方式。 我們在以下顯示在我們的測試資料集上使用此模型的結果。 若要取得這種情況，以滑鼠右鍵按一下輸出連接埠 **評估模型** 模組，然後 **視覺化**。

![視覺化評估模型模組](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a> 步驟 6：將模型發佈為 Web 服務
發佈 Azure Machine Learning 做為引起最少抱怨之 web 服務的功能，乃是使其可廣泛使用的寶貴功能。 完成作業後，任何人都可以利用他們需要預測的輸入資料來呼叫 web 服務，而 web 服務可使用模型傳回這些預測。

若要執行此動作，我們先將我們的訓練模型儲存為訓練的模型物件。 這是以滑鼠右鍵按一下 **訓練模型** 模組，並使用 **Save as Trained Model** 選項。

接下來，我們需要建立 web 服務的輸入和輸出連接埠： 

* 輸入連接埠會採用的資料形式和我們需要預測的資料相同 
* 輸出連接埠會傳回評分標籤和相關聯的機率。

#### 為輸入連接埠選取幾個資料列的資料

方便使用 **套用 SQL 轉換** 模組來選取 10 個資料列做為輸入連接埠資料。 始用下方顯示的 SQL 查詢，只為我們的輸入連接埠選取資料列。

![輸入連接埠資料](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### Web 服務
現在，我們已經準備好執行可以用來發佈 Web 服務的小型試驗。

#### 產生 Web 服務的輸入資料

做為預備步驟，由於計數資料表很大，我們會取得幾行測試資料，並使用計數功能從它產生輸出資料。 這會做為 web 服務的輸入資料格式。 這如以下所示：

![建立 BDT 輸入資料](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

注意 ︰ 針對輸入的資料格式，我們現在要使用的輸出 **計數 Featurizer** 模組。 一旦此實驗執行完成時，儲存的輸出 **計數 Featurizer** 做為資料集模組。 

**重要事項 ︰** 此資料集將用於 web 服務中的輸入資料。 

#### 發佈 Web 服務的評分實驗

首先，我們顯示其外觀。 基本結構是 **評分模型** 模組接受我們的訓練的模型物件和我們在上一個步驟中使用產生的輸入資料的幾行 **計數 Featurizer** 模組。 我們使用「專案資料行」來投射出評分標籤和評分的機率。 

![專案資料行](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

請注意如何 **專案資料行** 模組可用於 「 篩選 」 掉資料集中的資料。 我們顯示以下的內容：

![使用專案資料行模組篩選](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

若要取得藍色的輸入和輸出連接埠，您只要按一下 **準備 webservice** 右下方。 執行這項實驗也可讓我們發佈 web 服務，即可 **發佈 WEB 服務** 圖示位於右下方，如下所示。

![發佈 Web 服務](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


發佈 Web 服務之後，我們會被重新導向至看起來如下的頁面：

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

我們在左邊看到兩個 Web 服務的連結：

*  **要求/回應** 服務 （或 RRS） 適用於單一預測，我們會在這場研討會中使用。 
*  **批次執行** 服務 (BES) 用於批次預測，而且需要輸入的資料，用來進行預測位於 Azure Blob 儲存體。

按一下此連結 **要求/回應** 採用我們的頁面，讓我們前往預先定義程式碼在 C#、 python 和。此程式碼可以方便地用於對 web 服務進行呼叫。 請注意，需要使用此頁面上的 API 金鑰來進行驗證。 

將此 python 程式碼複製到 IPython notebook 中新的儲存格很方便。 

下面我們顯示一段具有正確的 API 金鑰的 python 程式碼。

![Python 程式碼](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


請注意，我們將預設 API 金鑰取代為 Web 服務的 API 金鑰。 按一下 **執行** IPython 這個儲存格上的筆記本會產生下列回應 ︰

![IPython 回應](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

我們會看到對我們要求的兩個測試範例 (在 python 指令碼的 JSON 架構中)，我們會以 "Scored Labels, Scored Probabilities" 形式獲得解答。 請注意，在此情況下，我們選擇預先定義的程式碼提供的預設值 (所有數值資料行為 0，所有類別資料行為字串 "value")。

這包含我們的端對端逐步解說，示範如何使用 Azure Machine Learning 處理大型資料集。 我們開始使用 1 TB 的資料、建構預測模型，並將其部署為雲端中的 Web 服務。


