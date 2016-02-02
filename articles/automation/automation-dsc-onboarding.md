<properties 
   pageTitle="上架由 Azure 自動化 DSC 管理的實體和虛擬機器 | Microsoft Azure" 
   description="如何設定機器以使用 Azure 自動化 DSC 管理" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="11/23/2015"
   ms.author="coreyp"/>


# 上架由 Azure 自動化 DSC 管理的機器

## 為什麼要使用 Azure 自動化 DSC 管理機器？

像 [PowerShell 期望狀態組態](https://technet.microsoft.com/library/dn249912.aspx), ，Azure 自動化期望狀態組態是簡單但強大的功能，設定管理服務 (實體和虛擬機器) 的 DSC 節點的任何雲端或內部部署資料中心內。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 Azure 自動化 DSC 管理層之於 DSC 如同 Azure 自動化管理層之於 PowerShell 指令碼。 換句話說，在 Azure 自動化可協助您管理 Powershell 指令碼的相同方式，它也可協助您管理 DSC 組態，以深入了解使用 Azure 自動化 DSC 的優點，請參閱 [Azure 自動化 DSC 概觀](automation-dsc-overview/)。

Azure 自動化 DSC 可以用來管理各種不同的機器：

*    Azure virtual machines (classic)

*    Azure virtual machines

*    Physical / virtual Windows machines on-premises, or in a cloud other than Azure

*    Physical / virtual Linux machines on-premises, in Azure, or in a cloud other than Azure


下列各節概述如何將每個類型的機器上架到 Azure 自動化 DSC。

## Azure 虛擬機器 (傳統)

利用 Azure 自動化 DSC，您可以輕鬆上架 Azure 虛擬機器 (傳統)，以使用 Azure 入口網站或 PowerShell 進行組態管理。 在幕後並且不需要系統管理員遠端連至 VM 的情況下，Azure VM 期望的狀態組態延伸模組會向 Azure 自動化 DSC 註冊 VM。 因為 Azure VM Desired State Configuration 延伸模組是以非同步方式執行，追蹤其進度，或對此進行疑難排解的步驟中所提供 [* * 疑難排解 Azure 虛擬機器上架 * *](#troubleshooting-azure-virtual-machine-onboarding) 下一節。


### Azure 入口網站

在 [Azure 預覽入口網站](http://portal.azure.com/), ，按一下 [ **瀏覽** ]-> [ **虛擬機器 (傳統)**。選取您要上架的 Windows VM。在虛擬機器的儀表板刀鋒視窗上，按一下 [所有設定]**** -> [延伸模組]**** -> [新增]**** -> [Azure Automation DSC]**** -> [建立]****。輸入 [PowerShell DSC 本機組態管理員值](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) 指派給 VM 所需使用案例中，您的自動化帳戶註冊金鑰和註冊 URL，並選擇性節點組態。

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


尋找註冊 URL 和金鑰來自動化帳戶的內建電腦，請參閱 [* * 安全註冊 * *](#secure-registration) 下一節。

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Login-AzureRmAccount
    
    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""
    
    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""
    
    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo
    
    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"
    
    # update these DSC agent Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
       RegistrationKey = @{
         UserName = 'notused'
         Password = 'PrivateSettingsRef:RegistrationKey'
    }
      RegistrationUrl = $RegistrationInfo.Endpoint
      NodeConfigurationName = $NodeConfigName
      ConfigurationMode = "ApplyAndMonitor"
      ConfigurationModeFrequencyMins = 15
      RefreshFrequencyMins = 30
      RebootNodeIfNeeded = $False
      ActionAfterReboot = "ContinueConfiguration"
      AllowModuleOverwrite = $False
      }
    }
    
    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.6 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration `
     -ForceUpdate
    
    $VM | Update-AzureVM

## Azure 虛擬機器

Azure 自動化 DSC 可讓您輕鬆上架 Azure 虛擬機器以進行組態管理，請使用 Azure 入口網站、Azure 資源管理員範本或 PowerShell。 在幕後並且不需要系統管理員遠端連至 VM 的情況下，Azure VM 期望的狀態組態延伸模組會向 Azure 自動化 DSC 註冊 VM。 因為 Azure VM Desired State Configuration 延伸模組是以非同步方式執行，追蹤其進度，或對此進行疑難排解的步驟中所提供 [* * 疑難排解 Azure 虛擬機器上架 * *](#troubleshooting-azure-virtual-machine-onboarding) 下一節。


### Azure 入口網站

在 [Azure 預覽入口網站](http://portal.azure.com/), ，瀏覽至要建立的虛擬機器的 Azure 自動化帳戶。 在 [自動化帳戶] 儀表板上，按一下 [DSC 節點]**** -> [新增 Azure VM]****。

在 [選取要上架的虛擬機器]**** 下，選取一或多個要上架的 Azure 虛擬機器。

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


在 **設定註冊資料**, ，輸入 [PowerShell DSC 本機組態管理員值](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) 您的使用案例，並選擇性節點組態才能將指派給 VM。

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)


### Azure 資源管理員範本

您可以透過 Azure 資源管理員範本部署 Azure 虛擬機器和上架到 Azure 自動化 DSC。 請參閱 [設定透過 DSC 延伸模組的 VM 和 Azure 自動化 DSC](http://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) 如需範例範本該 onboards 到 Azure 自動化 DSC 現有的 VM。 若要尋找的註冊金鑰和所採取的註冊 URL 做為此範本中的輸入，請參閱 [* * 安全註冊 * *](#secure-registration) 下一節。

### PowerShell

[註冊 AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) 指令程式可以用來透過 PowerShell 的 Azure 預覽入口網站中建立的虛擬機器。

## 實體/虛擬 Windows 電腦內部部署，或在 Azure 以外的雲端中

內部部署 Windows 電腦和非 Azure 雲端中的 Windows 電腦 (例如 Amazon Web Services) 也可以上架到 Azure 自動化 DSC，只要它們對外可存取網際網路，透過一些簡單的步驟：

1. 請確定最新版本的 [WMF 5](http://www.microsoft.com/en-us/download/details.aspx?id=48729) 要上架到 Azure 自動化 DSC 的機器上安裝。

2. 在您的本機環境的機器中，以系統管理員身分開啟 PowerShell 主控台或 PowerShell ISE。 這台電腦也必須安裝最新版本的 WMF 5。

3. 使用 Azure PowerShell 模組連接至 Azure 資源管理員：
        `登入 AzureRmAccount`

4. 從您要上架節點的目標自動化帳戶下載您想要上架的電腦的 PowerShell DSC 中繼組態：

    `Get AzureRmAutomationDscOnboardingMetaconfig-ResourceGroupName MyResourceGroup-AutomationAccountName MyAutomationAccount-ComputerName MyServer1，MyServer2-OutputFolder C:\Users\joe\Desktop`

5. (選擇性) 檢視和更新的輸出資料夾中 metaconfigurations，配合 [PowerShell DSC 本機組態管理員欄位和值](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) 想，如果預設值不符合您的使用案例。

6. 從遠端將 PowerShell DSC metaconfiguration 套用至您想要上架的電腦：

    `Set-dsclocalconfigurationmanager-路徑 C:\Users\joe\Desktop\DscMetaConfigs-ComputerName MyServer1、 MyServer2`

7. 如果您無法從遠端套用 PowerShell DSC 中繼組態，請複製步驟 4 的輸出資料夾到每一部要上架的電腦。 然後在要上架的每台機器本機上呼叫 Set-DscLocalConfigurationManager。

8. 使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中顯示為已註冊的 DSC 節點。

## 內部部署在 Azure 中或 Azure 以外的雲端中的實體/虛擬 Linux 機器

內部部署 Linux 電腦、Azure 中的 Linux 電腦和非 Azure 雲端中的 Linux 電腦也可以上架到 Azure Automation DSC，只要它們對外可存取網際網路，透過一些簡單的步驟：

1. 請確定最新版本的 [DSC Linux 代理程式](http://www.microsoft.com/en-us/download/details.aspx?id=49150) 要上架到 Azure 自動化 DSC 的機器上安裝。

2. 如果 [PowerShell DSC 本機組態管理員的預設值](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) 符合您的使用案例:

    *    On each Linux machine to onboard to Azure Automation DSC, use Register.py to onboard using the PowerShell DSC Local Configuration Manager defaults:

        `/opt/microsoft/dsc/Scripts/Register.py < 自動化帳戶註冊金鑰 >< 自動化帳戶註冊 URL >`

    *    To find the registration key and registration URL for your Automation account, see the [**Secure registration**](#secure-registration) section below.

    如果 PowerShell DSC 本機設定管理員預設值**不******符合您的使用情況，請遵循步驟 3 到 9。 否則直接跳到步驟 9。

3. 在您的本機環境的 Windows 機器上，以系統管理員身分開啟 PowerShell 主控台或 PowerShell ISE。 這台電腦必須安裝最新版的 [WMF 5](http://www.microsoft.com/en-us/download/details.aspx?id=48729) 安裝。

4. 使用 Azure PowerShell 模組連接至 Azure 資源管理員：

    `登入 AzureRmAccount`

5.  從您要上架節點的目標自動化帳戶下載您想要上架的電腦的 PowerShell DSC 中繼組態：

    `Get AzureRmAutomationDscOnboardingMetaconfig-ResourceGroupName MyResourceGroup-AutomationAccountName MyAutomationAccount-ComputerName MyServer1，MyServer2-OutputFolder C:\Users\joe\Desktop_`

6.  (選擇性) 檢視和更新的輸出資料夾中 metaconfigurations，配合 [PowerShell DSC 本機組態管理員欄位和值](http://https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) 想，如果預設值不符合您的使用案例。

7.  從遠端將 PowerShell DSC metaconfiguration 套用至您想要上架的電腦：

    $SecurePass = ConvertTo-SecureString -string "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurPass
    $Opt = New-CimSessionOption -UseSs1:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true
    
    # need a CimSession for each Linux machine to onboard
    
    $Session = New-CimSession -Credential:$Cred -ComputerName:<your Linux machine> -Port:5986 -Authentication:basic -SessionOption:$Opt
    
    Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs

8.  如果您無法從遠端套用 PowerShell DSC 中繼設定，針對要上架的每部 Linux 電腦，請從步驟 5 的資料夾複製對應於該電腦的中繼組態到 Linux 電腦。 然後呼叫 `SetDscLocalConfigurationManager.py` 在本機上每一部 Linux 機器要上架到 Azure 自動化 DSC:

    `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py – configurationmof < metaconfiguration 檔案路徑 >`

9.  使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中顯示為已註冊的 DSC 節點。

## 安全註冊

機器可以透過 WMF 5 DSC 註冊通訊協定安全地上架到 Azure 自動化帳戶，如此可讓 DSC 節點向 PowerShell DSC V2 提取或報表伺服器 (包括 Azure 自動化 DSC) 進行驗證。 節點會在**註冊 URL**時向伺服器註冊，並使用**註冊金鑰**驗證。 在註冊期間，DSC 節點和 DSC 提取/報告伺服器會交涉獨特的憑證，讓此節點在註冊伺服器用於進行驗證。 此程序可避免上架的節點彼此模擬，例如當節點遭到入侵並且具有惡意行為。 註冊之後，註冊金鑰不會再次用於驗證，並且會從節點中刪除。

您可以從 Azure Preview 入口網站的 [管理金鑰]**** 刀鋒視窗取得 DSC 註冊通訊協定所需的資訊。 在自動化帳戶的 [基本功能]**** 面板按一下金鑰圖示，可開啟此刀鋒視窗。

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    Registration URL is the URL field in the Manage Keys blade.

*    Registration key is the Primary Access Key or Secondary Access Key in the Manage Keys blade. Either key can be used.


為了提高安全性，自動化帳戶的主要和次要存取金鑰可以隨時重新產生 (在 [管理金鑰]**** 刀鋒視窗上)，以避免未來的節點使用先前的金鑰註冊。

## 疑難排解 Azure 虛擬機器上架

Azure Automation DSC 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。 在幕後，Azure VM 期望的狀態組態延伸模組是用來向 Azure 自動化 DSC 註冊 VM。 因為 Azure VM 期望的狀態組態延伸模組是以非同步方式執行，追蹤其進度和疑難排解其執行可能很重要。
>[AZURE.NOTE] 將 Azure Windows VM 上架到使用 Azure VM 期望的狀態組態延伸模組的 Azure 自動化 DSC 的任何方法，最多可能需要一小時的時間，節點才會顯示為已在 Azure 自動化中註冊。 這是因為 VM 上憑藉著 Azure VM DSC 延伸模組的 Windows Management Framework 5.0 安裝，需要它才能將 VM 上架到 Azure 自動化 DSC。

若要疑難排解或檢視 Azure VM 預期狀態設定延伸模組的狀態，在 Azure Preview 入口網站中，瀏覽至正在上架的 VM，然後按一下 -> [所有設定]**** -> [延伸模組]**** -> [DSC]****。 如需詳細資訊，您可以按一下 [檢視詳細狀態]****。

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)] (https://technet.microsoft.com/library/dn249912.aspx)

## 憑證到期日和重新註冊

在註冊之後，每個節點會自動交涉唯一的驗證憑證，該憑證於一年之後到期。 目前，當憑證即將過期時，PowerShell DSC 註冊通訊協定便無法自動更新憑證，因此您必須在一年之後重新註冊這些節點。 在重新登錄之前，請確定每個節點都正在執行 Windows Management Framework 5.0 RTM。 如果節點的驗證憑證過期，而且該節點尚未註冊，則該節點將無法與 Azure 自動化通訊，並將標示為「未回應」。 註冊執行方式與您一開始註冊節點時相同。 與憑證到期時間相距 90 天或更短時間內執行的註冊，或是憑證到期時間之後任何時間點執行的註冊，將會產生新的憑證並予以使用。

## 相關文章

* [Azure 自動化 DSC 概觀](automation-dsc-overview.md)
* [Azure 自動化 DSC 指令程式](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure 自動化 DSC 定價](http://azure.microsoft.com/pricing/details/automation/)








