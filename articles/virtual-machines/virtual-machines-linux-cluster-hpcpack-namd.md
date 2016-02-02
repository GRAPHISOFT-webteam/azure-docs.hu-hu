<properties
 pageTitle="Linux VM 上的 NAMD 與 Microsoft HPC Pack | Microsoft Azure"
 description="在 Azure 上部署 Microsoft HPC Pack 叢集並且執行在多個 Linux 運算節點上具有 charmrun 的 NAMD 模擬。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>


# 在 Azure 中的 Linux 運算節點以 Microsoft HPC Pack 執行 NAMD

本文將說明如何部署在 Azure 上的 Microsoft HPC Pack 叢集使用多個 Linux 運算節點和執行 [NAMD](http://www.ks.uiuc.edu/Research/namd/) 工作 **charmrun** 計算和視覺化大型 biomolecular 系統的結構。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。



NAMD (適用於奈米分子動力程式) 是專為高效能模擬大型生物分子系統而設計的平行分子動力套件，包含多達數百萬個原子，例如病毒、細胞結構和大蛋白。 NAMD 會針對典型模擬縮放到數百個核心，以及針對最大型的模擬縮放至超過 500,000 個核心。

Microsoft HPC Pack 提供功能來執行各種大規模 HPC 和平行應用程式，包括 Microsoft Azure 虛擬機器的叢集上的 MPI 應用程式。 從 Microsoft HPC Pack 2012 R2 Update 2 開始，HPC Pack 也支援在部署於 HPC Pack 叢集中的 Linux 計算節點 VM 上執行 Linux HPC 應用程式。 請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster-hpcpack.md) 的簡介。


## 必要條件

