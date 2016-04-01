<properties 
    pageTitle="提交 Hive 查詢至 Hadoop 叢集 Cortana 分析程序中 |Microsoft Azure" 
    description="從 Hive 資料表處理資料" 
    services="machine-learning" 
    documentationCenter="" 
    authors="hangzh-msft" 
    manager="paulettm" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2015" 
    ms.author="hangzh;bradsev" /> 

#<a name="heading"></a>在 Cortana 分析程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集

本文件說明在 Azure 中，將 Hive 查詢提交至 HDInsight 服務所管理的 Hadoop 叢集的各種方式。 您可以使用下列方法來提交 Hive 查詢： 

* 叢集前端節點上的 Hadoop 命令列
* IPython Notebook 
* Hive 編輯器
* Azure PowerShell 指令碼 

提供泛型 Hive 查詢，可用來探索資料或產生使用內嵌 Hive 使用者定義函式 (UDF) 的功能。 

特定查詢的範例 [NYC 計程車車程資料](http://chriswhong.com/open-data/foil_nyc_taxi/) 案例也會提供在 [Github 儲存機制](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)。 這些查詢已經具備指定的資料結構描述，且準備好提交給次案例來執行。 

最後一節將討論使用者可以微調的參數，可改善 Hive 查詢的效能。

## 必要條件
本文假設您已經：
 
* 建立 Azure 儲存體帳戶。 如果您需要這項工作的指示，請參閱 [建立 Azure 儲存體帳戶](../hdinsight-get-started.md#storage) 
* 佈建含有 HDInsight 服務的 Hadoop 叢集。  如需指示，請參閱 [佈建 HDInsight 叢集](../hdinsight-get-started.md#provision)。
* 已將資料上傳至 Azure HDInsight Hadoop 叢集中的 Hive 資料表。 如果沒有，請遵循所提供的指示 [建立資料並載入 Hive 資料表](machine-learning-data-science-hive-tables.md) 第一次資料上傳至 Hive 資料表。
* 啟用叢集的遠端存取。 如需指示，請參閱 [存取 Hadoop 叢集的前端節點](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)。 


## <a name="submit"></a>如何提交 Hive 查詢

1. [透過 Hadoop 叢集前端節點中的 Hadoop 命令列提交 Hive 查詢](#headnode)
2. [利用 Hive 編輯器提交 Hive 查詢](#hive-editor)
3. [利用 Azure PowerShell 命令提交 Hive 查詢](#ps)
 
###<a name="headnode"></a> 1. 透過 Hadoop 叢集前端節點中的 Hadoop 命令列提交 Hive 查詢

如果 Hive 查詢相當複雜，在 Hadoop 叢集的前端節點中直接提交 Hive 查詢，通常會導致整備速度比使用 Hive 編輯器或 Azure PowerShell 指令碼進行提交還快。 

登入 Hadoop 叢集的前端節點、在前端節點的桌面上開啟 Hadoop 命令列，然後輸入命令 `cd %hive_home%\bin`。

使用者在 Hadoop 命令列中提交 Hive 查詢的方式有三種： 

* 直接
* 使用 .hql 檔案
* 利用 Hive 命令主控台

#### 在 Hadoop 命令列中直接提交 Hive 查詢。 

使用者可以執行類似 `hive -e "<your hive query>;` 的命令，在 Hadoop 命令列中直接提交簡單的 Hive 查詢。 在下列範例中，紅色方塊框起來的是提交 Hive 查詢的命令，而綠色方塊框起來的則是 Hive 查詢的輸出。

![建立工作區][10]

#### 提交 .hql 檔案中的 Hive 查詢。

若 Hive 查詢更複雜且有多行，則在命令列或 Hive 命令主控台中編輯查詢並不實際。 替代方法是在 Hadoop 叢集的前端節點中使用文字編輯器，將 Hive 查詢儲存於前端節點本機目錄上的 .hql 檔案中。 然後可以使用 `-f` 引數提交 .hql 檔案中的 Hive 查詢，如下所示：
    
    `hive -f "<path to the .hql file>"`

![建立工作區][15]


**隱藏 Hive 查詢的進度狀態畫面顯示**

根據預設，在 Hadoop 命令列中提交 Hive 查詢之後，Map/Reduce 工作的進度將顯示於螢幕上。 若要隱藏 Map/Reduce 工作進度的畫面顯示，您可以在命令列中使用引數 `-S` (大寫的 "S")，如下所示：

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### 在 Hive 命令主控台中提交 Hive 查詢。

使用者也可以在 Hadoop 命令列中執行 `hive` 命令，先進入 Hive 命令主控台，然後在 Hive 命令主控台中提交 Hive 查詢。 範例如下。 在此範例中，這兩個紅色方塊反白顯示的命令分別是用來進入 Hive 命令主控台，以及在 Hive 命令主控台中提交 Hive 查詢。 綠色方塊反白顯示的是 Hive 查詢的輸出。 

![建立工作區][11]

上述範例會在螢幕上直接輸出 Hive 查詢結果。 使用者也可以將輸出寫入前端節點上的本機檔案，或寫入 Azure Blob。 接著，使用者可以使用其他工具，進一步分析 Hive 查詢的輸出。

**將 Hive 查詢結果輸出到本機檔案。** 

若要將 Hive 查詢結果輸出到前端節點上的本機目錄，使用者必須在 Hadoop 命令列中提交 Hive 查詢，如下所示：

    `hive -e "<hive query>" > <local path in the head node>`

在下列範例中，Hive 查詢的輸出會寫入 `C:\apps\temp` 目錄中的 `hivequeryoutput.txt` 檔案。

![建立工作區][12]

**將 Hive 查詢結果輸出到 Azure Blob**

使用者也可以將 Hive 查詢結果輸出到 Hadoop 叢集預設容器內的 Azure Blob。 Hive 查詢必須如下：

    `insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

在下列範例中，Hive 查詢的輸出會寫入 Hadoop 叢集預設容器內的 Blob 目錄 `queryoutputdir`。 在此處，您只需提供目錄名稱，而不需提供 Blob 名稱。 如果您同時提供目錄和 Blob 名稱 (例如 `wasb:///queryoutputdir/queryoutput.txt`)，則會擲回錯誤。 

![建立工作區][13]

如果您使用 Azure 儲存體總管之類的工具開啟 Hadoop 叢集的預設容器，將會看到 Hive 查詢的輸出，如下所示。 您可以套用篩選條件 (以紅色方塊反白顯示)，只擷取名稱中具有指定字母的 Blob。

![建立工作區][14]

###<a name="hive-editor"></a> 2. 利用 Hive 編輯器提交 Hive 查詢

使用者也可以在 Web 瀏覽器 `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor` 中輸入 URL 來使用查詢主控台 (Hive 編輯器) (系統將要求您輸入 Hadoop 叢集認證以登入)，

###<a name="ps"></a> 3. 利用 Azure PowerShell 命令提交 Hive 查詢

使用者也可以使用 PowerShell 提交 Hive 查詢。 如需指示，請參閱 [使用 PowerShell 提交 Hive 工作](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)。 

## <a name="explore"></a>資料探索、功能工程及 Hive 參數微調

我們將在本節中使用 Azure HDInsight Hadoop 叢集中的 Hive，說明下列資料有爭議的工作：

1. [資料探索](#hive-dataexploration)
2. [功能產生](#hive-featureengineering)

> [AZURE.NOTE] Hive 查詢範例假設資料已上傳至 Azure HDInsight Hadoop 叢集中的 Hive 資料表。 如果沒有，請遵循 [建立資料並載入 Hive 資料表](machine-learning-data-science-hive-tables.md) 第一次資料上傳至 Hive 資料表。

###<a name="hive-dataexploration"></a>資料探索
以下提供數個 Hive 指令碼範例，可用來探索 Hive 資料表中的資料。

1. 取得每個資料分割的觀察計數
    `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 取得每天的觀察計數
    `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. 取得類別資料行中的層級  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 取得兩個類別資料行組合中的層級數目 
    `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 取得數值資料行的分佈  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 聯結兩個資料表來擷取記錄 

        SELECT 
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>, 
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>, 
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

###<a name="hive-featureengineering"></a>功能產生

本節將說明使用 Hive 查詢產生功能的方式： 
  
1. [以頻率為基礎的功能產生](#hive-frequencyfeature)
2. [二進位分類中類別變數的風險](#hive-riskfeature)
3. [從日期時間欄位擷取功能](#hive-datefeature)
4. [從文字欄位擷取功能](#hive-textfeature)
5. [計算 GPS 座標間的距離](#hive-gpsdistance)

> [AZURE.NOTE] 一旦產生額外功能，您可以將它們當成資料行新增至現有的資料表，或建立具有額外功能和主索引鍵，然後與原始資料表已加入新的資料表。

####<a name="hive-frequencyfeature"></a> 以頻率為基礎的功能產生

有時，計算類別變數層級的頻率，或是多個類別變數層級組合的頻率，是非常有用的。 使用者可以使用下列指令碼來計算頻率：

        select 
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select 
                <column_name1>,<column_name2>, count(*) as sub_count 
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;
    

####<a name="hive-riskfeature"></a> 二元分類中類別變數的風險

在二進位分類中，有時我們需要使用數值風險來取代非數值層級 (因為某些模型可能只會採用數值功能)，將非數值類別變數轉換成數值功能。 本節將示範一些計算類別變數風險值 (記錄機率) 的泛型 Hive 查詢。 


        set smooth_param1=1;
        set smooth_param2=20;
        select 
            <column_name1>,<column_name2>, 
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select 
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select 
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename> 
                )a
            group by <column_name1>, <column_name2>
            )b 

這個範例會設定變數 `smooth_param1` 和 `smooth_param2`，以減緩從資料計算得來的風險值。 風險範圍介於 -Inf 和 Inf 之間。 風險 > 0 代表目標等於 1 的機率大於 0.5。 

計算出風險資料表之後，使用者就可以藉由將資料表聯結至風險資料表，來將風險值指派給該資料表。 Hive 聯結查詢已於上一節提供。

####<a name="hive-datefeature"></a> 從日期時間欄位擷取功能

Hive 會和一組 UDF 一起出現，用以處理日期時間欄位。 在 Hive 中，預設的日期時間格式是 'yyyy-MM-dd 00:00:00' (例如 '1970-01-01 12:21:32')。 本節將顯示擷取月份日期及來自日期時間欄位的月份範例，以及將預設格式以外格式的日期時間字串轉換為預設格式的日期時間字串範例。 

        select day(<datetime field>), month(<datetime field>) 
        from <databasename>.<tablename>;

這個 Hive 查詢假設 `<datetime field>` 是預設的日期時間格式。

如果日期時間欄位不是預設格式，就需要先將日期時間欄位轉換為 Unix 時間戳記，然後將 Unix 時間戳記轉換為預設格式的日期時間字串。 當日期時間為預設格式之後，使用者就可以套用內嵌的日期時間 UDF 來擷取功能。

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

在這個查詢中，如果 `<datetime field>` 具有類似 `03/26/2015 12:04:39` 的模式，則 `'<pattern of the datetime field>'` 應為 `'MM/dd/yyyy HH:mm:ss'`。 若要進行測試，使用者可以執行

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

在這個查詢中，`hivesampletable` 預設會在佈建叢集時，隨附於所有 Azure HDInsight Hadoop 叢集中。 


####<a name="hive-textfeature"></a> 從文字欄位擷取功能

假設 Hive 資料表有一個文字欄位，而其是以空格分隔的文字字串，則下列查詢會擷取字串長度，以及字串中的字數。

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
        from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a> 計算 GPS 座標間的距離

本節中提供的查詢可直接在「NYC 計程車車程資料」上加以套用。 此查詢的目的是示範如何在 Hive 中套用內嵌的數學函式來產生功能。 

此查詢中所使用的欄位是上車與下車位置的 GPS 座標，其名稱為 pickup\_longitude、pickup\_latitude、dropoff\_longitude 及 dropoff\_latitude。 計算上車與下車座標間之直線距離的查詢如下：

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
        from nyctaxi.trip 
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10; 

計算兩個 GPS 座標間距離的數學方程式位於 [可移動的類型指令碼](http://www.movable-type.co.uk/scripts/latlong.html), 、 Peter Lapisu 作者。 在這個 Javascript 中，函數 toRad() 僅為 `lat_or_lon*pi/180`，可將角度轉換為弧度。 在此，`lat_or_lon` 為緯度或經度。 由於 Hive 不提供函式 `atan2`, ，但提供函數 `atan`, 、 `atan2` 函式由實作 `atan` 位於上述 Hive 查詢，根據其定義中的函式 [Wikipedia](http://en.wikipedia.org/wiki/Atan2)。 

![建立工作區][1]

Hive 內嵌的 Udf 可以在中找到的完整清單 [UDF 語言手動](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions)。 

## <a name="tuning"></a> 進階主題：微調 Hive 參數以提升查詢速度

Hive 叢集的預設參數設定可能不適合 Hive 查詢以及查詢正在處理的資料。 本節將討論一些使用者可以微調的參數，如此便可提升 Hive 查詢的效能。 使用者需要在處理資料的查詢之前新增參數微調查詢。 

1. Java 堆積空間 ︰ 對於涉及聯結大型資料集，或處理長記錄的查詢，常見的錯誤是 **堆積空間不足**。 這可以藉由將參數 `mapreduce.map.java.opts` 和 `mapreduce.task.io.sort.mb` 設定為所需的值來微調。 下列是一個範例：

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;
    

    這個參數會配置 4 GB 記憶體給 Java 堆積空間，也會藉由配置更多記憶體來使排序更有效率。 如果發生任何與堆積空間相關的工作失敗錯誤，這會是個很好的做法。

2. DFS 區塊大小：這個參數會設定檔案系統所儲存的最小資料單位。 例如，如果 DFS 區塊大小為 128 MB，則任何大小小於等於 128 MB 的資料都會儲存於單一區塊中，而大於 128 MB 的資料則會配置額外的區塊。 選擇非常小的區塊大小會在 Hadoop 中造成極大的負荷，因為名稱節點必須處理更多要求，以尋找與檔案有關的相關區塊。 在處理 GB (或更大型) 資料時，建議的設定如下：

        set dfs.block.size=128m;

3. 將 Hive 中的聯結作業最佳化：儘管 Map/Reduce 架構中的聯結作業通常是在縮減階段執行，但有時可藉由在對應階段中排程聯結 (亦稱為 "mapjoin") 來得到大量的收穫。 若要在適當時機引導 Hive 執行這個動作，我們可以設定：

        set hive.auto.convert.join=true;

4. 指定 Hive 的對應程式數目：儘管 Hadoop 允許使用者設定縮減程式的數目，但通常使用者可能不會設定對應程式的數目。 允許對此數目進行某種程度控制的一個技巧是選擇 hadoop 變數 (mapred.min.split.size 和 mapred.max.split.size)。 每個對應工作的大小取決於：

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    一般而言，mapred.min.split.size 的預設值為 0，mapred.max.split.size 的預設值是 Long.MAX，而 dfs.block.size 的預設值則是 64 MB。 誠如所見，若指定了資料大小，則藉由「設定」這些參數來微調它們，讓我們能夠微調所使用的對應程式數目。 

5. 以下將提及最佳化 Hive 效能的其他數個更進階選項。 這些選項會考慮到配置來對應和縮減工作的記憶體設定，在調整效能時非常實用。 請記住，`mapreduce.reduce.memory.mb` 不能大於 Hadoop 叢集中每個背景工作節點的實際記憶體大小。

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png


 

