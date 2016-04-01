<properties
 pageTitle="Excel 和 SOA 適用的 HPC Pack 叢集 | Microsoft Azure"
 description="使用資源管理員部署模型，開始使用 HPC Pack 叢集以執行 Excel 和 SOA 工作負載。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="11/11/2015"
 ms.author="danlep"/>

# 開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載

本文將說明如何在 Azure 基礎結構服務 (IaaS) 上使用 Azure 快速入門範本或 Azure PowerShell 部署指令碼部署 HPC Pack 叢集。 您將使用 Azure Marketplace VM 映像，其設計目的為使用 HPC Pack 執行 Microsoft Excel 或服務導向架構 (SOA) 工作負載。 您可以從內部部署用戶端電腦使用叢集來執行簡單的 Excel HPC 和 SOA 服務。 Excel HPC 服務包括 Excel 活頁簿卸載和 Excel 使用者定義函數或 UDF。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


在較高層級上，下圖顯示您將建立的 HPC Pack 叢集。

![HPC 叢集與執行 Excel 工作負載的節點][scenario]

## 必要條件

* **用戶端電腦** -您必須執行 Azure PowerShell 叢集部署指令碼 （如果您選擇的部署方法），以及提交範例 Excel 和 SOA 工作至叢集的 Windows 用戶端電腦。