* **HPC Pack 叢集使用 Linux 運算節點** -請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster-hpcpack.md) 的先決條件及步驟來部署 linux HPC Pack 叢集計算節點，在 Azure 上的使用 Azure Marketplace 中的 PowerShell 指令碼和 HPC Pack 的映像。

    以下是範例 XML 組態檔，您可以與指令碼搭配使用，以部署 Azure 架構 HPC Pack 叢集，其中包含 Windows Server 2012 R2 前端節點和 4 個大型 (A3) CentOS 6.6 運算節點。 請將您的訂用帳戶和服務名稱取代為適當的值。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>訂用帳戶 1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>美國西部</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>子網路 1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>大型</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN %00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>大型</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic CentOS-66 20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```


* **NAMD software and tutorial files** - Download NAMD software for Linux from the [NAMD](http://www.ks.uiuc.edu/Research/namd/) site. This article is based on NAMD version 2.10, and uses the [Linux-x86_64 (64-bit Intel/AMD with Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) archive, which you'll use to run NAMD on multiple Linux compute nodes in a cluster network. Also download the [NAMD tutorial files](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Follow the instructions later in this article to extract the archive and the tutorial samples on the cluster head node.

* **VMD** (optional) - To see the results of your NAMD job, download and install the molecular visualization program [VMD](http://www.ks.uiuc.edu/Research/vmd/) on a computer of your choice. The current version is 1.9.2. See the VMD download site to get started.  


## Set up mutual trust between compute nodes
Running a cross-node job on multiple Linux nodes requires the nodes to trust each other (by **rsh** or **ssh**). When you create the HPC Pack cluster with the Microsoft HPC Pack IaaS deployment script, the script automatically sets up permanent mutual trust for the administrator account you specify. For non-administrator users you create in the cluster's domain, you have to set up temporary mutual trust among the nodes when a job is allocated to them, and destroy the relationship after the job is complete. To do this for each user, provide an RSA key pair to the cluster which HPC Pack uses to establish the trust relationship.

### Generate an RSA key pair
It's easy to generate an RSA key pair, which contains a public key and a private key, by running the Linux **ssh-keygen** command.

1.  Log on to a Linux computer.

2.  Run the following command.
```
    ssh-keygen -t rsa
    ```
    
    >[AZURE.NOTE] Press **Enter** to use the default settings until the command is completed. Do not enter a passphrase here; when prompted for a password, just press **Enter**.
    
    ![Generate an RSA key pair][keygen]

3.  將目錄變更為 ~/.ssh 目錄。 私密金鑰會儲存在 id_rsa，而公開金鑰會儲存在 id_rsa.pub。

    ![私密和公開金鑰][keys]

### 將金鑰組新增至 HPC Pack 叢集

1.  使用您的 HPC Pack 系統管理員帳戶與您的前端節點進行遠端桌面連線 (當您執行部署指令碼時設定的系統管理員帳戶)。

2. 您可以使用標準的 Windows Server 程序在叢集的 Active Directory 網域中建立網域使用者帳戶。 例如，在前端節點上使用 Active Directory 使用者和電腦工具。 本文中的範例假設您建立名為 hpclab\hpcuser 的網域使用者。

2.  建立名為 C:\cred.xml 的檔案，並且將 RSA 金鑰資料複製到其中。 您可以在本文結尾處的範例檔案中找到範例。

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  開啟命令提示字元並輸入下列命令，以設定 hpclab\hpcuser 帳戶的認證資料。 您使用 **extendeddata** 參數來傳遞您針對金鑰資料建立之 C:\cred.xml 檔案的名稱。

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    這個命令會成功完成而沒有輸出。 為您執行工作所需的使用者帳戶設定認證之後，將 cred.xml 檔案儲存在安全的位置，或將它刪除。

5.  如果您在其中一個 Linux 節點上產生 RSA 金鑰組，請記得在您完成使用後刪除金鑰。 如果 HPC Pack 找到現有的 id_rsa 檔案或 id_rsa.pub 檔案，則它不會設定相互信任。

>[AZURE.IMPORTANT] 我們不建議以共用叢集上的叢集系統管理員身分執行 Linux 工作，因為系統管理員提交的工作會在 Linux 節點上的根帳戶底下執行。 非系統管理員使用者所提交的工作會在具有與工作使用者相同名稱的本機 Linux 使用者帳戶下執行，HPC Pack 會跨配置給工作的所有節點，為此 Linux 使用者設定相互信任。 您可以在執行工作之前，手動在 Linux 節點上設定 Linux 使用者，否則 HPC Pack 會在工作提交時自動建立使用者。 如果 HPC Pack 建立使用者，HPC Pack 會在工作完成之後刪除它。 金鑰會在工作完成之後於節點上移除，以降低安全性威脅。

## 為 Linux 節點設定檔案共用

現在在前端節點上的資料夾設定標準 SMB 共用，並且在所有 Linux 節點上掛接共用資料夾，以允許 Linux 節點存取具有共用路徑的 NAMD 檔案。 檔案共用的項目及中的步驟，請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster-hpcpack.md)。 (我們建議您在本文中的前端節點上掛接共用資料夾，因為 CentOS 6.6 Linux 節點目前不支援 Azure 檔案服務，該服務提供類似的功能。 如需詳細資訊，裝載 Azure 檔案共用，請參閱 [Persisting 與 Microsoft Azure 檔案的連線](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).)

1.  在前端節點上建立資料夾，並藉由設定讀取/寫入權限與每個人共用。 在此範例中，\\\CentOS66HN\Namd 是資料夾的 CentOS66HN 是資料夾的前端節點的主機名稱名稱。

2. 在資料夾中解壓縮 NAMD 檔案，方法是使用 Windows 的 **tar** 版本，或其他可以操作 .tar 封存的 Windows 公用程式。 NAMD tar 封存檔解壓縮到 \\\CentOS66HN\Namd\namd2，並擷取下 \\\CentOS66HN\Namd\namd2\namdsample 教學課程檔案。

2.  開啟 Windows PowerShell 視窗並執行下列命令來裝載共用資料夾。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /namd2 的資料夾。 第二個命令會將共用資料夾 //CentOS66HN/Namd/namd2 掛接至此資料夾，其 dir_mode 和 file_mode 位元設為 777。 命令中的「使用者名稱」**和「密碼」**應該是前端節點上使用者的認證。
>[AZURE.NOTE]「 \ 」 「 第二個命令中的符號是 powershell 的逸出符號。 「 \'，"表示"，"(逗號) 是命令的一部分。


## 準備執行 NAMD 工作

 NAMD 工作需要 *nodelist* 檔案 **charmrun** 知道啟動 NAMD 程序時要使用的節點數目。 您會撰寫 Bash 指令碼，它會產生節點清單檔案，並且搭配此節點清單檔案執行 **charmrun**。 然後您就可以在會呼叫此指令碼的 HPC 叢集管理員中提交 NAMD 工作。

### 環境變數和節點清單檔案

$CCP_NODES_CORES 環境變數中關於節點和核心的資訊，當工作啟動時，HPC Pack 前端節點會自動設定該資訊。 $CCP_NODES_CORES 變數的格式如下所示：

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

這會列出節點總數、節點名稱和配置給工作之每個節點上的核心數目。 例如，如果工作需要 10 個核心以執行，$CCP_NODES_CORES 的值會類似：

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

以下是指令碼將會產生的節點清單檔案中的資訊：

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

例如：

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### 用以建立節點清單檔案的 Bash 指令碼

使用您選擇的文字編輯器，在包含 NAMD 程式檔案的資料夾中建立下列的 Bash 指令碼，並將它命名為 hpccharmrun.sh。 本文結尾處的範例檔案中有完整的範例。 這個 bash 指令碼會執行下列作業。
>[AZURE.TIP] 將您的指令碼儲存為具有 Linux 行尾結束符號 (只有 LF，不是 CR LF) 的文字檔案。 這可確保它在 Linux 節點上正常運作。

1.  定義一些變數。

    ```
    #!/bin/bash
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  從環境變數取得節點的資訊。 $NODESCORES 儲存從 $CCP_NODES_CORES 分割單字的清單。 $COUNT 是 $NODESCORES 的大小。

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.  如果未設定 $CCP_NODES_CORES 變數，只要直接啟動 **charmrun** 即可。 (這應該只有當您在 Linux 節點上直接執行這個指令碼時才會發生。)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  或者為 **charmrun** 建立節點清單檔案。

