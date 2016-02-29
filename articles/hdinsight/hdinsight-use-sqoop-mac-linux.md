<properties
    pageTitle="在以 Linux 為基礎的 HDInsight 上使用 Hadoop Sqoop | Microsoft Azure"
    description="了解如何在 HDInsight 叢集上 Linux 架構的 Hadoop 與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。"
    editor="cgronlun"
    manager="paulettm"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>

#在 HDInsight 上將 Sqoop 與 Hadoop 搭配使用 (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Sqoop，在 HDInsight 叢集與 Azure SQL Database 或 SQL Server Database 之間進行匯入和匯出。

> [AZURE.NOTE] 這篇文章中的步驟使用 SSH 來連線至以 Linux 為基礎的 HDInsight 叢集。 Windows 用戶端也可以使用 Azure PowerShell 中所述上以 Linux 為基礎的叢集搭配使用 Sqoop [搭配 HDInsight (PowerShell) 中的 Hadoop 使用 Sqoop](hdinsight-use-sqoop.md)。

##什麼是 Sqoop？

雖然在處理非結構化資料和半結構化資料時 (例如記錄和檔案)，很自然地會選擇 Hadoop，但有時也需要處理儲存在關聯式資料庫中的結構化資料。

[Sqoop][sqoop-user-guide-1.4.4] 是專門在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。 在本教學課程中，您將使用 SQL Server Database 做為關聯式資料庫。

如需 HDInsight 叢集支援的 Sqoop 版本，請參閱 [的 HDInsight 所提供叢集版本的新功能?][hdinsight-versions]。


##先決條件

開始進行本教學課程之前，您必須具備下列條件：

- **工作站**: 具有 SSH 用戶端的電腦。

- **Azure CLI**: 如需詳細資訊，請參閱 [安裝和設定 Azure CLI](../xplat-cli-install.md)

- **以 Linux 為基礎的 HDInsight 叢集**: 如需叢集佈建的指示，請參閱 [開始使用 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) 或 [佈建 HDInsight 叢集][hdinsight-provision]。

- **Azure SQL database**: 本文件說明如何建立 SQL 資料庫範例。 如需有關 SQL 資料庫的詳細資訊，請參閱 [開始使用 Azure SQL database][sqldatabase-get-started]。

* **SQL Server**: 這份文件中的步驟也可使用，做一些修改，SQL Server，不過，HDInsight 叢集和 SQL Server 都必須是相同的 Azure 虛擬網路上。 若要使用這份文件搭配 SQL Server 的特定需求的詳細資訊，請參閱 [使用 SQL Server](#using-sql-server) 一節。

##了解案例

HDInsight 叢集附有一些範例資料。 您將使用名為的 Hive 資料表 **hivesampletable**, ，參考資料檔案位於 **wasb: / hive/hivesampletable**。 此資料表包含某些行動裝置資料。 此 Hive 資料表的結構描述為：

| 欄位 | 資料類型 |
| ----- | --------- |
| clientid | 字串 |
| querytime | 字串 |
| market | 字串 |
| deviceplatform | 字串 |
| devicemake | 字串 |
| devicemodel | 字串 |
| state | 字串 |
| country | 字串 |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

您會先將匯出 **hivesampletable** 到 Azure SQL database 或名為的資料表中的 SQL Server **mobiledata**, ，然後將此資料表匯入回到 HDInsight 在 **wasb: / 教學課程//tutorials/usesqoop/importeddata**。

##建立資料庫

1. 開啟終端機或命令提示字元，並使用下列命令來建立新的 Azure SQL Database 伺服器：

        azure sql server create <adminLogin> <adminPassword> <region>

    例如，`azure sql server create admin password "West US"`

    命令完成時，您應該會收到類似這樣的回應：

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT] 請注意此命令傳回的伺服器名稱。 這是所建立的 SQL Database 伺服器的簡短名稱。 完整的網域名稱 (FQDN) 是 **& lt; 簡短名稱 (& s) gt;。.database.windows.net**。