* **Azure 訂用帳戶** -如果您沒有帳戶，您可以建立免費試用帳戶只需要幾分鐘的時間。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* **核心配額** -您可能需要增加核心配額，特別是如果您選擇部署多核心 VM 大小的數個叢集節點。 如果您使用 Azure 快速入門範本，請注意資源管理員中的核心配額為每個 Azure 區域，您可能需要增加特定區域中的配額。 請參閱 [Azure 訂用帳戶限制、 配額和條件約束](../azure-subscription-service-limits.md)。 若要增加配額，您可以 [開啟線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。


## 步驟 1. 在 Azure 中設定 HPC Pack 叢集

我們將說明設定叢集的兩種方式：第一，使用 Azure 快速入門範本和 Azure 入口網站；第二，使用 Azure PowerShell 部署指令碼。


### 使用快速入門範本
使用 Azure 快速入門範本在 Azure 入口網站中快速、輕鬆地部署 HPC Pack 叢集。 在 Preview 入口網站中開啟範本時，您會看到一個可供您輸入叢集設定的簡單 UI。 步驟如下：

1. 請瀏覽 [GitHub 上的建立 HPC 叢集範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)。 您可根據意願檢視範本和原始碼的相關資訊。

2. 按一下 [ **部署至 Azure** Azure 入口網站中的範本開始進行部署。

    ![將範本部署到 Azure][github]

3. 在 Preview 入口網站中，依照下列步驟輸入 HPC 叢集範本的參數。

    a. 在 **編輯範本** 頁面上，按一下 **儲存**。

    ![儲存範本][template]

    b. 在 **參數** 頁面上，輸入範本參數的值。 (按一下說明資訊的每個設定旁邊的圖示。)下列畫面顯示範例值。 這個範例會建立名為的新 HPC Pack 叢集 *hpc01* 中 *hpc.local* 網域組成的前端節點和 2 的運算節點。 運算節點將會從 HPC Pack VM 映像建立，包括 Microsoft Excel。

    ![輸入參數][parameters]

    >[AZURE.NOTE]前端節點 VM 就會自動建立從 [最新 Marketplace 映像](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) 的 Windows Server 2012 R2 上的 HPC Pack 2012 R2。 目前此映像以 HPC Pack 2012 R2 Update 3 為基礎。
    >
    >運算節點 VM 會從選取之運算節點系列的最新映像建立。 選取 **ComputeNode** 的最新 HPC Pack 2012 R2 更新 3 選項運算用於一般用途的映像。 選取 **ComputeNodeWithExcel** 選項的最新的 HPC Pack 運算節點映像，包括 Microsoft Excel Professional Plus 2013 評估版。 如果您想要部署一般 SOA 工作階段或 Excel UDF 卸載的叢集，請選擇 **ComputeNode** 選項 （不需安裝 Excel）。
    >
    >當使用  **ComputeNodeWithExcel** 生產工作負載，您必須提供有效的 Excel 授權運算節點上啟動 Excel。 否則，Excel 評估版會在 30 天內到期，且執行 Excel 活頁簿會不斷失敗並出現 COMExeption (0x800AC472)。 如果發生這種情況，您可登入前端節點，透過 [HPC 叢集管理員] 主控台在所有 Excel 運算節點上 clusrun “%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe”，進而重設 Excel 的授權狀態以獲得另外 30 天的評估時間。 寬限期的重設授權狀態時間上限為 2，之後您可能需要提供有效的 Excel 授權。

    c. 選擇訂用帳戶。

    d. 建立新的資源群組的叢集，例如 *hpc01RG*。

    e. 選擇資源群組的位置，例如美國東部。

    f. 在 **法律條款** 頁面上，檢閱條款。 如果您同意，請按一下 [ **購買**。

    g. 當您完成設定範本的值，請按一下 **建立** 。

    ![建立叢集][create]

3.  當部署完成時 (通常需要約 30 分鐘)，從叢集前端節點匯出叢集憑證檔。 在稍後的步驟中，此公開憑證將在用戶端電腦上匯入以提供安全 HTTP 繫結的伺服器端驗證。

    a. 從 Azure 入口網站透過「遠端桌面」連線到前端節點。

     ![連接至前端節點][connect]

    b. 使用標準程序來使用憑證管理員匯出前端節點憑證 (位於 Cert: \LocalMachine\My 之下) 而不需私密金鑰。 在此範例中，將匯出 *CN = hpc01.eastus.cloudapp.azure.com*。

    ![匯出憑證][cert]

### 使用 HPC Pack IaaS 部署指令碼

HPC Pack IaaS 部署指令碼提供靈活的另一種方式部署 HPC Pack 叢集。 它會以 Azure 服務管理 (ASM) 模式執行，而範本會以 Azure 資源管理員 (ARM) 模式執行。 指令碼也和 Azure 全域或 Azure China 服務中的訂用帳戶相容。

**其他必要條件**

* **Azure PowerShell** - [安裝和設定 Azure PowerShell](../powershell-install-configure.md) (0.8.10 或更新版本) 用戶端電腦上。

* **HPC Pack IaaS 部署指令碼** -下載並解壓縮最新版的指令碼從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)。 執行 `New-HPCIaaSCluster.ps1 –Version` 以檢查指令碼的版本。 這篇文章根據 4.5.0 版或更新版本的指令碼。

**建立組態檔**

 HPC Pack IaaS 部署指令碼會使用 XML 組態檔做為輸入，可描述 HPC 叢集的基礎結構。 若要部署由一個前端節點和從包含 Microsoft Excel 之運算節點映像所建立的 18 個運算節點組成的叢集，請將環境的值取代為下列範例組態檔。 如需組態檔的詳細資訊，請參閱指令碼資料夾中的 Manual.rtf 檔案和 [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**組態檔的相關注意事項**

*  **VMName** 前端節點的 **必須** 能完全相同 **ServiceName**, 、 或 SOA 工作無法執行。

* 請確定您指定 **EnableWebPortal** 以便產生並匯出前端節點憑證。

* 後組態 PowerShell 指令碼 PostConfig.ps1 會在前端節點上進行某些設定，例如設定 Azure 儲存體連接字串、從前端節點移除運算節點角色，並在部署所有節點時使其上線。 隨後會出現範例指令碼。

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**執行指令碼**

1. 在用戶端電腦上以系統管理員身分開啟 PowerShell 主控台。

2. 將目錄變更為指令碼資料夾 (在本範例中為 E:\IaaSClusterScript)。

    ```
    cd E:\IaaSClusterScript
```

4. 執行下列命令來部署 HPC Pack 叢集。 這個範例假設組態檔位於 E:\HPCDemoConfig.xml。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

HPC Pack 部署指令碼將執行一段時間。 指令碼將會匯出和下載叢集憑證，並將它儲存在用戶端電腦上目前使用者的文件資料夾中。 指令碼將產生類似下方的訊息。 在下列步驟中，您將在適當的憑證存放區中匯入憑證。

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## 步驟 2. 卸載 Excel 活頁簿並從內部部署用戶端執行 UDF

### 卸載 Excel 活頁簿

遵循下列步驟來卸載要在 Azure 的 HPC Pack 叢集上執行的 Excel 活頁簿。 若要這樣做，您必須在用戶端電腦上安裝 Excel 2010 或 2013。

1. 使用步驟 1 中的下列其中一個方法來部署具有 Excel 運算節點映像的 HPC Pack 叢集。 取得叢集憑證檔 (.cer) 以及叢集的使用者名稱和密碼。

2. 在用戶端電腦上，在 Cert:\CurrentUser\Root 下匯入叢集憑證。

3. 請確定已安裝 Excel。 利用用戶端電腦上的 Excel.exe，建立具備相同資料夾中下列內容的  Excel.exe.config 檔案。 這樣可確保 HPC Pack 2012 R2 Excel COM 增益集順利載入。

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
</configuration>
```
4.  下載完整 [HPC Pack 2012 R2 更新 3 安裝](http://www.microsoft.com/download/details.aspx?id=49922) 並安裝 HPC Pack 用戶端
或下載並安裝 [HPC Pack 2012 R2 更新 3 用戶端公用程式](https://www.microsoft.com/download/details.aspx?id=49923) 和適當 Visual c + + 2010 可轉散發套件為您的電腦 ([x64](http://www.microsoft.com/download/details.aspx?id=14632), ，[x86](https://www.microsoft.com/download/details.aspx?id=5555))。

5.  在此範例中，我們使用名為 ConvertiblePricing_Complete.xlsb，可供下載的範例 Excel 活頁簿 [這裡](https://www.microsoft.com/en-us/download/details.aspx?id=2939)。

6.  將 Excel 活頁簿複製到工作資料夾，例如 D:\Excel\Run。

7.  開啟 Excel 活頁簿。 在 **開發** 功能區上，按一下 [ **COM 增益集** 和 HPC Pack Excel COM 增益集已載入成功，如下列螢幕畫面所示。

    ![HPC Pack 的 Excel 增益集][addin]

8.  藉由變更加上註解的行，編輯 Excel 中的 VBA 巨集 HPCControlMacros，如下列指令碼所示。 請將您的環境取代為適當的值。

    ![HPC Pack 的 Excel 巨集][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  將 Excel 活頁簿複製到上載目錄，例如 D:\Excel\Upload，如同 VBA 巨集中的 HPC_DependsFiles 常數中所指定。

10. 按一下 [ **叢集** Azure IaaS 叢集上執行活頁簿工作表上的按鈕。

### 執行 Excel UDF

若要執行 Excel UDF，請遵循上述的步驟 1 - 3 來設定用戶端電腦。 關於 Excel UDF，您不需要在運算節點上安裝 Excel 應用程式，因此您可以在步驟 1 中選擇一般運算節點映像，而不是具有 Excel 的運算節點映像。

>[AZURE.NOTE] 沒有 34 個字元限制，在 Excel 2010 和 2013年叢集連接器] 對話方塊。 如果完整的叢集名稱過長，例如 hpcexcelhn01.southeastasia.cloudapp.azure.com，該名稱就無法放入對話方塊中。 解決方法是將機器設定為寬變數例如 *CCP_IAASHN* 長時間的叢集名稱和輸入的值與 *%ccp_iaashn%* 在對話方塊中，做為叢集前端節點名稱。 請注意 Update 2 叢集需要 Update 2 QFE KB3085833 (下載 [這裡](http://www.microsoft.com/en-us/download/details.aspx?id=48725)) SOA 工作階段 api，用戶端電腦上以支援這個因應措施。

成功部署叢集之後，繼續進行下列步驟來執行內建的範例 Excel UDF。 關於自訂 Excel Udf，請參閱這些 [資源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 以建置 Xll 並將它們部署在 IaaS 叢集上。

1.  開啟新的 Excel 活頁簿。 在 **開發** 功能區上，按一下 [ **增益集**。 然後，在對話方塊中，按一下 [ **瀏覽**, 、 %CCP_HOME%Bin\XLL32 資料夾中，瀏覽和選取範例 ClusterUDF32.xll。 如果 ClusterUDF32 不存在於用戶端電腦上，您可以從前端節點上的 %CCP_HOME%Bin\XLL32 資料夾複製它。

    ![選取 UDF][udf]

2.  按一下 [ **檔案** > **選項** > **進階**。 在 **公式** 檢查 **允許使用者定義的 XLL 函數執行運算叢集**。 然後按一下 [ **選項** 中，輸入完整的叢集名稱和 **叢集前端節點名稱**。 (如先前所述，這個輸入方塊限制為 34 個字元，因此較長的叢集名稱可能不適合。 您在這裡可以對長叢集名稱使用電腦全域變數。)

    ![設定 UDF][options]

3.  按一下值 =XllGetComputerNameC() 的儲存格並按 Enter 以在 IaaS 叢集上執行 UDF 運算。 函數只會擷取 UDF 執行所在的運算節點名稱。 初次執行時，認證對話方塊會提示輸入使用者名稱和密碼以連接到 IaaS 叢集。

    ![執行 UDF][run]

    有大量儲存格要時，請按 Alt-Shift-Ctrl + F9 以在所有儲存格上執行計算。

## 步驟 3. 從內部部署用戶端執行 SOA 工作負載

若要在 HPC Pack IaaS 叢集上執行一般的 SOA 應用程式，請先使用步驟 1 中的其中一個方法部署 IaaS 叢集，使用一般運算節點映像 (因為您在運算節點上不需要 Excel)。 接著，遵循下列步驟。

1. 擷取叢集憑證之後，在 Cert:\CurrentUser\Root 下的用戶端電腦上匯入叢集憑證。

2. 安裝 [HPC Pack 2012 R2 更新 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) 和 [HPC Pack 2012 R2 更新 3 用戶端公用程式](https://www.microsoft.com/download/details.aspx?id=49923) ，您可以開發及執行 SOA 用戶端應用程式。

3. 下載 HellowWorldR2 [範例程式碼](https://www.microsoft.com/download/details.aspx?id=41633)。 在 Visual Studio 2010 或 2012 中開啟 HelloWorldR2.sln。

4. 先建置 EchoService 專案，並以您部署至內部部署叢集的相同方式，將服務部署到 IaaS 叢集。 如需詳細步驟，請參閱 HelloWordR2 中的 Readme.doc。 如下所述修改並建置 HellowWorldR2 和其他專案，以從內部部署用戶端電腦產生執行於 Azure IaaS 叢集上的 SOA 用戶端應用程式。

### 搭配使用 Http 繫結和 Azure 儲存體佇列

若要搭配使用 Http 繫結與 Azure 儲存體佇列，請對範例程式碼進行一些變更。

* 更新叢集名稱。

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* (選擇性) 在 SessionStartInfo 中使用預設 TransportScheme 或明確地將它設為 Http。

```
    info.TransportScheme = TransportScheme.Http;
```

* 使用預設繫結做為 BrokerClient。

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    或者明確使用 basicHttpBinding 設定。

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* 選擇性地，在 SessionStartInfo 中將 UseAzureQueue 旗標設為 true。 如果未設定，它會根據預設，在叢集名稱具有 Azure 網域尾碼且 TransportScheme 為 Http 時設為 true。

    ```
    info.UseAzureQueue = true;
```

###使用 Http 繫結而不使用 Azure 儲存體佇列

若要這樣做，請在 SessionStartInfo 中明確將 UseAzureQueue 旗標設為 false。

```
    info.UseAzureQueue = false;
```

### 使用 NetTcp 繫結

若要使用 NetTcp 繫結，組態就像是連接至內部部署叢集。 您必須開啟幾個前端節點 VM 上的端點。 在 Azure 傳統入口網站中執行下列動作。


1. 停止 VM。

2. 新增 TCP 連接埠 9090、9087、9091、9094 分別做為工作階段、代理人、 代理背景工作和資料服務

    ![設定端點][endpoint]

3. 啟動 VM。

SOA 用戶端應用程式不需要變更，除了將標頭名稱改變為 IaaS 叢集的完整名稱。

## 後續步驟

* 請參閱 [這些資源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 如需有關使用 HPC Pack 執行 Excel 工作負載。

* 請參閱 [Microsoft HPC Pack 中管理 SOA 服務](https://technet.microsoft.com/library/ff919412.aspx) 如需有關部署和管理 HPC pack SOA 服務。

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png


