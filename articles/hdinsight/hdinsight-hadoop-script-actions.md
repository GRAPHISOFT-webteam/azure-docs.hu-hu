<properties
    pageTitle="使用 HDInsight 開發指令碼動作| Microsoft Azure"
    description="了解如何使用指令碼動作來自訂 Hadoop 叢集。"
    services="hdinsight"
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
    ms.date="11/09/2015"
    ms.author="jgao"/>

# 開發 HDInsight 的指令碼動作指令碼

了解如何寫入 HDInsight 的指令碼動作指令碼 如需使用指令碼動作指令碼，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。 HDInsight 叢集上 Linux 作業系統寫入相同的文章，請參閱 [hdinsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)。

指令碼動作可用來安裝其他在 Hadoop 叢集上執行的軟體，或變更叢集上所安裝應用程式的組態。 指令碼動作是在部署 HDInsight 叢集時，在叢集節點上執行的指令碼，一旦叢集中的節點完成 HDInsight 組態之後，就會執行這些指令碼動作。 指令碼動作是依據系統管理員帳戶的權限來執行，並具有叢集節點的完整存取權限。 您可對每個叢集提供一份依其指定順序來執行的指令碼動作清單。 

> [AZURE.NOTE] 如果您遇到下列錯誤訊息 ︰ 
> 
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> 這是因為您沒有包括協助程式方法。  請參閱 [Helper 方法的自訂指令碼](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts)。

## 範例指令碼

對於在 Windows 作業系統上佈建 HDInsight 叢集，指令碼動作是 Azure PowerShell 指令碼。以下是設定網站組態檔的範例指令碼：

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

此指令碼採用四個參數，組態檔名稱、您想要修改的屬性、您要設定的值以及描述。 例如：

    hive-site.xml hive.metastore.client.socket.timeout 90 

這些參數會在 hive-site.xml 檔案中將 hive.metastore.client.socket.timeout 值設定為 90。  預設值為 60 秒。

此範例指令碼也可以在找到 [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1)。 

HDInsight 提供數個指令碼在 HDInsight 叢集上安裝其他元件：

名稱 | 指令碼
----- | -----
**安裝 Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 請參閱 [HDInsight 上的 Spark 叢集的安裝和使用][hdinsight-install-spark]。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 請參閱 [安裝和使用 R HDInsight 叢集上的][hdinsight-r-scripts]。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 請參閱 [安裝和使用 HDInsight 叢集上 Solr](hdinsight-hadoop-solr-install.md)。
- **安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 請參閱 [Giraph 的 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-giraph-install.md)。

您可以從 Azure 入口網站、Azure PowerShell 或使用 HDInsight .NET SDK 來部署「指令碼動作」。  如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。

> [AZURE.NOTE] 範例指令碼工作只能與 HDInsight 叢集 3.1 版或更高。 如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](../hdinsight-component-versioning/)。





## 自訂指令碼的協助程式方法

指令碼動作協助程式方法是您在撰寫字訂指令碼時可以使用的公用程式。 定義在 [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1), ，而且可以包含在指令碼，使用下列命令 ︰

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);
    
    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    { 
        Import-Module $CONFIGACTIONMODULE;
    } 
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

以下是這個指令碼提供的協助程式方法：

協助程式方法 | 說明
-------------- | -----------
**Save-HDIFile** | 從指定的統一資源識別項 (URI) 將檔案下載到與指派給叢集的 Azure VM 節點相關聯的本機磁碟位置。
**Expand-HDIZippedFile** | 將壓縮檔解壓縮。
**Invoke-HDICmdScript** | 從 cmd.exe 執行指令碼。
**Write-HDILog** | 撰寫用於指令碼動作之自訂指令碼的輸出。
**Get-Services** | 取得執行指令碼的機器上所執行之服務的清單。
**Get-Service** | 輸入特定服務名稱，來取得執行指令碼的機器上特定服務的詳細資訊 (服務名稱、處理序識別碼、狀態等)。
**Get-HDIServices** | 取得執行指令碼的電腦上所執行之 HDInsight 服務的清單。
**Get-HDIService** | 輸入特定 HDInsight 服務名稱，來取得執行指令碼的機器上特定服務的詳細資訊 (服務名稱、處理序識別碼、狀態等)。
**Get-ServicesRunning** | 取得執行指令碼的電腦上所執行之服務的清單。
**Get-ServiceRunning** | 檢查執行指令碼的電腦上是否在執行特定服務 (依名稱)。
**Get-HDIServicesRunning** | 取得執行指令碼的電腦上所執行之 HDInsight 服務的清單。
**Get-HDIServiceRunning** | 檢查執行指令碼的電腦上是否在執行特定 HDInsight 服務 (依名稱)。
**Get-HDIHadoopVersion** | 取得執行指令碼的電腦上所安裝的 Hadoop 版本。
**Test-IsHDIHeadNode** | 檢查執行指令碼的電腦是否為前端節點。
**Test-IsActiveHDIHeadNode** | 檢查執行指令碼的電腦是否為作用中前端節點。
**Test-IsHDIDataNode** | 檢查執行指令碼的電腦是否為資料節點。
**Edit-HDIConfigFile** | 編輯組態檔 hive-site.xml、core-site.xml、hdfs-site.xml、mapred-site.xml 或 yarn-site.xml。


