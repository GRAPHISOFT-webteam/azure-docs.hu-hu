<properties
   pageTitle="使用 Windows PowerShell 指令碼來發佈至開發和測試環境 | Microsoft Azure"
   description="了解如何從 Visual Studio 使用 Windows PowerShell 指令碼來發佈至開發和測試環境。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="tarcher" />

# 使用 Windows PowerShell 指令碼來發佈至開發和測試環境

當您在 Visual Studio 中建立 Web 應用程式時，您可以產生 Windows PowerShell 指令碼，以供稍後用來將網站自動發佈至 Azure 做為 Azure App Service 或虛擬機器中的 Web 應用程式。 您可以在 Visual Studio 編輯器中編輯和擴充 Windows PowerShell 指令碼以符合需求，或將指令碼整合到現有組建、測試和發佈指令碼。

使用這些指令碼，您可以佈建網站的自訂版本 (也稱為開發和測試環境)，以做臨時使用。 例如，您可以在 Azure 虛擬機器上或網站的預備位置上設定網站的特定版本，以執行測試套件、重現錯誤、測試錯誤修正、試驗提議的變更，或設定用來進行示範或展示的自訂環境。 在建立用來發佈專案的指令碼後，您可以視需要重新執行指令碼來重建相同環境，或對 Web 應用程式的自有組建執行指令碼以建立用於測試的自訂環境。

## 您需要什麼

- Azure SDK 2.3 或更新版本。 請參閱 [Visual Studio 下載](http://go.microsoft.com/fwlink/?LinkID=624384) 如需詳細資訊。

    要產生 Web 專案的指令碼並不需要用到 Azure SDK。 這項功能是供 Web 專案使用，而非供雲端服務中的 Web 角色使用。

- Azure PowerShell 0.7.4 或更新版本。 請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 如需詳細資訊。

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) 或更新版本。

## 其他工具

我們有提供其他工具和資源，以供您在 Visual Studio 中使用 PowerShell 進行 Azure 開發。 請參閱 [PowerShell Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012)。

## 產生發佈指令碼

您可以產生發行指令碼的虛擬機器裝載您的網站，當您建立新專案遵循 [這些指示](virtual-machines-dotnet-create-visual-studio-powershell.md)。 您也可以 [產生 Azure App Service 中發佈的 web 應用程式的指令碼](web-sites-dotnet-get-started.md)。

## Visual Studio 所產生的指令碼

Visual Studio 會產生名為方案層級的資料夾 **PublishScripts** ，其中包含兩個 Windows PowerShell 檔案，您的虛擬機器或網站，並包含您可以使用指令碼中的函式的模組的發行指令碼。 Visual Studio 也會產生 JSON 格式的檔案，以指定您要部署之專案的詳細資料。

### Windows PowerShell 發佈指令碼

發佈指令碼含有部署至網站或虛擬機器的特定發佈步驟。 Visual Studio 提供語法著色功能，可用來進行 Windows PowerShell 開發。 有提供函式說明，而且您可以自由編輯指令碼中的函式以符合您不斷變化的需求。

### Windows PowerShell 模組

Visual Studio 所產生的 Windows PowerShell 模組包含發佈指令碼所使用的函式。 這些都是 Azure PowerShell 函式，不可進行修改。 請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 如需詳細資訊。

### JSON 組態檔

JSON 檔案建立在 **組態** 資料夾，並且包含指定完全要部署至 Azure 的資源的組態資料。 Visual Studio 所產生之檔案的名稱是 project-name-WAWS-dev.json (如果您建立網站) 或 project name-VM-dev.json (如果您建立虛擬機器)。 以下是建立網站時所產生之 JSON 組態檔的範例。 其中大多數的值都簡單易懂。 網站名稱是由 Azure 產生，因此可能不符合您的專案名稱。

```
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [
            {
                "connectionStringName": "DefaultConnection",
                "databaseName": "WebApplication26632_db",
                "serverName": "YourDatabaseServerName",
                "user": "sqluser2",
                "password": "",
                "edition": "",
                "size": "",
                "collation": "",
                "location": "West US"
            }
        ]
    }
}
```
在建立虛擬機器時，JSON 組態檔看起來類似下面範例。 請注意，雲端服務會建立為虛擬機器的容器。 虛擬機器包含用來透過 HTTP 和 HTTPS 進行 Web 存取的常用端點以及供 Web Deploy 使用的端點，後者可讓您從本機電腦、遠端桌面和 Windows PowerShell 發佈至網站。

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

