<properties
   pageTitle="使用 Beeline 與 HDInsight (Hadoop) 上的 Hive 搭配作業 |Microsoft Azure"
   description="學習如何使用 SSH 連線至 HDInsight 中的 Hadoop 叢集，然後使用 Beeline 以互動方式提交 Hive 查詢。Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# 利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用安全殼層 (SSH) 連線至以 Linux 為基礎的 HDInsight 叢集，並接著使用以互動方式提交 Hive 查詢 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 命令列工具。
> [AZURE.NOTE] Beeline 使用 JDBC 連接到 Hive。 如需有關如何搭配 Hive 使用 JDBC 的詳細資訊，請參閱 [連接到 Azure HDInsight 使用 Hive JDBC 驅動程式上的 Hive](hdinsight-connect-hive-jdbc-driver.md)。

## <a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上以 Linux 為基礎的 Hadoop。

* SSH 用戶端。 Linux、Unix 和 Mac OS 應該具備 SSH 用戶端。 Windows 使用者必須下載用戶端，例如 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

## <a id="ssh"></a>使用 SSH 連線

使用 SSH 命令，連線至 HDInsight 叢集的完整網域名稱 (FQDN)。 FQDN 將是您提供給叢集的名稱，然後是 **.azurehdinsight.net**。 例如，下列命令會連線至名為 **myhdinsight** 的叢集：

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供憑證金鑰進行 SSH 驗證** (在建立 HDInsight 叢集時)，可能需要指定用戶端系統上私密金鑰的位置：

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則需要在出現提示時提供密碼。

如需有關如何使用 SSH 與 HDInsight 的詳細資訊，請參閱 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Linux、 Unix 和 OS X 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-unix.md)。

### PuTTY (Windows 架構用戶端)

Windows 未提供內建 SSH 用戶端。 我們建議使用 **PuTTY**, ，這可以從下載 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

如需有關如何使用 PuTTY 的詳細資訊，請參閱 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Windows 在 HDInsight 上 ](hdinsight-hadoop-linux-use-ssh-windows.md)。

## <a id="beeline"></a>使用 Beeline 命令

1. 連線之後，使用下列命令來取得前端節點的主機名稱：

        hostname -f

    儲存傳回的主機名稱，因為稍後從 Beeline 連接到 HiveServer2 時會用到該資料。

2. 使用下列命令來啟動 Hive CLI：

        beeline

2. 從 `beeline >` 提示，請使用以下連接到 HiveServer2 服務。 以稍早傳回的前端節點主機名稱取代 __HOSTNAME__：

        !connect jdbc:hive2://HOSTNAME:10001/;transportMode=http admin

    出現提示時，輸入 HDInsight 叢集的系統管理員 (admin) 帳戶密碼。 建議連線後，提示將變更如下：

        jdbc:hive2://HOSTNAME:10001/>

3. Beeline 命令通常開頭 `!` 字元，例如 `! 協助` 顯示說明。 不過， `!` 經常令 ommited。 例如， `協助` 也能運作。

    如果您檢視說明]，您會發現 `! sql`, ，用來執行 HiveQL 陳述式。 不過，HiveQL 因此通常用於您可以查看 ommit `! sql`。 下列兩個陳述式的結果完全相同；顯示目前可透過 Hive 取得的資料表：

        !sql show tables;
        show tables;

    新的叢集上只能列出一個資料表︰__hivesampletable__。

4. 使用下列命令來顯示 hivesampletable 的結構描述︰

        describe hivesampletable;

    此命令會傳回下列資訊：

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    這會顯示資料表中的資料行。 我們雖可對此資料執行某些查詢，但讓我們改為建立全新的資料表來示範如何將資料載入 Hive 及套用結構描述。