## 指令碼開發的最佳做法

為 HDInsight 叢集開發自訂的指令碼時，有數個最佳做法需要牢記在心：

- 檢查 Hadoop 版本

    只有 HDInsight 3.1 版 (Hadoop 2.4) 和以上版本才支援使用指令碼動作在叢集上安裝自訂元件。 在自訂指令碼，您必須使用 **Get-hdihadoopversion** helper 方法，可檢查 Hadoop 的版本，再繼續執行其他工作的指令碼中。


- 提供穩定的指令碼資源連結

    使用者應該確定在叢集的整個存留期間，於叢集自訂中使用的所有指令碼及其他成品都保持可用，並且這些檔案的版本在此持續時間內不會變更。 如需為叢集中的節點重新製作映像，就必須有這些資源。 最佳做法是下載並封存使用者所控制之儲存體帳戶中的所有項目。 這可以是預設的儲存體帳戶，或是在部署時為自訂叢集指定的任何其他儲存體帳戶。
    Spark 和 R 叢集範例中所提供自訂文件，比方說，我們有資源的本機複本中此儲存體帳戶 ︰ https://hdiconfigactions.blob.core.windows.net/。


- 確保叢集自訂指令碼具有等冪性

    您必須預期在叢集存留期間將會為 HDInsight 叢集的節點重新製作映像。 每當重新製作叢集映像時，都會執行叢集自訂指令碼。 此指令碼必須設計成具有等冪性，意思就是在重新製作映像時，此指令碼應該確保叢集會回到與當初建立叢集時，指令碼剛剛第一次執行後相同的自訂狀態。 例如，如果自訂指令碼在第一次執行時，在 D:\AppLocation 中安裝了某個應用程式，則在後續每次的執行中，當重新製作映像時，此指令碼應該先檢查 D:\AppLocation 位置中是否有該應用程式，再繼續執行指令碼中的其他步驟。


- 在最佳位置安裝自訂元件

    重新製作叢集節點映像時，C:\ 資源磁碟機和 D:\ 系統磁碟機可能被重新格式化，而導致資料及安裝在這些磁碟機上的應用程式遺失。 如果隸屬於叢集的 Azure 虛擬機器 (VM) 節點故障，而被新節點取代時，也可能發生這種情況。 您可以將元件安裝在 D:\ 磁碟機上，或叢集上的 C:\apps 位置中。 C:\ 磁碟機上的所有其他位置則已預留他用。 請在叢集自訂指令碼中指定要用來安裝應用程式或程式庫的位置。


