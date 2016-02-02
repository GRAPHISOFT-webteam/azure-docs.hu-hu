<properties 
    pageTitle="建立並將資料從 Blob 儲存體載入 Hive 資料表 | Microsoft Azure" 
    description="建立 Hive 資料表，並將 Blob 中的資料載入 Hive 資料表" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="hangzh-msft" 
    manager="jacob.spoelstra" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2015" 
    ms.author="hangzh;bradsev" />



# 建立並將資料從 Azure Blob 儲存體載入 Hive 資料表

此**功能表**所連結的主題說明如何將資料擷取至目標環境，以在 Cortana 分析程序 (CAPS) 期間儲存和處理該資料。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## 簡介

**本文件**會顯示泛型 Hive 查詢，這類查詢會建立 Hive 資料表，並從 Azure Blob 儲存體載入資料。 同時也會提供一些關於資料分割 Hive 資料表，以及使用最佳化單欄式資料列 (ORC) 格式來提升查詢效能的指引。

## 必要條件

本文假設您已經：

* 建立 Azure 儲存體帳戶。 如需指示，請參閱 [建立 Azure 儲存體帳戶](../hdinsight-get-started.md#storage)
* 佈建含有 HDInsight 服務的自訂 Hadoop 叢集。 如需指示，請參閱 [自訂 Azure HDInsight Hadoop 叢集的進階分析](machine-learning-data-science-customize-hadoop-cluster.md)。
* 啟用叢集的遠端存取、登入，然後開啟 Hadoop 命令列主控台。 如需指示，請參閱 [存取 Hadoop 叢集的前端節點](machine-learning-data-science-customize-hadoop-cluster.md#headnode)。

## 將資料上傳至 Azure Blob 儲存體

如果您依照所提供的指示建立 Azure 虛擬機器 [適用於進階分析的 Azure 虛擬機器設定](machine-learning-data-science-setup-virtual-machine.md), ，此指令碼檔案應該已經下載至 * C:\\Users\\\<user name\>\\Documents\\Data 科學指令碼 * 目錄，虛擬機器上的。這些 Hive 查詢只要求您插入自己的資料結構描述，以及已準備好進行提交之適當欄位中的 Azure Blob 儲存體設定。

我們假設 Hive 資料表的資料為**未壓縮的**表格格式，而且資料已上傳至 Hadoop 叢集所使用之儲存體帳戶的預設 (或其他) 容器。

如果您想要練習 nyc 計程車車程資料 」 __，您需要先下載 24 <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC 計程車車程資料</a> 檔案 (12 個車程檔和 12 個費用檔案)， **解壓縮** 成.csv 檔案和其預設值 (或適當的容器) 的 Azure 儲存體帳戶，然後上傳的所有檔案中所述的程序所都建立 [自訂 Azure HDInsight Hadoop 叢集的進階分析程序和技術](machine-learning-data-science-customize-hadoop-cluster.md) 主題。 .csv 檔案上傳至儲存體帳戶上的預設容器的程序，請參閱這 [頁面](machine-learning-data-science-process-hive-walkthrough/#upload)。

## <a name="submit"></a>如何提交 Hive 查詢

您可以使用下列方法來提交 Hive 查詢：

* 叢集前端節點上的 Hadoop 命令列
* IPython Notebook
* Hive 編輯器
* Azure PowerShell 指令碼

Hive 查詢類似 SQL。 熟悉 SQL 的使用者可能會發現 <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">SQL 若要 Hive 小抄</a> 很有用。

提交 Hive 查詢時，您也可以控制 Hive 查詢輸出的目的地，它是否會出現在螢幕上，或是輸出到前端節點上的本機檔案或 Azure Blob。

### 透過 Hadoop 叢集前端節點中的 Hadoop 命令列主控台

如果查詢相當複雜，從 Hadoop 叢集的前端節點中直接提交 Hive 查詢，通常會導致整備速度比使用 Hive 編輯器或使用 Azure PowerShell 指令碼來提交還快。

登入 Hadoop 叢集的前端節點、在前端節點的桌面上開啟 Hadoop 命令列，然後輸入命令

    cd %hive_home%\bin

使用者在 Hadoop 命令列主控台中提交 Hive 查詢的方式有三種：

* 直接從 Hadoop 命令列
* 使用 .hql 檔案
* 從 Hive 命令主控台

#### 從 Hadoop 命令列直接提交 Hive 查詢

使用者可以執行類似

    hive -e "<your hive query>;

的命令，在 Hadoop 命令列中直接提交簡單的 Hive 查詢。 在下列範例中，紅色方塊框起來的是提交 Hive 查詢的命令，而綠色方塊框起來的則是 Hive 查詢的輸出。

![建立工作區](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### 提交 .hql 檔案中的 Hive 查詢。

當 Hive 查詢更為複雜且有多行時，在 Hadoop 命令列或 Hive 命令主控台中編輯查詢並不實際。 替代方法是在 Hadoop 叢集的前端節點中使用文字編輯器，並將 Hive 查詢儲存在前端節點本機目錄中的 .hql 檔案。 然後可以提交.hql 檔案中的 Hive 查詢使用 `-f` 引數中的 `hive` 命令，如下所示:

    `hive -f "<path to the .hql file>"`

#### 隱藏 Hive 查詢的進度狀態畫面顯示

根據預設，在 Hadoop 命令列主控台中提交 Hive 查詢之後，Map/Reduce 工作的進度將顯示於螢幕上。 若要隱藏 Map/Reduce 工作進度的畫面列印，您可以使用引數 `-S` (區分大小寫) 的引數，在命令列中，如下所示:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### 在 Hive 命令主控台中提交 Hive 查詢。

使用者也可以輸入 Hive 命令主控台執行  `hive` 從 Hadoop 命令列命令，然後提交 Hive 查詢從 Hive 命令主控台 **hive >** 提示字元。 範例如下。

![建立工作區](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

在此範例中，這兩個紅色方塊反白顯示的命令分別是用來進入 Hive 命令主控台，以及在 Hive 命令主控台中提交 Hive 查詢。 綠色方塊反白顯示的是 Hive 查詢的輸出。

上述範例會在螢幕上直接輸出 Hive 查詢結果。 使用者也可以將輸出寫入前端節點上的本機檔案，或寫入 Azure Blob。 接著，使用者可以使用其他工具，進一步分析 Hive 查詢的輸出。

#### 將 Hive 查詢結果輸出到本機檔案。

若要將 Hive 查詢結果輸出到前端節點上的本機目錄，使用者必須在 Hadoop 命令列中提交 Hive 查詢，如下所示：

    `hive -e "<hive query>" > <local path in the head node>`

#### 將 Hive 查詢結果輸出到 Azure Blob

使用者也可以將 Hive 查詢結果輸出到 Hadoop 叢集預設容器內的 Azure Blob。 執行此動作的 Hive 查詢看起來如下：

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

在下列範例中，Hive 查詢的輸出會寫入 blob 目錄 `queryoutputdir` Hadoop 叢集預設容器內。 在此處，您應該只提供目錄名稱，而不需提供 Blob 名稱。 錯誤將會擲回如果您同時提供目錄和 blob 名稱，例如 *wasb:///queryoutputdir/queryoutput.txt*。

![建立工作區](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

Hive 查詢的輸出會顯示於 Blob 儲存體中，方法是使用 Azure 儲存體總管 (或對等項目) 工具來開啟 Hadoop 叢集的預設容器。 如果只要擷取名稱中具有指定字母的 Blob，您可以套用篩選條件 (以紅色方塊反白顯示)。

![建立工作區](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### 透過 Hive 編輯器或 Azure PowerShell 命令

使用者也可以透過下列方式來使用查詢主控台 (Hive 編輯器)：在 Web 瀏覽器中輸入

*https://&#60;Hadoop 叢集名稱>.azurehdinsight.net/Home/HiveEditor*

。 請注意，系統將要求您輸入 Hadoop 叢集認證以進行登入。

或者，您可以 [使用 PowerShell 執行 Hive 查詢](../hdinsight/hdinsight-hadoop-use-hive-powershell.md)。


## <a name="create-tables"></a>建立 Hive 資料庫和資料表

Hive 查詢中共用 [Github 儲存機制](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) 並且可以從此處下載。

以下是建立 Hive 資料表的 Hive 查詢。

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string, 
        field2 int, 
        field3 float, 
        field4 double, 
        ...,
        fieldN string
    ) 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

以下是使用者需要插入的欄位和其他設定的說明：

- **&#60;資料庫名稱>**：使用者要建立之資料庫的名稱。 如果使用者只想要使用預設資料庫，則可省略 *create database...* 查詢。
- **&#60;資料表名稱>**：使用者想要在指定資料庫內建立之資料表的名稱。 如果使用者想要使用預設資料庫，可透過 *&#60;資料表名稱>* 直接參考資料表，而不需要使用 &#60;資料庫名稱>。
- **&#60;欄位分隔符號>**：上傳至 Hive 資料表的資料檔中分隔欄位的分隔符號。
- **&#60;資料行分隔符號>**：用來分隔資料檔中各行的分隔符號。
- **&#60;儲存體位置>**：用來儲存 Hive 資料表資料的 Azure 儲存體位置。 如果使用者未指定 *LOCATION &#60;儲存體位置>*，資料庫和資料表預設會儲存在 Hive 叢集之預設容器的 *hive/warehouse/* 目錄中。 如果使用者想要指定儲存體位置，儲存體位置必須位於資料庫和資料表的預設容器內。 這個位置必須是叢集之預設容器的相對位置，其格式為 *'wasb:///&#60;directory 1>/'* 或 *'wasb:///&#60;directory 1>/&#60;directory 2>/'* 等。執行查詢之後，系統會在預設容器內建立相對目錄。
- **TBLPROPERTIES("skip.header.line.count"="1")**：如果資料檔具有標頭行，使用者就必須在 *create table* 查詢的**結尾**處加入這個屬性。 否則，載入的標頭行將做為資料表的記錄。 如果資料檔不含標頭行，則可在查詢中省略此設定。

## <a name="load-data"></a>將資料載入至 Hive 資料表

以下是將資料載入 Hive 資料表的 Hive 查詢。

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;Blob 資料路徑>**：如果要上傳至 Hive 資料表的 Blob 檔案是在 HDInsight Hadoop 叢集的預設容器中，則 *&#60;Blob 資料路徑>* 的格式應該是 *'wasb:///&#60;此容器中的目錄>/&#60;Blob 檔案名稱>'*。 Blob 檔案也可以位於 HDInsight Hadoop 叢集的其他容器中。 在此情況下，*&#60;Blob 資料路徑>* 的格式應該是 *'wasb://&#60;容器名稱>@&#60;儲存體帳戶名稱>.blob.core.windows.net/&#60;Blob 檔案名稱>'*。
    >[AZURE.NOTE] 上傳至 Hive 資料表的 Blob 資料必須位於 Hadoop 叢集儲存體帳戶的預設或其他容器中。 否則，*LOAD DATA* 查詢將會失敗並提報它無法存取資料。 


## <a name="partition-orc"></a>進階主題: 資料分割資料表和儲存區以 ORC 格式的 Hive 資料

如果資料量很大，對於只需掃描資料表中數個資料分割的查詢而言，分割資料表就很有助益。 例如，依日期分割網站的記錄資料就很合理。

除了資料分割 Hive 資料表之外，對於使用最佳化單欄式資料列 (ORC) 格式來儲存 Hive 資料也很有幫助。 如需 ORC 格式的詳細資訊，請參閱 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Hive 讀取、 寫入及處理資料時使用 ORC 檔案提升效能</a>。

### 資料分割資料表

以下是建立資料分割資料表和並將資料載入其中的 Hive 查詢。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

查詢資料分割的資料表時，最好是將資料分割條件，在 **開頭** 的 `其中` 子句，這可以改善大幅搜尋效率。

    select 
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name> 
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 格式儲存 Hive 資料

使用者無法將資料從 Blob 儲存體直接載入以 ORC 格式儲存的 Hive 資料表。 以下是使用者為了將資料從 Azure Blob 載入以 ORC 格式儲存的 Hive 資料表所需採取的步驟。

1. 建立外部資料表 **STORED AS TEXTFILE**，並將資料從 Blob 儲存體載入該資料表。

     CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
     (
         field1 string,
         field2 int,
         ...
         fieldN date
     )
     ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
         lines terminated by '<line separator>' STORED AS TEXTFILE 
         LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");
    
     LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 建立和步驟 1 中建立的外部資料表具備相同結構描述及相同欄位分隔符號的內部資料表，並使用 ORC 格式儲存 Hive 資料。

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        ) 
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 從步驟 1 中的外部資料表選取資料，並插入 ORC 資料表

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

    >[AZURE.NOTE] 如果 TEXTFILE 資料表 *& #60; 資料庫名稱 >。 (& s) #60; textfile 資料表名稱 >* 具有資料分割，在步驟 3， `選取 * 從 < 資料庫名稱 >。 < textfile 資料表名稱 >` 命令將會選取資料分割變數做為傳回資料集中的欄位。將它插入 *&#60;資料庫名稱>.&#60;ORC 資料表名稱>* 將會失敗，因為 *&#60;資料庫名稱>.&#60;ORC 資料表名稱>* 沒有資料分割參數可做為資料表結構描述中的欄位。在此情況下，使用者需要明確選取要插入 *&#60;資料庫名稱>.&#60;ORC 資料表名稱>* 的欄位，如下所示：

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name> 
           WHERE <partition variable>=<partition value>;

4. 將所有資料插入 *&#60;資料庫名稱>.&#60;ORC 資料表名稱>* 之後，當您使用下列查詢時，即可安全捨棄 *&#60;外部文字檔資料表名稱>*：

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;


依照此程序執行之後，您應該會有含 ORC 格式之資料的資料表可供使用。


## 微調區段請至這裡

最後一節將討論使用者可以微調的參數，如此即可改善 Hive 查詢的效能。