```
else
    # 建立節點清單檔案
    NODELIST_PATH = ${SCRIPT_PATH} / $$ nodelist_

    # Write the head line
    echo "group main" > ${NODELIST_PATH}
    
    # Get every node name and number of cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

```
5.  Run **charmrun** with the nodelist file, get its return status, and remove the nodelist file at the end.

    ${CCP_NUMCPUS} is another environment variable set by the HPC Pack head node. It stores the number of total cores allocated to this job. We use it to specify the number of processes for charmrun.
```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    
    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi
    
    ```

6.  當 **charmrun** 傳回狀態時結束。

    ```
    exit ${RTNSTS}
    ```

## 提交 NAMD 工作

現在您已準備就緒在 HPC 叢集管理員中提交 NAMD 工作。

1.  連接至您的叢集前端節點並且啟動 HPC 叢集管理員。

2.  在 [節點管理]**** 中，確定 Linux 運算節點處於 [線上]**** 狀態。 如果不是，請選取它們然後按一下 [上線]****。

2.  在 [工作管理]**** 中，按一下 [新增工作]****。

3.  輸入工作的名稱，例如 *hpccharmrun*。

    ![新的 HPC 工作][namd_job]

4.  在 [工作詳細資料]**** 頁面的 [工作資源]**** 底下，選取 [節點]**** 做為資源類型，並且將 [最小值]**** 設為 3。 在此範例中，我們將在 3 個 Linux 節點上執行工作，每個節點有 4 個核心。

    ![工作資源][job_resources]

5.  在 [工作詳細資料和 I/O 重新導向]**** 頁面中，將新作業新增至工作並且設定下列值。

    * **命令列** -
`/namd2/hpccharmrun.sh + + 中遠端殼層 ssh/namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **工作目錄** - /namd2

    * **最小值** - 3

    ![作業詳細資料][task_details]
    >[AZURE.NOTE] 您在這裡設定工作目錄，因為 **charmrun** 嘗試瀏覽至每個節點上相同的工作目錄。 如果未設定工作目錄，HPC Pack 會在其中一個 Linux 節點上建立的隨機命名資料夾中啟動命令。 這將在其他節點上造成下列錯誤:
`/ bin/bash: 行 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: 沒有此類檔案或目錄。`若要避免這個問題，指定可存取的所有節點，和工作目錄的資料夾路徑。

5.  按一下 [提交]**** 以執行此工作。

    根據預設，HPC Pack 會以您目前登入的使用者帳戶提交工作。 對話方塊可能會在您按一下 [提交]**** 之後提示您輸入使用者名稱和密碼。

    ![工作認證][creds]

    在某些情況下 HPC Pack 會記住您以前輸入的使用者資訊，所以不會顯示此對話方塊。 若要讓 HPC Pack 再次顯示該對話方塊，在命令視窗中輸入下列命令，然後提交工作。

    ```
    hpccred delcreds
    ```

6.  工作需要數分鐘的時間才能完成。

7.  在作業記錄檔中看到 \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log 和輸出檔案 \\<headnode>\Namd\namd2\namdsample\1-2-sphere\。

8.  選擇性啟動 VMD 以檢視您的工作結果。 用來視覺化 NAMD 輸出檔案 (在此案例中，水圈中的泛素蛋白質分子) 的步驟已超出本文的範圍。 請參閱 [NAMD 教學課程](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) 如需詳細資訊。

    ![工作結果][vmd_view]

## 範例檔案

### 範例 hpccharmrun.sh 指令碼

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```


### 範例 cred.xml 檔案

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```






[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png 
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png 
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png 
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png 
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png 
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png 
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png 