- 確保叢集架構具有高可用性

    為了獲得高可用性，HDInsight 具備主動/被動架構，在此架構中，有一個處於使用中模式的前端節點 (此節點正在執行 HDInsight 服務)，以及另一個處於待命模式的前端節點 (此節點未執行 HDInsight 服務)。 如果 HDInsight 服務中斷，這兩個節點就會切換主動和被動模式。 如果為了獲得高可用性，而使用指令碼動作在這兩個前端節點安裝服務，請注意 HDInsight 的容錯移轉機制並無法自動容錯移轉這些由使用者所安裝的服務。 因此使用者在 HDInsight 前端節點上所安裝的服務若想要有高可用性，則必須有自己的主動/被動模式時的容錯移轉機制，或是處於主動/被動模式。

    當指定的前端節點角色中的值做為 HDInsight 指令碼動作命令會執行兩個前端節點上 *ClusterRoleCollection* 參數 (記載於下面的區段 [如何執行指令碼動作](#runScriptAction))。 因此，當您設計自訂指令碼時，請確定您的指令碼知道這項設定。 您不應該發生在這兩個前端節點上安裝並啟動相同的服務，而最終導致彼此競爭的問題。 此外也請注意，重新製作映像時，資料將會遺失，因此透過指令碼動作所安裝的軟體必須要能夠從這類事件復原。 應用程式在設計上應該要能夠與分散在眾多節點上的高可用性資料搭配運作。 請注意，最多可同時為叢集中 1/5 的節點重新製作映像。


- 設定自訂元件來使用 Azure Blob 儲存體

    您安裝在叢集節點上的自訂元件可能有使用 Hadoop 分散式檔案系統 (HDFS) 儲存體的預設組態。 您應該變更此組態，使其改為使用 Azure Blob 儲存體。 在重新製作叢集映像時，會格式化 HDFS 檔案系統，因此您會遺失儲存在其中的所有資料。 改用 Azure Blob 儲存體可確保資料保留下來。

## 常見使用模式

本節指引您如何實作某些撰寫自訂指令碼時可能遇到的常見使用模式。

### 設定環境變數

在開發指令碼動作時，您往往會感到需要設定環境變數。 例如，最可能的案例是當您從外部網站下載二進位檔，將它安裝在叢集上，然後將其安裝位置加入 ‘PATH’ 環境變數中。 下列程式碼片段顯示如何在自訂指令碼中設定環境變數。

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

此陳述式會設定環境變數 **MDS_RUNNER_CUSTOM_CLUSTER** 的值 'true' 並將這個變數來為整個機器的範圍。 有時候您必須將環境變數設定在適當範圍 – 機器或使用者。 請參閱 [這裡][1] 如需有關設定環境變數。

### 存取自訂指令碼儲存所在位置

用來自訂叢集的指令碼必須位於叢集的預設儲存體帳戶，或是位於其他任何儲存體帳戶上的公用唯讀容器。 如果指令碼存取位於他處的資源，則這些資源必須是可公開存取的資源 (至少是公用唯讀狀態)。 例如，您可能會想要用 SaveFile-HDI 命令存取檔案並加以儲存。

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

在此範例中，您必須確定儲存體帳戶 'somestorageaccount' 中的容器 'somecontainer' 可公開存取。 否則，指令碼將會擲回「找不到」例外狀況而且失敗。

### 傳遞參數到 Add-AzureRmHDInsightScriptAction Cmdlet

若要將多個參數傳遞至 Add-AzureRmHDInsightScriptAction cmdlet，您必須先格式化字串值以包含指令碼的所有參數。 例如：

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"
 
或

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### 對於失敗的叢集部署擲回例外狀況

如果您想要在叢集自訂程序未如預料般成功時準確收到通知，請務必擲回例外狀況並將叢集佈建作業判定為失敗。 例如，您可能會想要在檔案存在時予以進行處理，並處理檔案不存在時的錯誤狀況。 此做法可確保指令碼正常結束，並正確得知叢集的狀態。 下列程式碼片段示範如何實現此一目的：

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

在此程式碼片段中，如果檔案不存在，則會導致指令碼在列印錯誤訊息後確實正常結束的狀態，而叢集會達到執行中狀態 (前提是它「成功」完成叢集自訂程序)。 如果您要精確地獲知叢集自訂作業因為遺漏檔案而未如預期般成功的事實，就更適合擲回例外狀況並使叢集自訂步驟失敗。 若要達到這個目的，您必須改用下列範例程式碼片段。

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## 指令碼動作的部署檢查清單
以下是我們在準備部署這些指令碼時所採取的步驟：

1. 將包含自訂指令碼的檔案放在叢集節點可於部署期間存取的位置。 此位置可以是部署叢集時所指定的預設或其他儲存體帳戶，或是其他任何可公開存取的儲存體容器。
2. 在指令碼中加入檢查以確定它們以等冪方式執行，使得指令碼可以在相同的節點上執行多次。
3. 使用 **Write-output** Azure PowerShell cmdlet 來列印至 STDOUT 以及 STDERR。 請勿使用 **Write-host**。
4. 使用暫存檔案資料夾 (例如 $env:TEMP) 來存放指令碼所使用的下載檔案，然後在執行完指令碼之後將這些檔案清除。
5. 安裝自訂軟體，位置只能是 D:\ 或 C:\apps。 不應該使用 C: 磁碟機上的其他位置，因為它們已預留他用。 請注意，如果將檔案安裝在 C: 磁碟機上 C:\apps 資料夾以外的位置，可能會導致在重新製作節點映像期間設定失敗。
6. 如果作業系統層級設定或 Hadoop 服務組態檔已變更，您可能會想要重新啟動 HDInsight 服務，讓它們可以載入任何作業系統層級設定，例如指令碼中設定的環境變數。



## 使用 HDInsight Emulator 測試自訂指令碼

在將自訂指令碼用於 HDInsight 指令碼動作命令前若要先進行測試，最簡單的方式是在 HDInsight Emulator 中執行看看。 您可以將 HDInsight Emulator 安裝在本機上、安裝在 Azure 基礎結構即服務 (IaaS) Windows Server 2012 R2 VM 上，或安裝在本機機器上，然後看看指令碼執行起來是否正確。 但請注意，Windows Server 2012 R2 VM 是 HDInsight 用於其節點的相同 VM。

本節概述的是在本機使用 HDInsight Emulator 進行測試的程序，但與使用 VM 的程序是類似的。

**安裝 HDInsight Emulator** -若要執行指令碼動作在本機，您必須安裝 HDInsight Emulator。 如需如何安裝指示，請參閱 [開始使用 HDInsight Emulator](../hdinsight-get-started-emulator/)。

**設定 Azure PowerShell 執行原則** -開啟 Azure PowerShell 並 （以系統管理員） 執行下列命令，將執行原則設為 *LocalMachine* 以及 *不受限制*:

    Set-ExecutionPolicy Unrestricted –Scope LocalMachine

指令碼未經過簽署，因此需要讓此原則不受限制。

**下載指令碼動作** 您想要執行以便在本機目的地。 下列範例指令碼可從下列位置中下載：

* **Spark**。 https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**. https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**。 https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**。 https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**執行指令碼動作** -系統管理員模式開啟新的 Azure PowerShell 視窗，並從本機儲存的位置執行 Spark 或 R 安裝指令碼。

**使用方式範例**
當您使用 Spark 和 R 叢集時，可能不存在，HDInsight Emulator 中所需的資料檔案。 因此您可能需要將含有資料的相關 .txt 檔案上傳到 HDFS 中的路徑，然後使用該路徑存取資料。 例如：

    val file = sc.textFile("/example/data/gutenberg/davinci.txt")


請注意，在某些情況下，自訂指令碼可能會實際依存於 HDInsight 元件，例如偵測特定 Hadoop 服務是否已啟動。 在此情況下，您將必須把自訂指令碼部署到實際的 HDInsight 叢集，以測試這些自訂指令碼。


## 偵錯自訂指令碼

指令碼錯誤記錄檔會與其他輸出一起儲存在您建立叢集時為其指定的預設儲存體帳戶中。 記錄會儲存在資料表名稱 *u < \cluster-name-fragment >< \time-stamp > u<\cluster-name-fragment><\time-stamp>setuplog*。 這些是彙總的記錄檔，包含來自指令碼執行所在之所有叢集節點 (前端節點和背景工作節點) 的記錄。
檢查記錄檔的簡單方法是使用 HDInsight Tools for Visual Studio。 安裝工具，請參閱 [開始使用 HDInsight 的 Visual Studio Hadoop 工具](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**使用 Visual Studio 檢查記錄檔**

1. 開啟 Visual Studio。
2. 按一下 [ **檢視**, ，然後按一下 [ **伺服器總管**。
3. 以滑鼠右鍵按一下 「 Azure 」 中，按一下 [連線到 **Microsoft Azure 訂閱**, ，然後輸入您的認證。
4. 展開 **儲存體**, ，做為預設檔案系統的 Azure 儲存體帳戶依序展開 **資料表**, ，然後按兩下資料表名稱。


您也可以遠端登入到叢集節點以查看 STDOUT 和 STDERR 中的自訂指令碼。 每個節點上的記錄檔都只與該節點，並且會記錄到 **C:\HDInsightLogs\DeploymentAgent.log**。 這些記錄檔會記錄自訂指令碼的所有輸出。 Spark 指令碼動作的範例記錄程式碼片段看起來像這樣：

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


在此記錄中，清楚指出已經在名為 HEADNODE0 的 VM 上執行 Spark 指令碼動作，並且在執行期間沒有擲回任何例外狀況。

如果發生執行失敗的情況，描述它的輸出也會包含在這個記錄檔中。 對可能發生的指令碼問題進行偵錯時，這些記錄檔中提供的資訊應該很有幫助。


## 另請參閱

- [使用指令碼動作來自訂 HDInsight 叢集][hdinsight-cluster-customize]
- [在 HDInsight 叢集上安裝及使用 Spark][hdinsight-install-spark]
- [在 HDInsight 叢集上安裝及使用 R][hdinsight-r-scripts]
- [安裝及使用 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install.md)。
- [安裝及使用 Giraph HDInsight 叢集上](hdinsight-hadoop-giraph-install.md)。

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx


