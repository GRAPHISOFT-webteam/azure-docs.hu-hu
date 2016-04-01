<properties
    pageTitle="在 HDInsight 上將 Hadoop 工作的資料上傳 | Microsoft Azure"
    description="了解如何使用 Azure CLI、Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop 在 HDInsight 中上傳及存取 Hadoop 工作。"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/28/2015"
    ms.author="jgao"/>



#在 HDInsight 上將 Hadoop 工作的資料上傳

Azure HDInsight 在 Azure Blob 儲存體上提供了全功能的 Hadoop 分散式檔案系統 (HDFS)。 此儲存體是設計為 HDFS 的延伸，以便為使用者提供流暢的體驗。 此儲存體可讓 Hadoop 生態系統中的完整元件集直接在它管理的資料上運作。 Azure Blob 儲存體和 HDFS 是不同的檔案系統，但經過最佳化後，都非常適合儲存資料以及計算儲存的資料。 使用 Azure Blob 儲存體的優點的相關資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

**必要條件**

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。 如需指示，請參閱 [開始使用 Azure HDInsight][hdinsight-get-started] 或 [佈建 HDInsight 叢集][hdinsight-provision]。

##為什麼要使用 Blob 儲存體？

部署 Azure HDInsight 叢集通常是為了執行 MapReduce 工作，並在這些工作完成後卸除叢集。 將計算完成後的資料保留在 HDFS 叢集是成本較高的資料儲存方式。 對於使用 HDInsight 來處理的資料，Azure Blob 儲存體是一種高可用性、高延展性、大容量、低成本且可共用的儲存方案。 將資料儲存在 Blob 中，可安全地釋放做為計算用途的 HDInsight 叢集，而且不會遺失資料。

###目錄

Azure Blob 儲存體容器會以機碼/值組來儲存資料，而且沒有目錄階層。 然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。 HDInsight 會將它們視為就像是實際的目錄一樣。

例如，blob 的機碼可能 *input/log1.txt*。 實際上不存在 "input" 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的外觀。

因此，當您使用 Azure Explorer 工具時，可能會注意到一些 0 位元組的檔案。 這些檔案有兩種用途：

- 如果是空資料夾，這些檔案會標示資料夾的存在。 Azure Blob 儲存體很聰明，知道，如果名為 foo/bar 的 blob 存在，就稱為資料夾 **foo**。 表示空的資料夾的唯一方法呼叫，但是 **foo** 是保留這個特殊的 0 位元組檔案。

- 這些檔案中保存 Hadoop 檔案系統所需的特殊中繼資料，尤其是資料夾的權限和擁有者。

##命令列公用程式

Microsoft 提供下列公用程式來使用 Azure Blob 儲存體：

| 工具 | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure 命令列介面][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop 命令](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] 雖然 Azure CLI、 Azure PowerShell 與 AzCopy 都可以使用從外部 Azure，使用 Hadoop 命令只適用於 HDInsight 叢集上且只允許從本機檔案系統載入資料到 Azure Blob 儲存體。

###<a id="xplatcli"></a>Azure CLI

Azure CLI 是可讓您管理 Azure 服務的跨平台工具。 使用以下步驟將資料上傳至 Azure Blob 儲存體：

1. [安裝和設定 Azure CLI for Mac、 Linux 和 Windows](../xplat-cli-install.md)。

2. 開啟命令提示字元、Bash 或其他殼層，然後使用以下命令驗證您的 Azure 訂用帳戶。

        azure login

    出現提示時，請輸入訂用帳戶的使用者名稱和密碼。

3. 輸入以下命令可列出訂用帳戶的儲存體帳戶：

        azure storage account list

4. 選取含有您想要使用之 Blob 的儲存體帳戶，然後使用以下命令擷取此帳戶的金鑰：

        azure storage account keys list <storage-account-name>

    這應該會傳回 **主要** 和 **次要** 索引鍵。 複製 **主要** 金鑰值，因為它將用於後續的步驟。

5. 使用以下命令擷取儲存體帳戶內的 Blob 儲存體清單：

        azure storage container list -a <storage-account-name> -k <primary-key>

