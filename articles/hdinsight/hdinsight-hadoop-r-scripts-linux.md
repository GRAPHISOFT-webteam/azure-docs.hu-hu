<properties
    pageTitle="在以 Linux 為基礎的 HDInsight 上安裝 R | Microsoft Azure"
    description="了解如何安裝和使用 R 來自訂以 Linux 為基礎的 Hadoop 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>


# 在 HDInsight Hadoop 叢集上安裝和使用 R

您可以使用**指令碼動作**叢集自訂，在 HDInsight 上 Hadoop 中的任何一種叢集上安裝 R。 這可讓資料科學家和分析師使用 R 來部署強大的 MapReduce/YARN 程式設計架構，以處理部署在 HDInsight 中之 Hadoop 叢集上的大量資料。
> [AZURE.NOTE] 此文件中的步驟需要以 Linux 為基礎的 HDInsight 叢集。 在 R 中使用 Windows 叢集的資訊，請參閱 [安裝和使用 R HDinsight Hadoop 叢集 (Windows) 上的](hdinsight-hadoop-r-scripts.md)。

## 什麼是 R？

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> 是用於統計計算的開放原始碼語言和環境。 R 提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。 R 是廣泛各種不同領域中，大多數專業統計人員和科學家慣用的程式設計環境。

R 指令碼可以在 HDInsight 中的 Hadoop 叢集上執行，這些叢集是在建立時已藉由使用「指令碼動作」來自訂安裝 R 環境的叢集。 R 與 Azure Blob 儲存體 (WASB) 相容，因此便可在 HDInsight 上使用 R 來處理儲存在該處的資料。

## 指令碼會執行哪些作業

用來在您的 HDInsight 叢集上安裝 R 的指令碼動作會安裝下列提供基本 R 安裝的 Ubuntu 封裝：

* [r 基底](http://packages.ubuntu.com/precise/r-base): 基底 GNU R 封裝
* [r 基底-開發人員](http://packages.ubuntu.com/precise/r-base-dev): Auxilliary GNU R 封裝

下列 RHadoop 封裝也已安裝，以提供 MapReduce 和 HDFS 的整合：

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): 可讓 R 開發人員可以使用 Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): 可讓 R 開發人員可以使用 Hadoop HDFS (WASB hdinsight)

此外，也會安裝下列 R 封裝：

| R 封裝| 提供內容|
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html)| Java 介面的低階 R。|
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html)| R 與 C++ 整合。|
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html)| R 物件序列化/還原序列化為 JSON|
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html)| 整數向量的位元運算函數。|
| [摘要](https://cran.r-project.org/web/packages/digest/index.html)| 建立 R 物件的密碼編譯雜湊摘要。|
| [功能](https://cran.r-project.org/web/packages/functional/index.html)| Curry、撰寫和其他較高順序函數|
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html)| 有彈性地重新建構及彙總資料。|
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html)| 一般字串作業的簡單、一致包裝函式。|
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html)| 用於分割、套用和合併資料的工具。|
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html)| 用於移動視窗統計值、GIF、Base64、ROC AUC 等等的工具。|
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html)| 近似字串比對和字串距離函數。|

## 使用指令碼動作安裝 R

[Https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh) 指令碼動作用來在 HDInsight 叢集上安裝 R。 本節提供有關如何在使用 Azure 入口網站佈建叢集時使用指令碼的指示。
> [AZURE.NOTE] 您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。 如需有關如何使用這些方法的詳細資訊，請參閱 [指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 開始使用中的步驟來佈建叢集 [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal), ，但不是會完成佈建。

2. 在 [選用設定]**** 刀鋒視窗中，選取 [指令碼動作]****，並提供下列資訊：

    * __名稱__：輸入指令碼動作的易記名稱。
    * __指令碼 URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __HEAD__：勾選此選項
    * __WORKER__：勾選此選項
    * __ZOOKEEPER__：勾選此選項以在 Zookeeper 節點上安裝。
    * __參數__：將此欄位保留空白

3. 在 [指令碼動作]**** 底部，使用 [選取]**** 按鈕以儲存組態。 最後，使用 [選用組態]**** 刀鋒視窗底部的 [選取]**** 按鈕，儲存選用組態資訊。

4. 繼續中所述，佈建叢集 [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)。

## 執行 R 指令碼

叢集完成佈建之後，請使用下列步驟，在叢集上使用 R 執行 MapReduce 作業。

1. 使用 SSH 連線到 HDInsight 叢集

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [使用 SSH 與以 Linux 為基礎從 Linux、 Unix 或 OS X 在 HDInsight 上的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Windows 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 從 `username@hn0-CLUSTERNAME: ~ $` 提示中輸入下列命令來啟動的互動式 R 工作階段:

        R

3. 輸入下列 R 程式。 這會產生數字 1 到 100，然後將它們乘以 2。

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    第一行會呼叫 RHadoop 程式庫 rmr2，用於 MapReduce 作業。

    第二行產生值 1-100，然後將它們儲存在 Hadoop 檔案系統使用 `to.dfs`。

    第三行會使用 rmr2 所提供的功能建立 MapReduce 程序並開始處理。 您應該會在程序開始時看到幾行捲動過去。

4. 接下來，請使用下列項目查看儲存 MapReduce 輸出的目標暫存路徑：

        print(calc())

    這應該是類似的程式碼 `/tmp/file5f615d870ad2`。 若要檢視實際輸出，請使用下列項目：

        print(from.dfs(calc))

    輸出應該會看起來如下：

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. 若要關閉 R，請輸入以下內容：

        q()



## 後續步驟

- [色調在 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-hue-linux.md)。 色調是 Web UI，可讓您更輕鬆地建立、執行及儲存 Pig 和 Hive 工作，以及瀏覽您的 HDInsight 叢集的預設儲存體。

- [上安裝和使用 Spark HDInsight 叢集的 ][hdinsight-install-spark] 的指示，有關如何使用叢集自訂，以安裝和使用 Spark HDInsight Hadoop 叢集上。 Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。

- [HDInsight 叢集上的安裝 Giraph](hdinsight-hadoop-giraph-install.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。 Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。

- [HDInsight 叢集上的安裝 Solr](hdinsight-hadoop-solr-install.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。 Solr 可讓您對儲存的資料執行功能強大的搜尋作業。

- [HDInsight 叢集上安裝的色調](hdinsight-hadoop-hue-linux.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝色調。 色調是一組 Web 應用程式，用來與 Hadoop 叢集互動。


[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md 
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md 