5. 輸入下列陳述式，以使用 HDInsight 叢集隨附的範例資料來建立名為 **log4jLogs** 的新資料表：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式將執行下列動作：

    * **DROP TABLE** - 刪除資料表和資料檔 (如果資料表已存在)。
    * **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。
    * **ROW FORMAT** - 告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。
    * **STORED AS TEXTFILE LOCATION** - 將資料的儲存位置告訴 Hive (example/data 目錄)，且資料儲存為文字。
    * **SELECT** - 選取其資料行 **t4** 包含 **[ERROR]** 值的所有資料列計數。 這應該會傳回值 **3**，因為有 3 個資料列包含此值。
    * **INPUT__FILE__NAME LIKE '%.log'** - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 通常在使用 hive 查詢時，您在相同的資料夾中只會有具有相同結構描述的資料，不過此範例記錄檔會以其他資料格式儲存。
    > [AZURE.NOTE] 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
    >
    > 捨棄外部資料表並**不**會刪除資料，只會刪除資料表定義。

    此命令的輸出應類似這樣：

        資訊: Tez 工作階段尚未尚未建立。 開啟工作階段
        資訊:

        資訊: 狀態: 執行 (與應用程式識別碼 application_1443698635933_0001 YARN 叢集上執行)

        資訊: 對應 1:-/-減壓器 2: 0/1
        資訊: 對應 1: 0/1 減壓器 2: 0/1
        資訊: 對應 1: 0/1 減壓器 2: 0/1
        資訊: 對應 1: 0/1 減壓器 2: 0/1
        資訊: 對應 1: 0/1 減壓器 2: 0/1
        資訊: 對應 1: 0 (+ 1) / 1 減壓器 2: 0/1
        資訊: 對應 1: 0 (+ 1) / 1 減壓器 2: 0/1
        資訊: 對應 1: 1/1 減壓器 2: 0/1
        資訊: 對應 1: 1/1 減壓器 2: 0 (+ 1) / 1
        資訊: 對應 1: 1/1 減壓器 2: 1/1
        +----------+--------+--+
        |  嚴重性 |計數 |
        +----------+--------+--+
        |[錯誤] |3      |
        +----------+--------+--+
        1 個資料列選取 (47.351 秒)

4. 若要結束 Beeline，請使用 `! 結束`。

## <a id="file"></a>執行 HiveQL 檔案

Beeline 也可以用來執行包含 HiveQL 陳述式的檔案。 使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 __query.hql__ 的新檔案：

        nano query.hql

2. 編輯器開啟時，請使用下列做為檔案的內容。 此查詢將建立名為 **errorLogs** 的新「內部」資料表：

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式將執行下列動作：

    * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。 因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 所管理。
    * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。
    * **INSERT OVERWRITE ...選取** -選取資料列從 **log4jLogs** 資料表中含有 **[錯誤]**, ，然後將資料插入 **errorLogs** 資料表。
    > [AZURE.NOTE] 與外部資料表不同，捨棄內部資料表也會同時刪除基礎資料。

3. 若要儲存檔案，請使用 __Ctrl__+___X__，然後輸入 __Y__，最後按 __Enter__。

4. 使用下列命令，以使用 Beeline 來執行檔案。 以稍早取得的前端節點名稱取代 __HOSTNAME__，以及以管理員帳戶的密碼取代 __PASSWORD__：

        beeline -u 'jdbc:hive2://HOSTNAME:10001/;transportMode=http' -n admin -p PASSWORD -f query.hql

5. 若要確認已建立 **errorLogs** 資料表，啟動 Beeline 並連接到 HiveServer2，然後使用下列陳述式從 **errorLogs** 傳回所有資料列：

        SELECT * from errorLogs;

    應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]**：

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)


## <a id="summary"></a>摘要

如您所見，Beeline 命令提供簡單的方法，以互動方式在 HDInsight 叢集上執行 Hive 查詢。

## <a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[apache-tez]: http://tez.apache.org 
[apache-hive]: http://hive.apache.org/ 
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j 
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez 
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/ 
[hdinsight-use-oozie]: hdinsight-use-oozie.md 
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md 
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx 