6. 使用以下命令將檔案上傳及下載至 Blob：

    * 上傳檔案：

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * 下載檔案：

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 如果您將一律使用相同的儲存體帳戶，您可以設定下列環境變數，而不是指定之帳戶與金鑰]，為每個命令 ︰
>
> * **AZURE\_STORAGE\_ACCOUNT**︰ 儲存體帳戶名稱
>
> * **AZURE\_STORAGE\_ACCESS\_KEY**︰ 儲存體帳戶金鑰

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell 是一種指令碼環境，可讓您在 Azure 中用來控制和自動化工作負載的部署及管理。 如需設定工作站以執行 Azure PowerShell 資訊，請參閱 [安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

**將本機檔案上傳至 Azure Blob 儲存體**

1. 開啟 Azure PowerShell 主控台中的指示 [安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
2. 在下列指令碼中設定前五個變數的值：

        $subscriptionName = "<AzureSubscriptionName>"
        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        Switch-AzureMode -Name AzureResourceManager

        Add-AzureAccount
        Select-AzureSubscription $subscriptionName

        # Get the storage account key
        $storageaccountkey = get-azurestoragekey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{$_.Primary}

        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. 將指令碼貼到 Azure PowerShell 主控台，並執行該指令碼來複製檔案。

如何使用 HDInsight，建立 PowerShell 指令碼例如看到 [HDInsight tools](https://github.com/blackmist/hdinsight-tools)。

###<a id="azcopy"></a>AzCopy

AzCopy 是一套命令列工具，此工具設計目的在於簡化將資料移入及移出 Azure 儲存體帳戶的傳輸工作。 您可以將它當做獨立工具來使用，也可以將此工具納入現有的應用程式中。 [下載 AzCopy][azure-azcopy-download]。

AzCopy 語法如下：

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

如需詳細資訊，請參閱 [AzCopy-上傳/下載檔案的 Azure Blob][azure-azcopy]。


###<a id="commandline"></a>Hadoop 命令列

Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Blob 儲存體。

若要使用 Hadoop 命令，您必須先使用下列其中一個方法連線到前端節點：

* **Windows 型 HDInsight**: [使用遠端桌面連線](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Linux 型 HDInsight**︰ 使用 SSH 進行連線 ([SSH 命令](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) 或 [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

例如，`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

因為 HDInsight 的預設檔案系統是位於 Azure Blob 儲存體中，所以 /example/data.txt 實際上是在 Azure Blob 儲存體中。 您也可以用下列語法來參考此檔案：

    wasb:///example/data/data.txt

或

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

如需其他 Hadoop 的命令清單，與檔案，請參閱 [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##圖形化用戶端

其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下提供數個這類應用程式的清單：

| 用戶端 | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Azure 儲存體總管](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a id="storageexplorer"></a>Azure 儲存體總管

*Azure 儲存體總管* 是有用的工具，以檢查及更改 blob 中的資料。 它是免費、 開放原始碼工具，您可以從下載 [http://storageexplorer.com/](http://storageexplorer.com/)。 原始碼亦可從此連結取得。

使用此工具之前，必須先知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。 如需關於取得此資訊的指示，請參閱 「 如何 ︰ 檢視、 複製和重新產生儲存體存取金鑰 」 一節 [建立、 管理或刪除儲存體帳戶][azure-create-storage-account]。  

1. 執行 Azure 儲存體總管。 如果這是您有第一次執行儲存體總管，系統會提示您 ___儲存體帳戶名稱__ 和 __儲存體帳戶金鑰__。 如果您有執行它之前，使用 __新增__ 按鈕來加入新的儲存體帳戶名稱和金鑰。

    輸入名稱和您的 HDinsight 叢集所使用的儲存體帳戶金鑰，然後選取 __儲存和開啟__。

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. 在此介面左邊的 [容器] 清單中，按一下與 HDInsight 叢集相關聯的容器名稱。 根據預設，這是 HDInsight 叢集的名稱，但如果您在建立叢集時輸入了特定名稱，則有可能不同。

6. 從工具列選取上傳圖示。

    ![工具列和反白顯示的上傳圖示](./media/hdinsight-upload-data/toolbar.png)

7. 指定要上傳，然後按一下 [檔案 **開啟**。 出現提示時，選取 __上載__ 檔案上傳至儲存體容器的根目錄。 如果您想要將檔案上傳至特定路徑中，輸入路徑中的 __目的地__ 欄位，然後選取 [ __上載__。

    ![檔案上傳對話方塊](./media/hdinsight-upload-data/fileupload.png)
    
    完成上傳檔案之後，您可以從 HDInsight 叢集上的作業加以使用。

##將 Azure Blob 儲存體掛接為本機磁碟機

請參閱 [為本機磁碟機的掛接 Azure Blob 儲存體](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

##服務

###Azure Data Factory

Azure Data Factory 服務是完全受管理的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。

Azure Data Factory 可用來將資料移至 Azure Blob 儲存體，或建立資料管線直接使用 HDInsight 功能，例如 Hive 和 Pig。

如需詳細資訊，請參閱 [Azure Data Factory 文件](http://azure.microsoft.com/documentation/services/data-factory/)。

###<a id="sqoop"></a>Apache Sqoop

Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。

如需詳細資訊，請參閱 [搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]。

##開發 SDK

Azure Blob 儲存體也可以使用 Azure SDK，透過下列程式設計語言來存取：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

如需有關如何安裝 Azure Sdk 的詳細資訊，請參閱 [Azure 下載](http://azure.microsoft.com/downloads/)


## 後續步驟
您現在已了解如何將資料匯入 HDInsight，請接著閱讀下列文章以了解如何執行分析：

* [Azure HDInsight 使用者入門][hdinsight-get-started]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [在 HDInsight 上使用 Hive][hdinsight-use-hive]
* [在 HDInsight 上使用 Pig][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