您可以編輯 JSON 組態以變更執行發佈指令碼時會進行的作業。 `cloudService` 和 `virtualMachine` 是必要區段，但如果您不需要 `databases` 區段，則可以將它刪除。 Visual Studio 所產生的預設組態檔中的空白屬性是選用屬性；預設組態檔中具有值的屬性則是必要屬性。

如果您有內含多個部署環境 (稱為位置) 的網站而不是在 Azure 中的單一生產網站，您可以在 JSON 組態檔的網站名稱中加入位置名稱。 例如，如果您有一個名為的網站 **mysite** 和位置，名為 **測試** 的 URI 是 mysite test.cloudapp.net，但在組態檔中使用正確的名稱為 {1>mysite。 只有在訂用帳戶中已存在網站和位置時才能這麼做。 如果不存在，請執行指令碼 (不指定位置) 來建立網站，然後在 Azure 管理入口網站中建立位置，之後再以修改過的網站名稱執行指令碼。 如需 web 應用程式的部署位置的詳細資訊，請參閱 [設定預備環境的 web 應用程式在 Azure App Service 中](web-sites-staged-publishing.md)。

## 如何執行發佈指令碼

如果您之前未曾執行過 Windows PowerShell 指令碼，您必須先設定執行原則以啟用要執行的指令碼。 如果 Windows PowerShell 指令碼容易受到涉及執行指令碼的惡意程式碼或病毒的威脅，這項安全性功能可防止使用者執行 Windows PowerShell 指令碼。

### 執行指令碼

1. 建立專案的 Web Deploy 封裝。 Web Deploy 封裝是經過壓縮的封存檔 (.zip 檔案)，內含您想要複製到網站或虛擬機器的檔案。 您可以在 Visual Studio 中為任何 Web 應用程式建立 Web Deploy 封裝。

    ![建立 Web Deploy 封裝](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

    如需詳細資訊，請參閱 [How to: 在 Visual Studio 中建立 Web 部署套件](https://msdn.microsoft.com/library/dd465323.aspx)。 您也可以將自動化的 Web Deploy 套件，建立一節中所述 **自訂和擴充發行指令碼** 稍後在本主題中。

1. 在 **方案總管] 中**, ，開啟指令碼時，內容功能表，然後選擇 **開啟 PowerShell ISE**。

1. 如果這是您第一次在此電腦上執行 Windows PowerShell 指令碼，請以系統管理員權限開啟命令提示字元視窗，並輸入下列命令：

    `Set-ExecutionPolicy RemoteSigned`

1. 使用下列命令登入 Azure。

    `Add-AzureAccount`

    出現提示時，提供您的使用者名稱和密碼。

    請注意，當您自動執行指令碼時，這個提供 Azure 認證的方法將沒有作用。 您應該改用 .publishsettings 檔案來提供認證。 一次您只能使用命令 **Get-azurepublishsettingsfile** 從 Azure 下載檔案，並在之後使用 **Import-azurepublishsettingsfile** 匯入檔案。 如需詳細指示，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

1. (選擇性)資料庫和網站，但不要發行 web 應用程式，如果您想要建立 Azure 資源，例如虛擬機器，使用 **Publish-webapplication.ps1** 命令搭配 **-設定** 引數設定為 JSON 組態檔。 此命令列使用 JSON 組態檔來決定要建立哪些資源。 因為它使用其他命令列引數的預設設定，所以會建立資源，但不會發佈 Web 應用程式。 –Verbose 選項可讓您進一步了解會發生什麼事。

    `Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. 使用 **Publish-webapplication.ps1** 命令，因為其中一個叫用指令碼和發行您的 web 應用程式的下列範例所示。 如果您需要覆寫任何其他引數的預設設定，如訂用帳戶名稱、發佈封裝名稱、虛擬機器認證或資料庫伺服器認證，您可以指定這些參數。 使用 **-Verbose** 選項可查看的發行程序進度的詳細資訊。

    ```
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    如果您要建立虛擬機器，其命令如下所示。 此範例也顯示如何指定多個資料庫的認證。 針對這些指令碼所建立的虛擬機器，其 SSL 憑證不是來自受信任的根授權單位。 因此，您必須使用 **– AllowUntrusted** 選項。

    ```
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    指令碼可以建立資料庫，但不會建立資料庫伺服器。 如果您想要建立的資料庫伺服器，您可以使用 **New-azuresqldatabaseserver** Azure 模組中的函式。

## 自訂和擴充發佈指令碼

您可以自訂發佈指令碼和 JSON 組態檔。 Windows PowerShell 模組中的函式 **AzureWebAppPublishModule.psm1** 不適合修改。 如果您想要指定不同的資料庫或變更虛擬機器的某些屬性，請編輯 JSON 組態檔。 如果您想要擴充功能的指令碼，以自動建置和測試您的專案，您可以實作中的函數虛設常式 **Publish-webapplication.ps1**。

若要自動建置專案，請將呼叫 MSBuild 的程式碼加入 `New-WebDeployPackage`，如此程式碼範例所示。 MSBuild 命令的路徑會因為您所安裝的 Visual Studio 版本而有所不同。 若要取得正確的路徑，您可以使用函數 **Get-msbuildcmd**, ，在此範例所示。

### 自動建置專案

1. 在全域參數區段新增 `$ProjectFile` 參數。

    ```
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

1. 將函式 `Get-MSBuildCmd` 複製到指令碼檔案。

    ```
    function Get-MSBuildCmd
    {
            process
            {

                 $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                       Sort-Object {[double]$_.PSChildName} -Descending |
                                       Select-Object -First 1 |
                                       Get-ItemProperty -Name MSBuildToolsPath |
                                       Select -ExpandProperty MSBuildToolsPath
           
                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

1. 以下列程式碼取代 `New-WebDeployPackage`，並取代建構 `$msbuildCmd` 的程式行中的預留位置。 此程式碼係用於 Visual Studio 2015。 如果您使用 Visual Studio 2013，變更 **VisualStudioVersion** 屬性下方 `12.0`。

    ```
    function New-WebDeployPackage
      {
        #Write a function to build and package your web application
          
        #To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
          
        Write-VerboseWithTime 'Build-WebDeployPackage: Start'
          
        $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
          
        Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
          
        #Start execution of the build command
        $job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
          
        if ($job.ExitCode -ne 0)
        {
          throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
        }

        #Obtain the project name
        $projectName = (Get-Item $ProjectFile).BaseName
          
        #Construct the path to web deploy zip package
        $DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
          
          
        #Get the full path for the web deploy zip package. This is required for MSDeploy to work
        $WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
          
        Write-VerboseWithTime 'Build-WebDeployPackage: End'
          
        return $WebDeployPackage
      }
    ```

1. 在此程式行前呼叫 `New-WebDeployPackage` 函式：`$Config = Read-ConfigFile $Configuration` (若為 Web 應用程式) 或 `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` (若為虛擬機器)。

    ```
    if($ProjectFile)
      {
        $WebDeployPackage = New-WebDeployPackage
      }
    ```

1. 從命令列使用傳遞 `$Project` 引數叫用自訂指令碼，如下列範例的命令列所示。

    ```
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
     -ProjectFile ..\WebApplication5\WebApplication5.csproj `
     -VMPassword @{Name="VMUser";Password="Test.123"} `
     -AllowUntrusted `
    -Verbose
    ```

    若要自動測試應用程式，請將程式碼加入 `Test-WebApplication`。 請務必在行取消註解 **Publish-webapplication.ps1** 呼叫這些函數。 如果您沒有提供實作，您可以使用 Visual Studio 手動建置專案，然後執行發佈指令碼以發佈至 Azure。

## 發佈函式摘要

若要取得可以在 Windows PowerShell 命令提示字元使用之函式的說明，請使用 `Get-Help function-name` 命令。 說明中會包括參數說明和範例。 在指令碼來源檔案中，也有相同的說明文字 **AzureWebAppPublishModule.psm1** 和 **Publish-webapplication.ps1**。 指令碼和說明都已當地語系化為 Visual Studio 所使用的語言。

**AzureWebAppPublishModule**

|函式名稱|說明|
|---|---|
|Add-AzureSQLDatabase|建立新的 Azure SQL Database。|
|Add-AzureSQLDatabases|從 Visual Studio 所產生的 JSON 組態檔中的值建立 Azure SQL Database。|
|Add-AzureVM|建立 Azure 虛擬機器並傳回所部署 VM 的 URL。 函式會設定先決條件，然後呼叫 **New-azurevm** 函式 (Azure 模組) 來建立新的虛擬機器。|
|Add-AzureVMEndpoints|將新的輸入端點加入至虛擬機器，並傳回具有新端點的虛擬機器。|
|Add-AzureVMStorage|在目前的訂用帳戶中建立新的 Azure 儲存體帳戶。 帳戶名稱開頭是 "devtest"，後面接著唯一的英數字元字串。 此函式會傳回新儲存體帳戶的名稱。 您必須指定新儲存體帳戶的位置或同質群組。|
|Add-AzureWebsite|使用指定的名稱和位置建立網站。 此函數會呼叫 **New-azurewebsite** Azure 模組中的函式。 如果訂用帳戶還沒有具有指定名稱的網站，此函式會建立該網站並傳回網站物件。 否則，它會傳回 `$null`。|
|Backup-Subscription|在指令碼範圍的 `$Script:originalSubscription` 變數中儲存目前的 Azure 訂用帳戶。此函式會在指令碼範圍中，儲存目前的 Azure 訂用帳戶 (由 `Get-AzureSubscription -Current` 取得) 與其儲存體帳戶，以及此指令碼所變更的訂用帳戶 (儲存在 `$UserSpecifiedSubscription` 變數中) 與其儲存體帳戶。 透過儲存這些值，您可以使用函式 (例如 `Restore-Subscription`) 將原始的目前訂用帳戶和儲存體帳戶還原為目前狀態 (如果目前狀態已變更)。|
|Find-AzureVM|取得指定的 Azure 虛擬機器。|
|Format-DevTestMessageWithTime|在訊息前面加上日期和時間。 此函式是專為寫入 Error 和 Verbose 串流的訊息所設計。|
|Get-AzureSQLDatabaseConnectionString|組合連接字串來連線到 Azure SQL Database。|
|Get-AzureVMStorage|傳回名稱的第一個儲存體帳戶名稱模式"devtest*"(不區分大小寫) 中指定的位置或同質群組。如果"devtest*」 的位置或同質群組，不符合儲存體帳戶、 函式則會予以忽略。 您必須指定位置或同質群組。|
|Get-MSDeployCmd|傳回執行 MsDeploy.exe 工具的命令。|
|New-AzureVMEnvironment|在訂用帳戶中尋找或建立符合 JSON 組態檔中的值的虛擬機器。|
|Publish-WebPackage|使用 MsDeploy.exe 和 Web 發佈封裝 .Zip 檔案將資源部署至網站。 此函式不會產生任何輸出。 如果呼叫 MSDeploy.exe 失敗，此函式會擲回例外狀況。 若要取得更詳細的輸出，請使用 **-Verbose** 選項。|
|Publish-WebPackageToVM|驗證參數值，然後再呼叫 **Publish-webpackage** 函式。|
|Read-ConfigFile|驗證 JSON 組態檔並傳回所選值的雜湊資料表。|
|Restore-Subscription|將目前的訂用帳戶重設為原始訂用帳戶。|
|Test-AzureModule|如果所安裝的 Azure 模組版本為 0.7.4 或更新版本，則傳回 `$true`。 如果尚未安裝模組或模組為較舊的版本，則傳回 `$false`。 此函式沒有參數。|
|Test-AzureModuleVersion|如果 Azure 模組的版本為 0.7.4 或更新版本，則傳回 `$true`。 如果尚未安裝模組或模組為較舊的版本，則傳回 `$false`。 此函式沒有參數。|
|Test-HttpsUrl|將輸入的 URL 轉換為 System.Uri 物件。 如果 URL 為絕對值，而且其配置為 https，則傳回 `$True`。 如果 URL 是相對值、其配置不是 HTTPS 或輸入的字串無法轉換成 URL，則傳回 `$false`。|
|Test-Member|如果屬性或方法是物件的成員，則傳回 `$true`。 否則傳回 `$false`。|
|Write-ErrorWithTime|寫入前面會加上目前時間的錯誤訊息。 此函數會呼叫 **格式 DevTestMessageWithTime** 函式可將訊息寫入 Error 串流之前附加時間。|
|Write-HostWithTime|將訊息寫入主機程式 (**Write-host**) 前面加上目前的時間。 寫入主機程式的效果並不一定。 大部分裝載 Windows PowerShell 的程式會將這些訊息寫入標準輸出。|
|Write-VerboseWithTime|寫入前面會加上目前時間的詳細資訊訊息。 因為它會呼叫 **Write-verbose**, ，則會顯示訊息僅指令碼執行時使用 **詳細資訊** 參數或當 **VerbosePreference** 喜好設定設為 **繼續**。|

**Publish-WebApplication**

|函式名稱|說明|
|---|---|
|New-AzureWebApplicationEnvironment|建立 Azure 資源，例如網站或虛擬機器。|
|New-WebDeployPackage|此函式未實作。 您可以在此函式新增命令來建置專案。|
|Publish-AzureWebApplication|將 Web 應用程式發佈至 Azure。|
|Publish-WebApplication|建立並部署 Visual Studio Web 專案的 Web Apps、虛擬機器、SQL Database 和儲存體帳戶。|
|Test-WebApplication|此函式未實作。 您可以在此函式新增命令來測試應用程式。|

## 後續步驟

深入了解 PowerShell 指令碼讀取 [使用 Windows PowerShell 撰寫指令碼](https://technet.microsoft.com/library/bb978526.aspx) ，並查看其他 Azure PowerShell 指令碼在 [指令碼中心](https://azure.microsoft.com/documentation/scripts/)。

