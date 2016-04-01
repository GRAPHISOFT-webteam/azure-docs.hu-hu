<properties
   pageTitle="在 Linux 架構的 HDInsight 上使用 Hadoop 的秘訣 |Microsoft Azure"
   description="取得在 Azure 雲端中執行的熟悉 Linux 環境上使用 Linux 架構的 HDInsight (Hadoop) 叢集的實作秘訣。"
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

# 在 Linux 上使用 HDInsight 的相關資訊

以 Linux 為基礎的 Azure HDInsight 叢集可在您熟悉的 Linux 環境中提供於 Azure 雲端中執行的 Hadoop。 其操作大多與 Linux 安裝上的任何其他 Hadoop 相同。 本文件會指出其中應注意的特殊不同之處。

## 網域名稱

從網際網路連接到叢集時所要使用完整的網域名稱 (FQDN) 是 **（& s) lt; 叢集名稱 >。 n e t** 或 （適用於 SSH 只) **（& s) lt; clustername-ssh >。 azurehdinsight.net**。

就內部而言，叢集中的每個節點都具有在叢集組態期間指派的名稱。 若要尋找的叢集名稱，您可以瀏覽 __主機__ Ambari Web UI，在頁面上，或使用以下命令從 Ambari REST API 使用傳回的主機清單 [cURL](http://curl.haxx.se/) 和 [jq](https://stedolan.github.io/jq/):

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

取代 __密碼__ 系統管理員帳戶的密碼和 __CLUSTERNAME__ 與您的叢集名稱。 這個方法會傳回包含叢集中主機清單的 JSON 文件，然後 jq 會針對叢集中的每個主機提取 `host_name` 元素值。

如果您需要針對特定服務尋找節點的名稱，您可以查詢 Ambari 有無該元件。 例如，若要尋找主機有無 HDFS 名稱節點，使用下列項目。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

這會傳回描述服務的 JSON 文件，然後 jq 只會針對主機提取 `host_name` 值。

## 遠端存取服務

* **Ambari (web)** -https://&lt;clustername>.azurehdinsight.net

    使用叢集系統管理員使用者和密碼進行驗證，然後登入 Ambari 。 這也會用到叢集系統管理員使用者和密碼。

    驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

    > [AZURE.IMPORTANT] 雖然直接透過網際網路存取叢集的 Ambari，某些功能要靠存取叢集所使用的內部網域名稱的節點。 由於這是內部網域名稱且並未公開，因此在嘗試透過網際網路存取某些功能時會收到「找不到伺服器」的錯誤。
    >
    > 若要使用 Ambari Web UI 的完整功能，請使用 SSH 通道將 Web 流量以 Proxy 處理傳輸到叢集前端節點。 請參閱 [使用 SSH 通道存取 Ambari web UI、 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;clustername>.azurehdinsight.net/ambari

    > [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
    >
    > 驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

* **WebHCat (Templeton)** -https://&lt;clustername>.azurehdinsight.net/templeton

    > [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
    >
    > 驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

* **SSH** -& lt; 叢集名稱 >-ssh.azurehdinsight.net 連接埠 22 或 23。 連接埠 22 用來連接至前端節點 0、而 23 用來連接至前端節點 1。 如需前端節點的詳細資訊，請參閱 [HDInsight 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)。

    > [AZURE.NOTE] 您只能從用戶端電腦透過 SSH 存取叢集前端節點。 然後在連線後，再從前端節點使用 SSH 存取背景工作角色節點。

## 檔案位置

Hadoop 相關檔案可以在叢集節點的 `/usr/hdp` 上找到。 此目錄包含下列子目錄：

* __2.2.4.9-1__︰ 此目錄的名稱為 Hortonworks Data Platform 使用 HDInsight，因此您的叢集上的數字可能會與此處所列的一個不同的版本。
* __目前__︰ 此目錄包含目錄下的連結 __2.2.4.9-1__ 目錄中，而且每次您想要存取的檔案存在，因此您不必輸入版本編號 （也可能會變更，）。

範例資料和 JAR 檔案可於 Hadoop 分散式檔案系統 (HDFS) 或 Azure Blob 儲存體的 '/example' 或 'wasb:///example' 取得。

## HDFS、Azure Blob 儲存體及儲存最佳作法

在大部分的 Hadoop 散發套件中，是以叢集機器上的本機儲存體支援 HDFS 的運作。 這樣的方式雖有效率，但在用於雲端解決方案時則需高昂成本，因為運算資源是以每小時為單位來計費。

HDInsight 則是使用 Azure Blob 儲存體當做預設存放區，這麼做有下列好處：

* 長期儲存成本低廉

* 可從各種外部服務進行存取，例如網站、檔案上傳/下載公用程式、各種語言的 SDK 和網頁瀏覽器

由於 HDInsight 預設使用這種存放區，因此您通常不需要進行任何設定就能使用。 例如，下列命令會列出檔案 **/範例/資料** 資料夾，儲存在 Azure Blob 儲存體 ︰

    hadoop fs -ls /example/data

有些命令可能需要您指定您使用的是 Blob 儲存體。 對於這些前置詞與命令 **WASB: / /**。

HDInsight 也可讓您將多個 Blob 儲存體帳戶與叢集相關聯。 若要存取非預設 Blob 儲存體帳戶上的資料，您可以使用格式 **WASB: / / （& s) lt; 容器名稱 > @& lt; 帳戶名稱 >.blob.core.windows.net/**。 例如，下列命令會列出的內容 **/範例/資料** 目錄指定的容器和 Blob 儲存體帳戶 ︰

    hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### 叢集所使用的 Blob 儲存體為何？

叢集建立期間，您會選取使用現有 Azure 儲存體帳戶和容器，或是建立新的。 之後您可能就忘得一乾二淨。 您可以使用 Ambari REST API 尋找預設的儲存體帳戶和容器。

1. 請使用以下命令來擷取 HDFS 組態資訊：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. 在傳回的 JSON 資料中，找到 `fs.defaultFS` 項目。 這樣會以如下的格式包含預設容器和儲存體帳戶名稱：

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

    > [AZURE.TIP] 如果您已安裝 [jq](http://stedolan.github.io/jq/), ，您可以使用下列傳回只 `fs.defaultFS` 項目 ︰
    >
    > `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`

3. 若要尋找用來驗證儲存體帳戶的金鑰，或尋找與叢集相關聯的任何次要儲存體帳戶，使用下列方法：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

4. 在傳回的 JSON 資料中，找到以 `fs.azure.account.key` 開頭的項目。 項目名稱的其餘部分是儲存體帳戶名稱。 例如，`fs.azure.account.key.mystorage.blob.core.windows.net`。 此項目中儲存的值是用來驗證儲存體帳戶的金鑰。

    > [AZURE.TIP] 如果您已安裝 [jq](http://stedolan.github.io/jq/), ，您可以使用下列來傳回索引鍵和值的清單 ︰
    >
    > `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`

您也可以使用 Azure 入口網站尋找儲存體資訊︰

1. 在 [Azure 入口網站](https://portal.azure.com/), ，選取您的 HDInsight 叢集。

2. 從 __Essentials__ 區段中，選取 __所有設定__。

3. 從 __設定__, ，請選取 __Azure 儲存體金鑰__。

4. 從 __Azure 儲存體金鑰__, ，選取其中一個列出的儲存體帳戶。 如此會顯示有關儲存體帳戶的資訊。

5. 選取金鑰圖示。 如此會顯示此儲存體帳戶的金鑰。

### 如何存取 Blob 儲存體？

除了透過叢集的 Hadoop 命令，還有各種不同方式可用來存取 Blob：

* [適用於 Mac、 Linux 和 Windows 的 azure CLI](../xplat-cli-install.md)︰ 用於 Azure 的命令列介面命令。 安裝好後，請使用 `azure storage` 命令以協助使用儲存體，或是針對 Blob 特有命令使用 `azure blob`。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): python 指令碼，以使用 Azure 儲存體中的 blob。

* 各種 SDK：

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [儲存體 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>調整您的叢集規模

叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的資料節點數目，而不需要刪除然後再重新建立叢集。

正在叢集上執行其他工作或處理序時，您可以執行調整作業。

不同的叢集類型會受調整影響，如下所示：

* __Hadoop__︰ 在叢集中的節點數目時，一些在叢集服務會重新啟動。 這會導致執行中或擱置的工作在調整作業完成時失敗。 您可以在作業完成後重新提交這些工作。

* __HBase__︰ 調整作業完成後的幾分鐘內自動平衡區域伺服器。 若要手動平衡區域伺服器，請使用下列步驟：

    1. 使用 SSH 連線到 HDInsight 叢集。 如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列其中一份文件：

        * [從 Linux、Unix 和 Mac OS X 搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [從 Windows 搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. 使用下列命令來啟動 HBase Shell：

            hbase shell

    2. 載入 HBase Shell 後，使用下列命令來手動平衡區域伺服器︰

            balancer

* __Storm__︰ 執行調整作業之後，您應該重新平衡任何執行中 Storm 拓撲。 這可讓拓撲根據叢集中的新節點數目，重新調整平行處理原則設定。 若要重新平衡執行中的拓撲，請使用下列其中一個選項：

    * __SSH__︰ 連接到伺服器，並使用下列命令重新平衡拓撲 ︰

            storm rebalance TOPOLOGYNAME

        您也可以指定參數來覆寫拓撲原先提供的平行處理原則提示。 例如，`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` 會將拓撲重新設定為 5 個背景工作角色處理序、適用於 blue-spout 元件的 3 個執行程式，以及適用於 yellow-bolt 元件的 10 個執行程式。

    * __Storm UI__︰ 使用下列步驟重新平衡使用 Storm UI 的拓撲。

        1. [建立叢集的 SSH 通道，並開啟 Ambari web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

        2. 從左邊的頁面上的服務清單中選取 __Storm__。 然後選取 __Storm UI__ 從 __快速連結__。


            ![Storm UI entry in quick links](./media/hdinsight-hadoop-linux-information/ambari-storm.png)

            This will display the Storm UI:

            ![the storm ui](./media/hdinsight-hadoop-linux-information/storm-ui.png)

        3. Select the topology you wish to rebalance, then select the __Rebalance__ button. Enter the delay before the rebalance operation is performed.

如需有關調整 HDInsight 叢集的特定資訊，請參閱：

* [使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-portal-linux.md#scaling)

* [使用 Azure PowerShell 管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-command-line.md#scaling)

## 如何安裝 Hue (或其他 Hadoop 元件)？

HDInsight 是受管理的服務，這表示如果偵測到問題，叢集中的節點可能會終結並由 Azure 自動重新佈建。 因此，不建議在叢集節點上直接手動安裝項目。 請改用 [HDInsight 指令碼動作](hdinsight-hadoop-customize-cluster.md) 當您需要安裝下列 ︰

* 服務或網站，例如 Spark 或 Hue。
* 需要在叢集中的多個節點上進行組態變更的元件。 例如，必要的環境變數、建立記錄目錄或建立組態檔。

指令碼動作是在叢集佈建期間執行的 Bash 指令碼，而且可用來在叢集上安裝並設定其他元件。 範例指令碼可供安裝下列元件：

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)
* [Spark](hdinsight-hadoop-spark-install-linux.md)

如需開發您自己的指令碼動作的資訊，請參閱 [使用 HDInsight 進行指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

###JAR 檔案

獨立的 jar 檔案中提供了一些 Hadoop 技術，其中包含可用來做為 MapReduce 工作一部分的函式，或者來自 Pig 或 Hive 內部的函式。 使用指令碼動作安裝這些項目時，它們通常不需要任何設定，只需在佈建之後上傳到叢集，且可直接使用。 如果您想要確定元件會重新安裝叢集的映像，則可將 jar 檔案儲存於 WASB 中。

例如，如果您想要使用的最新版本 [DataFu](http://datafu.incubator.apache.org/), ，您可以下載包含專案的 jar 並將它上傳至 HDInsight 叢集。 接著遵循 DataFu 文件中，如何從 Pig 或 Hive 中使用它的指示進行。

> [AZURE.IMPORTANT] 是獨立的 jar 檔案的部分元件所提供的 [HDInsight]，但不是在路徑中。 如果您正在尋找特定元件，可在叢集上使用下列內容來搜尋：
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 這將會傳回任何相符 jar 檔案的路徑。

如果叢集已經提供某個元件版本做為獨立的 jar 檔案，但您想要使用不同的版本，則可將新版元件上傳到該叢集，並嘗試在您的工作中使用它。

> [AZURE.WARNING] 隨附於 HDInsight 叢集的元件受到完整支援，並以隔離並解決這些元件的相關問題協助 Microsoft 支援服務。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。 例如，有許多社群網站可以使用，像是 ︰ [適用於 HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), ，[http://stackoverflow.com](http://stackoverflow.com)。 Apache 專案也有專案網站 [http://apache.org](http://apache.org), ，例如 ︰ [Hadoop](http://hadoop.apache.org/), ，[Spark](http://spark.apache.org/)。

## 後續步驟

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)