2. 使用下列命令來建立名為 **sqooptest** SQL 資料庫伺服器上:

        sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    完成時會傳回 [確定] 訊息。

    > [AZURE.NOTE] 如果您收到錯誤訊息指出您沒有存取權，您可能需要將用戶端工作站的 IP 位址加入至 SQL Database 防火牆，使用下列命令:
    >
    > `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##建立資料表

> [AZURE.NOTE] 有許多方式可以連線到 SQL Database 建立資料表。 下列步驟會使用 [FreeTDS](http://www.freetds.org/) 從 HDInsight 叢集。

1. 使用 SSH 來連線至 Linux 架構的 HDInsight 叢集。 連接時要使用的位址為 `CLUSTERNAME-ssh.azurehdinsight.net`，而連接埠為 `22`。

    如需有關使用 SSH 連線至 HDInsight 的詳細資訊，請參閱下列文件：

    * **Linux、 Unix 或 OS X 用戶端**: 請參閱 [從 Linux、 OS X 或 Unix 連接至以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Windows 用戶端**: 請參閱 [從 Windows 連接至以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. 使用下列命令來安裝 FreeTDS：

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. 安裝 FreeTDS 後，請使用下列命令來連接至先前建立的 SQL Database 伺服器：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    您將收到類似以下的輸出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. 在 `1>` 提示字元輸入下列幾行：

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    輸入 `GO` 陳述式後，將評估先前的陳述式。 首先， **mobiledata** 建立資料表時，則叢集的索引加入至它 (SQL Database 所需)。

    使用下列命令來確認已建立資料表：

        SELECT * FROM information_schema.tables
        GO

    您應該會看到如下所示的輸出：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. 在 `1>` 提示字元輸入 `exit` 以結束 tsql 公用程式。

##Sqoop export

2. 使用下列命令來建立從 Sqoop lib 目錄到 SQL Server JDBC 驅動程式的連結。 這可讓 Sqoop 使用此驅動程式來聯繫 SQL Database：

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. 使用下列命令以確認 Sqoop 看得見您的 SQL Database：

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    這應該會傳回一份資料庫清單，其中 **sqooptest** 您稍早建立的資料庫。

4. 使用下列命令，將資料從匯出 **hivesampletable** 至 **mobiledata** 資料表:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    這會指示 Sqoop 連接至 SQL 資料庫到 **sqooptest** 資料庫，然後將資料從匯出 **wasb: / hive/hivesampletable** (實體檔案 *hivesampletable*,，) 來 **mobiledata** 資料表。

5. 在命令完成後，使用下列程式碼連接至使用 TSQL 的資料庫：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    連線之後，請使用下列陳述式來確認資料已匯出到 **mobiledata** 資料表:

        SELECT * FROM mobiledata
        GO

    您應會看到資料表中的資料清單。 輸入 `exit` 以結束 tsql 公用程式。

##Sqoop import

1. 使用以下命令匯入 **mobiledata** 至 SQL 資料庫中資料表 **wasb: / 教學課程//tutorials/usesqoop/importeddata** HDInsight 上的目錄:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    匯入的資料會有以定位字元分隔的欄位，以及以換行字元終止的行。

2. 匯入完成後，使用下列命令來列出新目錄中的資料：

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##使用 SQL Server

您也可以使用 Sqoop 從 SQL Server 匯入和匯出資料 (在資料中心或在 Azure 中裝載的虛擬機器上)。 使用 SQL Database 和 SQL Server 之間的差異如下：

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路

    > [AZURE.NOTE] HDInsight 僅支援以位置為基礎虛擬網路，以及它目前無法使用以同質群組為基礎的虛擬網路。

    當您使用 SQL Server 資料中心裡時，您必須設定為虛擬網路 *站台對站台* 或 *點對站台*。

    > [AZURE.NOTE] 如 **點對站台** 虛擬網路時，SQL Server 必須執行 VPN 用戶端組態應用程式，可從 **儀表板** 的 Azure 虛擬網路組態。

    如需有關建立和設定虛擬網路的詳細資訊，請參閱 [虛擬網路組態工作](../services/virtual-machines/)。

* SQL Server 也必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱 [選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)

* 您可能必須設定 SQL Server 以接受遠端連線。 請參閱 [如何疑難排解 SQL Server 資料庫引擎連線](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) 如需詳細資訊

* 您必須建立 **sqooptest** 使用公用程式，例如 SQL Server 資料庫中的 **SQL Server Management Studio** 或 **tsql** -使用 Azure CLI 的步驟只適用於 Azure SQL Database

    TSQL 陳述式來建立 **mobiledata** 資料表很類似用於 SQL Database，除了建立叢集索引-這不是必要的 SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* 從 HDInsight 連線到 SQL Server 時，您可能必須使用 SQL Server 的 IP 位址 (除非您已設定網域名稱系統 (DNS)) 來解析 Azure 虛擬網路上的名稱。 例如：

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##後續步驟

現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

- [搭配 HDInsight 使用 Oozie][hdinsight-use-oozie]: 在 Oozie 工作流程中的使用 Sqoop 動作。
- [分析航班延誤資料使用 HDInsight][hdinsight-analyze-flight-data]: 使用 Hive 分析航班誤點資料，，然後使用 Sqoop 將資料匯出至 Azure SQL database。
- [將資料上傳至 HDInsight][hdinsight-upload-data]: 尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

