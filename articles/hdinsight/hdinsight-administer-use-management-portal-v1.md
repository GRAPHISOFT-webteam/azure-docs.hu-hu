<properties
    pageTitle="使用 Azure 傳統入口網站管理 HDInsight 中的 Hadoop 叢集 | Microsoft Azure"
    description="了解如何管理 HDInsight 服務。 建立 HDInsight 叢集、開啟互動式 JavaScript 主控台，以及開啟 Hadoop 命令主控台。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="jgao"/>

# 使用 Azure 傳統入口網站管理 HDInsight 中的 Hadoop 叢集

使用 [Azure 傳統入口網站](https://manage.windowsazure.com), ，您可以佈建 Azure HDInsight Hadoop 叢集、 變更 Hadoop 使用者密碼，以及啟用遠端桌面通訊協定 (RDP)，以便存取叢集上的 Hadoop 命令主控台。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md)

## 用來管理 HDInsight 的其他工具
除了 Azure 傳統入口網站以外，還有其他工具可以用來管理 HDInsight。

- 如需有關如何使用 Azure PowerShell 管理 HDInsight 的詳細資訊，請參閱 [HDInsight 使用 Azure PowerShell 來管理](hdinsight-administer-use-powershell.md)。

- 如需使用 Azure CLI 管理 HDInsight 的詳細資訊，請參閱 [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)。

##先決條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure 儲存體帳戶** -HDInsight 叢集使用 Azure Blob 儲存體容器做為預設檔案系統。 如需 Azure Blob 儲存體如何提供順暢 HDInsight 叢集使用體驗的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。 如需建立 Azure 儲存體帳戶的詳細資訊，請參閱 [如何建立儲存體帳戶](../storage-create-storage-account.md)。


##佈建 HDInsight 叢集

您可以使用 [快速建立] 或 [自訂建立] 選項，從 Azure 傳統入口網站佈建 HDInsight 叢集。 如需相關指示的連結，請參閱：

- [使用快速建立佈建叢集](../hdinsight-get-started.md#provision)
- [使用自訂建立佈建叢集](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##自訂 HDInsight 叢集

HDInsight 可以與很多 Hadoop 元件搭配使用。 如需已驗證和支援的元件清單，請參閱 [Azure HDInsight 是 Hadoop 的什麼版本](hdinsight-component-versioning.md)。 您可以使用下列其中一個選項來自訂 HDInsight：

- 使用 [指令碼動作] 來執行可自訂叢集的自訂指令碼，以變更叢集組態或安裝自訂元件 (如 Giraph 或 Solr)。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。
- 在叢集佈建期間，使用 HDInsight .NET SDK 或 Azure PowerShell 中的叢集自訂參數。 即會在叢集的存留期保留這些組態變更，而且它們不受叢集節點重新製作映像的影響，而 Azure 平台會定期執行重新製作映像以進行維護。 如需有關如何使用叢集自訂參數的詳細資訊，請參閱 [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。
- 您可以使用 JAR 檔案形式在叢集上執行一些原生 Java 元件 (例如 Mahout 和 Cascading)。 這些 JAR 檔案可以配送至 Azure Blob 儲存體，並透過 Hadoop 工作提交機制提交至 HDInsight 叢集。 如需詳細資訊，請參閱 [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。


    >[AZURE.NOTE] If you have issues deploying JAR files to HDInsight clusters or calling JAR files on HDInsight clusters, contact [Microsoft Support](http://azure.microsoft.com/support/options/).

    > Cascading is not supported by HDInsight, and is not eligible for Microsoft Support. For lists of supported components, see [What's new in the cluster versions provided by HDInsight?](hdinsight-component-versioning.md).


不支援使用遠端桌面連線在叢集上安裝自訂軟體。 您應該避免在前端節點的磁碟機上儲存任何檔案，因為這些檔案會在您需要重新建立叢集時遺失。 建議您將檔案儲存至 Azure Blob 儲存體。 Blob 儲存體是持續性的。

##變更 HDInsight 叢集使用者名稱和密碼
HDInsight 叢集可以有兩個使用者帳戶。 HDInsight 叢集使用者帳戶是在佈建程序期間所建立。 您也可以建立 RDP 使用者帳戶，以透過 RDP 來存取叢集。 請參閱 [啟用遠端桌面](#connect-to-hdinsight-clusters-by-using-rdp)。

**變更 HDInsight 叢集使用者名稱和密碼**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下 [ **HDINSIGHT** 在左窗格中。 您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要重設使用者名稱和密碼的 HDInsight 叢集。
4. 從頁面頂端，按一下 [ **組態**。
5. 按一下 [ **OFF** 旁 **HADOOP 服務**。
6. 按一下 [ **儲存** 底部的頁面上，並等待停用完成。
7. 停用服務之後，請按一下 **ON** 旁 **HADOOP 服務**。
8. 如 **使用者名稱** 和 **新密碼**, ，輸入新的使用者名稱和密碼 (分別) 叢集。
8. 按一下 [ **儲存**。


##使用 RDP 連線到 HDInsight 叢集

您在建立叢集時所提供的叢集認證可以存取叢集上的服務，但是無法透過遠端桌面來存取叢集本身。 預設會關閉遠端桌面存取，因此，如果使用它直接存取叢集，則需要進行一些額外的後續建立組態。

**啟用遠端桌面**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下 [ **HDINSIGHT** 在左窗格中。 您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
4. 從頁面頂端，按一下 [ **組態**。
5. 從頁面底部，按一下 [ **啟用遠端**。
6. 在 **設定遠端桌面** 精靈] 中，輸入遠端桌面使用者名稱和密碼。 請注意，必須不同於用來建立叢集的使用者名稱 (**admin** 依預設，使用 [快速建立] 選項)。 輸入到期日在 **到期日** 方塊。 請注意，到期日必須是未來日期，而且最多是從現在算起的 90 天。 預設到期時間假設為所指定日期的午夜。 然後按一下核取圖示。

    ![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE] 您也可以使用 HDInsight.NET SDK 在叢集上啟用遠端桌面。 使用 **EnableRdp** 以下列方式在 HDInsight 用戶端物件上的方法: **用戶端。EnableRdp (clustername，location，"rdpuser"，"rdppassword"，DateTime.Now.AddDays(6))**。 同樣地，若要在叢集上，停用遠端桌面，您可以使用 **用戶端。DisableRdp (clustername，location)**。 如需有關這些方法的詳細資訊，請參閱 [HDInsight.NET SDK 參考](http://go.microsoft.com/fwlink/?LinkId=529017)。 這僅適用於在 Windows 上執行的 HDInsight 叢集。



> [AZURE.NOTE] 一旦啟用叢集的 RDP，您必須更新網頁，才能連線到叢集。

**使用 RDP 連線到叢集**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下 [ **HDINSIGHT** 在左窗格中。 您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
4. 從頁面頂端，按一下 [ **組態**。
5. 按一下 [ **連接**, ，並依照指示進行。

##建立自我簽署憑證

如果您要在叢集上使用 .NET SDK 執行任何作業，您必須在工作站建立自我簽署憑證，並將憑證上傳至 Azure 訂用帳戶。 此工作只需要執行一次。 只要憑證有效，您可以將相同的憑證安裝在其他電腦上。

**建立自我簽署憑證**

1. 建立用來驗證要求的自我簽署憑證。 您可以使用網際網路資訊服務 (IIS) 或 [makecert]( http://go.microsoft.com/fwlink/?LinkId=534000) 來建立憑證。

2. 瀏覽至憑證的位置，以滑鼠右鍵按一下憑證，按一下 **安裝憑證**, ，並將憑證安裝到電腦的個人存放區。 編輯憑證屬性，並為它指派一個更容易記住的名稱。

3. 將憑證匯入 Azure 傳統入口網站。 從入口網站中，按一下 [ **設定** 左下角，然後按一下頁面上，以及 **管理憑證**。 從頁面底部，按一下 [ **上載** 並遵循指示，將.cer 檔案上傳您在上一個步驟中建立。

    ![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##授與/撤銷 HTTP 服務存取

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

預設會授與這些服務的存取權。 您可以從 Azure 傳統入口網站撤銷/授與存取權。

>[AZURE.NOTE] 透過授與/撤銷存取權，您將會重設叢集使用者名稱和密碼。

**授與/撤銷 HTTP Web 服務存取**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下 [ **HDINSIGHT** 在左窗格中。 您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要設定的 HDInsight 叢集。
4. 從頁面頂端，按一下 [ **組態**。
5. 按一下 [ **ON** 或 **OFF** 旁 **HADOOP 服務**。
6. 如 **使用者名稱** 和 **新密碼**, ，輸入新的使用者名稱和密碼 (分別) 叢集。
7. 按一下 [ **儲存**。

請參閱 [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。

##開啟 Hadoop 命令列

若要使用遠端桌面連線到叢集，並使用 Hadoop 命令列，則您必須先啟用對叢集的遠端桌面存取 (如上一節所述)。

**開啟 Hadoop 命令列**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下 [ **HDINSIGHT** 在左窗格中。 將出現已部署的 Hadoop 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
3. 按一下 [ **組態** 頁面頂端。
4. 按一下 [ **連接** 頁面的底部。
5. 按一下 [ **開啟**。
6. 輸入您的認證，然後按一下 **確定**。 請使用您在建立叢集時所設定的使用者名稱和密碼。
7. 按一下 [ **是**。
8. 從桌面上，按兩下 **Hadoop 命令列**。

    ![HDI.HadoopCommandLine][image-hadoopcommandline]


    For more information on Hadoop commands, see [Hadoop commands reference](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

在前一個螢幕擷取畫面上，資料夾名稱已內嵌 Hadoop 版本號碼。 版本號碼會根據叢集上所安裝的 Hadoop 元件版本而變更。 您可以使用 Hadoop 環境變數來參照那些資料夾。 例如：

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

##調整叢集
請參閱 [在 HDInsight 中調整 Hadoop 叢集](hdinsight-hadoop-cluster-scaling.md)。

##後續步驟
透過本文，您已了解如何使用 Azure 傳統入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。 若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 使用者入門](../hdinsight-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal-v1/hdi.createrdpuser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal-v1/hdinsight-hadoop-command-line.png "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal-v1/hdi.clustercreate.uploadcert.png

