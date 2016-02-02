<properties
    pageTitle="在 Azure 中使用 TFS 連續傳遞雲端服務 | Microsoft Azure"
    description="了解如何設定 Azure 雲端應用程式的連續傳遞。MSBuild 命令列陳述式和 PowerShell 指令碼的程式碼範例。"
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor="tglee"/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="tarcher"/>


# Azure 中雲端服務的連續傳遞

本文所述的程序顯示如何
為 Azure 雲端應用程式設定連續傳遞。 此程序可讓您在每次程式碼簽入後
自動建立封裝，並將封裝部署至 Azure
之後每個程式碼簽入。 封裝建置在此程序
等同於 **封裝** 命令在 Visual Studio 中，
發行步驟相當於 **發行** Visual Studio 中的命令。
本文涵蓋您可用來建立組建伺服器的方法 (
使用 MSBuild 命令列陳述式和 Windows PowerShell 指令碼)，
另外示範如何選擇性設定 Visual Studio Team
Foundation Server-Team Build 定義來使用 MSBuild 命令
和 PowerShell 指令碼。 您可針對組建
環境及 Azure 目標環境自訂此程序。

此動作也可以用 Visual Studio Team Services (託管於 Azure 中的 TFS 版本) 來執行，這樣會變得更容易。 如需詳細資訊，請參閱 [連續傳遞至 Azure 使用 Visual Studio Team Services []][]。

開始之前，您應該先從 Visual Studio 發佈應用程式。
如此可確保當您嘗試將發佈程序自動化時，
所有資源皆可用並已初始化。

## 步驟 1：設定組建伺服器

您可以使用 MSBuild 建立 Azure 封裝之前，您必須
在組建伺服器上安裝必要的軟體和工具。

組建伺服器上不需要安裝 Visual Studio。 If
要使用 Team Foundation Build Service 來管理您的組建
伺服器上，依照 [Team Foundation Build 服務 []][]
文件的指示。

