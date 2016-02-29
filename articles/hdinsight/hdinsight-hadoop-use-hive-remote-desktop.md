<properties
   pageTitle="在 HDInsight 中使用 Hadoop Hive 與遠端桌面 | Microsoft Azure"
   description="學習如何使用遠端桌面連接到 HDInsight 中的 Hadoop 叢集，然後使用 Hive 命令列介面執行 Hive 查詢。"
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

# 利用遠端桌面搭配使用 Hive 與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用遠端桌面連接到 HDInsight 叢集，然後使用 Hive 命令列介面 (CLI) 執行 Hive 查詢。

> [AZURE.NOTE] 這份文件不提供範例中所用的 HiveQL 陳述式所執行的工作詳細的的描述。 如需此範例中使用的 HiveQL 的相關資訊，請參閱[搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)。

##<a id="prereq"></a>先決條件

若要完成本文中的步驟，您需要下列項目：

* Windows 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* 執行 Windows 10、Windows 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

依照[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)中的指示，為 HDInsight 叢集啟用遠端桌面，然後進行連線。

##<a id="hive"></a>使用 Hive 命令

當您連線到 HDInsight 叢集的桌面時，請使用下列步驟來使用 Hive：

1. 從 HDInsight 桌面，啟動 **Hadoop 命令列**。

2. 輸入下列命令以啟動 Hive CLI：

        %hive_home%\bin\hive

    啟動 CLI 之後，將會看到 Hive CLI 提示字元：`hive>`。

3. 使用 CLI，輸入下列陳述式來建立新的資料表名稱為 **log4jLogs** 使用範例資料:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式會執行下列動作：

    * **DROP TABLE**: 刪除資料表和資料檔，如果資料表已存在。

    * **建立外部資料表**: 在 Hive 中建立新的 「 外部 」 資料表。 外部資料表只會在 Hive 中儲存資料表定義 (資料會保留在原始位置)。

        > [AZURE.NOTE] 當您預期以外部來源 (例如自動化的資料上傳程序)，或以其他 MapReduce 作業，更新基礎資料時，應該使用外部資料表，但您希望 Hive 查詢一律使用最新的資料。
        >
        > 捨棄外部資料表並 **不** 刪除資料，資料表定義。

    * **ROW FORMAT**: 告訴的 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。

    * **STORED AS TEXTFILE LOCATION**: 告訴 Hive 資料的儲存 (example/data 目錄)，並儲存為文字。

    * **選取**: 選擇的所有資料列計數，資料行 **t4** 包含值 **[錯誤]**。 這應該會傳回值為 **3** 因為有三個資料列包含此值。

    * **像是 '%.log' INPUT__FILE__NAME** -告訴 Hive，我們應該只傳回從檔案中的資料。 記錄檔。 這將限制包含此資料的 sample.log 檔案搜尋，對於不符合我們所定義結構描述的其他範例資料檔案，會防止其傳回資料。


4. 使用下列陳述式建立名為的新 「 內部 」 資料表 **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式會執行下列動作：

    * **CREATE TABLE IF NOT EXISTS**: 建立資料表，如果不存在。 因為 **外部** 未使用關鍵字，這是內部資料表，會儲存在 Hive 資料倉儲並完全透過 Hive 所管理。

        > [AZURE.NOTE] 不同於 **外部** 資料表，捨棄內部資料表也會刪除基礎資料。

    * **STORED AS ORC**: 以最佳化資料列單欄式 (ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。

    * **INSERT OVERWRITE ...選取**: 選取資料列從 **log4jLogs** 資料表中含有 **[錯誤]**, ，然後將資料插入 **errorLogs** 資料表。

    若要確認只包含 **[錯誤]** t4 儲存至資料行中 **errorLogs** 資料表，請使用下列陳述式傳回的所有資料列 **errorLogs**:

        SELECT * from errorLogs;

    三個資料列應該傳回，全部包含 **[錯誤]** 資料欄 t4 中。

##<a id="summary"></a>摘要

如您所見，Hive 命令提供簡單的方法，以互動方式在 HDInsight 叢集上執行 Hive 查詢、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


