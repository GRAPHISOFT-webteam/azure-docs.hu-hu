<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與遠端桌面 | Microsoft Azure"
   description="學習如何使用 Pig 命令，從連往 HDInsight 中 Windows 型 Hadoop 叢集的遠端桌面連線執行 Pig Latin 陳述式。"
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

#從遠端桌面連線執行 Pig 工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

本文件逐步解說如何使用 Pig 命令，從連往 Windows 型 HDInsight 叢集的遠端桌面連線執行 Pig Latin 陳述式。 Pig Latin 可讓您透過描述資料轉換來建立 MapReduce 應用程式，而不是建立對應和縮減函數。

在本文中，您將學習如何

##<a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Windows 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* 執行 Windows 10、Windows 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

依照[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)中的指示，為 HDInsight 叢集啟用遠端桌面，然後進行連線。

##<a id="pig"></a>使用 Pig 命令

2. 遠端桌面連線之後，啟動 **Hadoop 命令列** 使用桌面上的圖示。

2. 使用下列命令來啟動 Pig 命令：

        %pig_home%\bin\pig

    您會看到 `grunt>` 提示字元。

3. 輸入下列陳述式：

        LOGS = LOAD 'wasb:///example/data/sample.log';

    此命令會將 sample.log 檔案的內容載入至 LOGS 檔案。 您可以使用下列命令檢視檔案的內容：

        DUMP LOGS;

4. 套用規則運算式以僅擷取每筆記錄的記錄層級，來轉換資料：

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    您可以使用 **傾印** 轉換之後檢視資料。 在此案例中為 `DUMP LEVELS;`。

5. 使用下列陳述式，繼續套用轉換。 使用 `DUMP` 檢視每個步驟後的轉換結果。

    <table>
    <tr>
    <th>陳述式</th><th>作用</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>移除含有記錄層級之 Null 值的資料列，並將結果儲存到 FILTEREDLEVELS。</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>依記錄層級對資料列進行分組，並將結果儲存到 GROUPEDLEVELS。</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>建立一組新的資料，其中包含每個唯一記錄層級值和其發生次數。 這會儲存到 FREQUENCIES</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>依計數排序記錄層級 (遞減)，並且儲存到 RESULT</td>
    </tr>
    </table>

6. 您也可以使用 `STORE` 陳述式儲存轉換結果。 例如，下列命令將 `RESULT` 至 **/example/data/pigout** 目錄中為您叢集的預設儲存體容器:

        STORE RESULT into 'wasb:///example/data/pigout'

    > [AZURE.NOTE] 資料會儲存在名為的檔案中指定的目錄 **部分 nnnnn**。 如果目錄已經存在，則會收到錯誤訊息。

7. 若要結束 grunt 提示字元，請輸入下列陳述式。

        QUIT;

###Pig Latin 批次檔

您也可以使用 Pig 命令執行檔案中所含的 Pig Latin。

3. 結束 grunt 提示字元之後, 開啟 **記事本** ，並建立新檔案命名為 **pigbatch.pig** 中 **%pig_home%** 目錄。

4. 輸入或貼上下列數行 **pigbatch.pig** 檔案，並儲存:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. 使用下列命令以執行 **pigbatch.pig** 檔案中使用 pig 命令。

        pig %PIG_HOME%\pigbatch.pig

    批次工作完成後，您應該會看到下列輸出，而輸出應該會與先前步驟中使用 `DUMP RESULT;` 時相同：

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>摘要

如您所見，Pig 命令可讓您以互動方式執行 MapReduce 作業，或執行批次檔中所儲存的 Pig Latin 工作。

##<a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Pig 的一般資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