1.  在組建伺服器上安裝 [[.NET Framework 4.5.2]][], ，其中包括 MSBuild。
2.  安裝最新 [適用於.NET 的 Azure 製作工具](https://azure.microsoft.com/develop/net/)。
3.  安裝 [Azure Libraries for.NET](http://go.microsoft.com/fwlink/?LinkId=623519)。
4.  將 Microsoft.WebApplication.targets 檔案從 Visual Studio 安裝複製到組建伺服器上。

    在電腦上已安裝 Visual studio，這個檔案位於目錄 C:\\Program Files (x86) \\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications。 您應該將它複製至組建伺服器上的相同目錄。
5.  安裝 [Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx)。

## 步驟 2：使用 MSBuild 命令建置封裝

本節說明如何建構 MSBuild 命令來建置
Azure 封裝。 在組建伺服器上執行這個步驟，確認
一切都已正確設定，且 MSBuild 命令執行
您要它執行。 您可以將此命令列新增至現有的
組建伺服器上的現有組建指令碼，或者在
TFS 組建定義中使用命令列，如下一節所述。 如需
命令列參數及 MSBuild 的詳細資訊，請參閱 [MSBuild 命令列參考](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx)。

1.  如果組建伺服器上已安裝 Visual Studio，找出並選擇
    **Visual Studio 命令提示字元** 中 **Visual Studio 工具**
    在 Windows 中的資料夾。

    如果組建伺服器上未安裝 Visual Studio，請開啟
    命令提示字元，並確定可在路徑上存取 MSBuild.exe
    。 MSBuild 會與 .NET Framework 一起安裝在路徑
    %WINDIR%\\Microsoft.NET\\Framework\\*版本*。 例如，若要在已安裝 .NET Framework 4 的情況下，
    將 MSBuild.exe 新增至 PATH 環境變數，
    請在命令提示字元中輸入下列命令
    ：

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  在命令提示字元中，瀏覽至包含的資料夾
    Azure 專案檔所在的資料夾。

3.  執行 MSBuild 搭配 /target: Publish 選項，如下列所示
    例如：

        MSBuild /target:Publish

    此選項可以縮寫為 /t:Publish。 請勿混淆 MSBuild 中的 /T: Publish 選項
    與 Visual Studio 中的 [發行] 命令 (
    如果已安裝 Azure SDK)
    。 /t:Publish 選項只會建置 Azure
    封裝。 其並不會如 Visual Studio 中的 [發行] 命令一樣部署
    封裝。

    (選擇性) 您可以指定專案名稱作為 MSBuild
    參數。 如果未指定，則會使用目前目錄。 如需
    MSBuild 命令列選項資訊，請參閱 [MSBuild 命令
    列參考] [1]。

4.  尋找輸出。 根據預設，此命令會建立
    與專案的根資料夾相關的目錄，例如
    *ProjectDir*\\bin\\*組態*\\app.publish\\。 當您
    建置 Azure 專案時會產生兩個檔案，封裝
    檔案本身和伴隨的組態檔：

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    根據預設，每個 Azure 專案包含一個
    本機 (偵錯) 組建的 (.cscfg 檔)，
    和另一個用於雲端 (預備或生產) 組建的服務組態檔，但是您
    可以視需要加入或移除服務組態檔。 當您
    在 Visual Studio 中建置封裝時，系統會詢問您
    要隨封裝包含的服務組態檔。

5.  指定服務組態檔。 使用 MSBuild 建立封裝時，
    預設會包含本機服務組態檔
    。 若要包含不同的服務組態檔，請設定
    MSBuild 命令的 TargetProfile 屬性，如下列所示
    例如：

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  指定輸出的位置。 使用
    /p: publishdir =*目錄*\\ 選項，包括句尾
    反斜線分隔字元，如下列範例所示：

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    一旦建構並測試適當的 MSBuild 命令
    列來建置專案，並將它們結合為 Azure 封裝後，
    您可以將這個命令列加入至組建指令碼。 如果您的組建
    伺服器使用自訂指令碼，此程序將取決於
    自訂建置流程的內容。 如果您將 TFS 做為
    組建環境，則可遵循下一步的指示，
    將 Azure 封裝新增至您的建置流程。

## 步驟 3：使用 TFS Team Build 建置套件

如果您有設定為組建控制器的 Team Foundation Server (TFS)，
設定組建伺服器作為 TFS 組建機器，然後您可以選擇性地設定
Azure 封裝設定自動化組建。 如需
如何設定和使用 Team Foundation Server 做為組建系統的詳細資訊，請參閱
[擴充您的建置系統 []][]。 特別是，
下列程序假設您已
中所述 [部署和設定組建伺服器 []][], ，而且您已建立
在 Team 專案，並在 Team 專案中建立雲端服務專案。

若要設定 TFS 來建置 Azure 套件，請執行下列
步驟：

1.  在開發電腦的 Visual Studio，於 [檢視]
    功能表上，選擇 [ **Team Explorer**, ，或選擇 Ctrl + \\，Ctrl + M。 在
    Team 總管] 視窗中，展開 **組建** 節點或選擇 **組建**
    頁面，然後選擇 [ **新增組建定義**。

    ![][0]

2.  選擇 **觸發程序** 索引標籤，然後指定所需的條件
    。 例如，指定
    **連續整合** 建置套件時的來源
    。

3.  選擇 **來源設定** 索引標籤，然後確認已列出您的專案資料夾
    在 **原始檔控制資料夾** 資料行，以及狀態是 **Active**。

4.  選擇 **組建預設值** 索引標籤，然後在 [組建控制器，驗證
    組建伺服器的名稱。 而且請選擇選項 * * 將組建複製
    輸出至下列置放資料夾 * *，並指定所需的置放
    位置。

5.  選擇 [處理序]**** 索引標籤。 在 [處理序] 索引標籤上選擇預設
    範本下 **建置**, ，選擇專案如果未選取，
    展開 **進階** 一節中 **建置** 方格的區段。

6.  選擇 **MSBuild 引數**, ，並設定適當的 MSBuild
    命令列引數。 例如，
    輸入 **/t: Publish /p: publishdir = \\\myserver\\drops\\** 建置
    封裝，並將封裝檔案複製到位置
    \\\\myserver\\drops\\：

    ![][2]

    **附註:** 將檔案複製至公用共用可讓您更輕鬆地
    從開發電腦手動部署封裝。

5.  簽入專案的變更來測試組建步驟是否成功，
    或將新組建排入佇列。 若要佇列新組建，請在
    Team Explorer 中，以滑鼠右鍵按一下 **所有組建定義** ，然後
    選擇 **佇列新組建**。

## 步驟 4：使用 PowerShell 指令碼發佈封裝

本節描述如何建構 Windows PowerShell 指令碼，
以使用選用參數，將雲端應用程式封裝輸出發行至 Azure
。 呼叫此指令碼的時機可以是
執行自訂組建自動化中的組建步驟之後。 也可以從
Visual Studio TFS Team Build 中的流程範本工作流程活動中呼叫它。

1.  安裝 [Azure PowerShell cmdlet []][] (0.6.1 版或更高版本)。
    在 Cmdlet 設定階段期間，請選擇安裝為嵌入式管理單元。 注意
    此正式支援的版本會取代 CodePlex 提供的較舊版本，
    雖然舊版本的編號為 2.x.x。

2.  使用 [開始] 功能表或 [開始] 頁面啟動 Azure PowerShell。 如果您以這種方式啟動，
    將會載入 Azure PowerShell Cmdlet。

3.  在 PowerShell 提示字元中
    輸入部分命令 `Get-azure` 然後按下 Tab 鍵陳述式
    確認已載入 PowerShell Cmdlet。

    如果您重覆按 Tab 鍵，應該會看到各種 Azure PowerShell 命令。

4.  從 .publishsettings 檔案匯入您的訂閱資訊，
    確認可以連接到 Azure 訂閱。

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    然後輸入命令

    `Get-AzureSubscription`

    如此即會顯示訂用帳戶的相關資訊。 確認一切正確無誤。

4.  將本文結尾提供的指令碼範本儲存至
    您的指令碼資料夾，做為
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**。

5.  檢閱指令碼的參數區段。 加入或修改任何
    預設值。 您永遠可以傳遞明確參數來覆寫這些值
    。

6.  確定在發行指令碼所針對的訂閱中，
    建立有效的雲端服務和儲存體帳戶。 Auch die Eigenschaften
    儲存體帳戶 (Blob 儲存體) 將在建立部署時，用來上傳和
    暫時儲存部署封裝與組態檔
    。

    -   若要建立新的雲端服務，您可以呼叫此指令碼或
        Azure 管理入口網站中。 雲端服務名稱
        將做為完整網域名稱的前置詞，
        因此它必須是唯一的。

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   若要建立新的儲存體帳戶，您可以呼叫此指令碼或
        Azure 管理入口網站中。 儲存體帳戶名稱。
        將做為完整網域名稱的前置詞，
        因此它必須是唯一的。 您可以嘗試使用與雲端服務相同的名稱
        。

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"


7.  直接從 Azure PowerShell 呼叫指令碼，或將此
    指令碼連線您的主機，以便在封裝組建後自動建置
    。
    >[AZURE.IMPORTANT] 指令碼將永遠刪除或取代現有的
    部署 (如果偵測到)。 此為不得不的方式，
    因為如此一來，完全省去使用者互動過程的自動化程序才有辦法進行連續傳遞
    。

    **範例案例 1:** 連續部署至預備環境
    ：

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    此作業後面通常接著測試執行驗證及 VIP 交換
    。 可以透過 Azure 管理入口網站完成 VIP 交換，
    也可以使用 Move-deployment Cmdlet 完成交換。

    **範例案例 2:** 連續部署至生產環境
    環境

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **遠端桌面：**

    如果您的 Azure 專案中啟用遠端桌面，
    則需要再執行一遍步驟，以確保將正確的
    雲端服務憑證上傳到此指令碼選定的所有雲端服務
    。

    尋找您的角色所預期收到的憑證指紋值。 Auch die Eigenschaften
    憑證指紋值會顯示在
    雲端組態檔 (也就是 ServiceConfiguration.Cloud.cscfg) 的 [Certificates] 區段。 當您 [顯示選項] 並檢視所選取的憑證時，它
    也會出現在 Visual Studio 中的 [遠端桌面組態] 對話方塊中
    。

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    使用下列 Cmdlet 指令碼，上傳遠端桌面憑證作為一次性設定步驟
    ：

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    例如：

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    或者您可以匯出憑證檔 PFX 與私密金鑰，
    並使用 Azure 管理入口網站將憑證上傳至每個目標雲端服務
    Azure 管理入口網站。 若要深入了解，請閱讀下列文章
    ：
    [[http://msdn.microsoft.com/library/windowsazure/gg443832.aspx]][]。

    **升級部署以及刪除部署-\ > 新的部署**

    未傳入參數或明確傳遞值 1 時，指令碼預設會執行升級部署
    ($enableDeploymentUpgrade = 1)
    。 若為單一執行個體，則具有
    耗時較短的好處 (相較於完整部署)。 若為
    需要高可用性的執行個體，這也具有一項優點，
    即同時執行執行個體並升級其他執行個體 (瀏覽
    您的更新網域)，而且不會刪除您的 VIP。

    您可以在指令碼
    ($enableDeploymentUpgrade = 0) 中停用升級部署，或藉由傳遞
    *-enableDeploymentUpgrade 0* 做為參數，這會改變
    指令碼的行為，即先刪除任何現有的部署，然後
    建立新的部署。
    >[AZURE.IMPORTANT] 指令碼將永遠刪除或取代現有的
    部署 (如果偵測到)。 此為不得不的方式，
    因為如此一來，才有辦法執行完全省去使用者/操作者互動過程的自動連續傳遞
    。

## 步驟 5：使用 TFS Team Build 發佈套件

這個選擇性的步驟連接 TFS Team Build 到步驟 4 中建立的指令碼
該指令碼負責處理將套件組建發佈至 Azure。 此
需要修改您的組建定義所使用的流程範本，以便在
工作流程結束時執行 Publish 活動。 Publish
活動會利用組建傳入的參數，執行 PowerShell 命令
。 會將輸出的 MSBuild 目標與發行指令碼
透過管線傳遞給標準組建輸出。

1.  編輯負責連續部署的「組建定義」。

2.  選取 [處理序]**** 索引標籤。

3.  請依照下列 [這些指示](http://msdn.microsoft.com/library/dd647551.aspx) 新增
    活動專案，下載預設範本，將它加入至
    將專案簽入。 指定建置流程範本的新的名稱例如
    AzureBuildProcessTemplate。

3.  返回 **程序** 索引標籤，然後使用 **顯示詳細資料** 以顯示可用的清單
    建置流程範本的清單。 選擇 **新增...** ] 按鈕，然後巡覽至專案
    剛才加入及簽入中。 找出剛才建立的範本並選擇 [**確定**]。

4.  開啟選取的流程範本進行編輯。 您可以
    在工作流程設計工具或在 XML 編輯器中直接開啟，以使用
    XAML。

5.  在工作流程設計工具的引數索引標籤中，
分行新增下列清單中的新引數。 所有引數都應該
具有 direction=In 及 type=String。 這些引數可將
參數從組建定義傳至工作流程，然後用來
呼叫發行指令碼。

    SubscriptionName
    StorageAccountName
    CloudConfigLocation
    PackageLocation
    Environment
    SubscriptionDataFileLocation
    PublishScriptLocation
    ServiceName

![][3]

對應的 XAML 看起來如下：

    <Activity  _ />
      <x:Members>
        <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
        <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
        <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
        <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
        <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
        <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
        <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
        <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
        <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
        <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
        <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
        <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
        <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
        <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
        <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
        <x:Property Name="GetVersion" Type="InArgument(x:String)" />
        <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
        <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
        <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
        <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
        <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
        <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
        <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
        <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
        <x:Property Name="Environment" Type="InArgument(x:String)" />
        <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
        <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
        <x:Property Name="ServiceName" Type="InArgument(x:String)" />
      </x:Members>
    
      <this:Process.MSBuildArguments>

6.  在 [在代理程式上執行] 結尾新增一個順序：

    1.  先新增 If Statement 活動，以檢查是否有有效
        指令碼檔案。 將條件設為此值：

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  在 If 陳述式的 Then 案例中，新增 Sequence
        活動。 將顯示名稱設為 'Start publish'

    3.  在開始發行序列仍處於選取的狀態下，加入
        下列新變數清單做為
        工作流程設計工具的變數索引標籤中的個別明細項目。 所有變數都應該
        具有 Variable type =String 及 Scope=Start publish。 這些變數可將
        參數從組建定義傳至
        工作流程，然後用來呼叫發行指令碼。

        -   SubscriptionDataFilePath，型別為 String

        -   PublishScriptFilePath，型別為 String

            ![][4]

    4.  如果您使用 TFS 2012 或更早版本，請在新序列的開頭新增 ConvertWorkspaceItem 活動
        。 如果您使用 TFS 2013 或更新版本，請在新序列的開頭新增 GetLocalPath 活動。 針對 ConvertWorkspaceItem，設定屬性，如下所示: 方向 = ServerToLocal，DisplayName ='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'。 針對 GetLocalPath 活動，請將內容 IncomingPath 設定為 'PublishScriptLocation'，以及將 Result 設定為 'PublishScriptFilePath'。 此
        活動會將發行指令碼的路徑從 TFS 伺服器
        位置，轉換成標準本機磁碟路徑 (如果適用)。

    5.  如果您使用 TFS 2012 或更早版本，請在新序列的結尾新增另一個 ConvertWorkspaceItem 活動
        。 Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        結果 = 'SubscriptionDataFilePath'，Workspace = 'Workspace'。 如果您使用 TFS 2013 或更新版本，請新增另一個 GetLocalPath。 IncomingPath='SubscriptionDataFileLocation'，以及 Result='SubscriptionDataFilePath'。

    6.  在新順序的結尾新增 InvokeProcess 活動。
        此活動會利用組建定義傳入的引數呼叫 PowerShell.exe
        。

        1.  引數 = String.Format ("-檔案""{0}""-serviceName \{1\}
            -a g e \{2\}-packageLocation 」 「 \{3\}"」
            -cloudConfigLocation 」 」 \{4\}""-subscriptionDataFile 」 」 \{5\}"」
            -selectedSubscription \{6\}-環境 」 「 \{7\}"""，
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (包括引號)

        4.  OutputEncoding=
            System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  在 **處理標準輸出** 的] 區段文字方塊
        設文字方塊值設為 'data'。 這是
        要用來儲存標準輸出資料的變數。

    8.  新增 WriteBuildMessage 活動正下方 **處理標準輸出**
        一節中。 設定 Importance =
        '' Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High
        及 Message='data'。 如此可確保指令碼的標準輸出會
        寫入至組建輸出。

    9.  在 **處理錯誤輸出** 的] 區段文字方塊
        設文字方塊值設為 'data'。 這是
        要用來儲存標準錯誤資料的變數。

    10. 新增 WriteBuildError 活動正下方 **處理錯誤輸出**
        一節中。 設定 Message='data'。 如此可確保
        指令碼的標準錯誤會寫入至組建錯誤輸出。

    11. 更正由藍色驚嘆號指出的任何錯誤。 以滑鼠暫留在
        驚嘆號上以取得錯誤的相關提示。 儲存工作流程以
        清除錯誤。

    在設計工具中，發佈工作流程活動的最終結果將看起來如下
    ：

    ![][5]

    在設計工具中，發佈工作流程活動的最終結果將看起來如下
    ：

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>

7.  儲存建置流程範本工作流程，並簽入此檔案。

8.  編輯組建定義 (如果已開啟則將其關閉)，然後選取
    **新增** ] 按鈕，如果您不尚未看到新範本的流程範本清單中。

9.  在 Misc 區段中設定如下參數屬性值：

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        *該值衍生自：
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *此值衍生自: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *在這裡使用適當的雲端服務名稱*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *在這裡使用適當的儲存體帳戶名稱*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. 儲存組建定義的變更。

11. 將組建排入佇列，以同時執行套件建置及發佈。 如果您
    已將觸發程序設為 Continuous Integration，則每次簽入時都會執行此行為
    。

### PublishCloudService.ps1 指令碼範本

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## 後續步驟

若要啟用遠端偵錯使用連續傳遞時，請參閱 [啟用遠端偵錯使用連續傳遞來發行至 Azure 時](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)。


[continuous delivery to azure by using visual studio team services]: cloud-services-continuous-delivery-use-vso.md 
[team foundation build service]: https://msdn.microsoft.com/library/ee259687.aspx 
[.net framework 4]: https://www.microsoft.com/download/details.aspx?id=17851 
[.net framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653 
[.net framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643 
[scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx 
[deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx 
[azure powershell cmdlets]: powershell-install-configure.md 
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0 
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png 
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png 
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png 
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png 
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png 
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png 

