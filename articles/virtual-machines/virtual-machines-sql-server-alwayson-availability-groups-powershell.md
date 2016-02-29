<properties
    pageTitle="在 Azure VM 中設定 AlwaysOn 可用性群組 | Microsoft Azure"
    description="本教學課程會使用傳統部署模型，以建立資源，並使用 PowerShell 在 Azure 中建立 AlwaysOn 可用性群組。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="12/04/2015"
    ms.author="jroth" />

# 在 Azure VM 中設定 AlwaysOn 可用性群組 (PowerShell)

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


Azure 虛擬機器 (VM) 可協助資料庫管理員以較低的成本實作高可用性 SQL Server 系統。 本教學課程將示範如何使用 Azure 環境中的 SQL Server AlwaysOn 端對端實作可用性群組。 在本教學課程結束時，您 Azure 中的 SQL Server AlwaysOn 解決方案將包含下列項目：

- 包含多個子網路 (前端和後端子網路) 的虛擬網路

- 具有 Active Directory (AD) 網域的網域控制站

- 兩個 SQL Server VM 已部署至後端子網路並加入 AD 網域

- 具有節點多數仲裁模型的 3 節點 WSFC 叢集

- 具有兩份可用性資料庫同步認可複本的可用性群組

選擇此案例是基於其單純性，而非因為在 Azure 中具有成本效益或其他因素。 比方說，您可以將雙複本可用性群組的 VM 數量減到最少，以縮短在 Azure 中的運算時數，方法是在 2 節點 WSFC 叢集中使用網域控制站作為仲裁檔案共用見證。 此方法可讓上述組態減少一個 VM。

本教學課程的目的是示範設定上述解決方案所需執行的步驟，但不會闡述每個步驟的細節內容。 因此，本教學課程會使用 PowerShell 指令碼帶您快速進行每個步驟，但不會顯示 GUI 組態步驟。 本教學課程假設您已具備下列條件：

- 您已經有訂閱虛擬機器訂用帳戶的 Azure 帳戶。

- 您已安裝 [Azure PowerShell cmdlet](..\powershell-install-configure.md)。

- 您已經知道如何透過 GUI 佈建來自虛擬機器資源庫的 SQL Server VM。 如需詳細資訊，請參閱 [佈建 Azure 上的 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)

- 您已非常熟悉內部部署解決方案的 AlwaysOn 可用性群組的功能。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

## 連接至您的 Azure 訂用帳戶並建立虛擬網路

1. 在您本機電腦上的 [PowerShell] 視窗中匯入 Azure 模組，再將發佈設定檔案下載至您的電腦，然後透過匯入所下載的發佈設定，將 PowerShell 工作階段連接至您的 Azure 訂用帳戶。

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

     **Get AzurePublishgSettingsFile** 命令自動產生管理使用 Azure 的憑證會下載檔案至您的電腦。 瀏覽器會自動開啟，並提示您輸入 Azure 訂用帳戶的 Microsoft 帳戶認證。 下載 **.publishsettings** 檔案包含您要管理您的 Azure 訂閱的所有資訊。 將此檔案儲存到本機目錄之後, 將它匯入使用 **Import-azurepublishsettingsfile** 命令。

    >[AZURE.NOTE] Publishsettings 檔案包含的認證 (未編碼) 用來管理 Azure 訂用帳戶和服務。 這個檔案的安全性最佳作法是暫時儲存在來源目錄之外 (例如在 Libraries\Documents 資料夾)，然後在匯入完成後予以刪除。 惡意使用者若取得 .publishsettings 檔案的存取權，就可以編輯、建立和刪除您的 Azure 服務。

1. 定義一系列可用來建立雲端 IT 基礎結構的變數。

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    請注意下列事項，以確保稍後執行命令時，能成功發揮作用。

    - 變數 **$storageAccountName** 和 **$dcServiceName** 必須是唯一，因為它們用來識別您的雲端儲存體帳戶和雲端伺服器，分別在網際網路上。

    - 指定變數名稱 **$affinityGroupName** 和 **$virtualNetworkName** 稍後您將使用的虛擬網路組態文件中所設定。

    - **$sqlImageName** 指定包含 SQL Server 2012 Service Pack 1 Enterprise Edition 之 VM 映像的更新的名稱。

    - 為了簡單起見， **Contoso! 000** 整個教學課程所用的相同密碼。

1. 建立同質群組。

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. 匯入組態檔以建立虛擬網路。

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    組態檔包含下列 XML 文件。 簡單地說，它會指定虛擬網路稱為 **{1>contosoag** 同質群組中呼叫 **ContosoAG**, ，且位址空間 **10.10.0.0/16** 兩個子網路，且 **10.10.1.0/24** 和 **10.10.2.0/24**, ，這分別是端子網路和後端子。 前端網路是放置 Microsoft SharePoint 之類的用戶端應用程式的位置，後端網路則是放置 SQL Server VM 的位置。 如果您變更 **$affinityGroupName** 和 **$virtualNetworkName** 變數，您也必須變更下列對應名稱。

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. 建立與您所建立之同質群組相關聯的儲存體帳戶，並將其設為訂用帳戶目前的儲存體帳戶。

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. 建立新雲端服務和可用性集合中的 DC 伺服器。

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    這個已輸送的命令系列可執行下列動作：

    - **新 AzureVMConfig** 建立 VM 組態。

    - **新增 AzureProvisioningConfig** 提供獨立的 Windows 伺服器的組態參數。

    - **新增 AzureDataDisk** 新增資料磁碟，您將用於儲存 Active Directory 資料，與快取選項設定為 None。

    - **New-azurevm** 會建立新的雲端服務，並在新的雲端服務中建立新的 Azure VM。

1. 等候系統完整佈建新 VM ，並將遠端桌面檔案下載至您的工作目錄。 因為佈建新的 Azure VM 需要很長的時間，所以 while 迴圈會持續輪詢新的 VM 直到該 VM 準備就緒。

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

現在已成功佈建 DC 伺服器。 接下來，您將在這個 DC 伺服器上設定 Active Directory 網域。 讓 [PowerShell] 視窗在本機電腦上保持開啟。 稍後需使用用該視窗建立兩個 SQL Server VM。

## 設定網域控制站。

1. 啟動遠端桌面檔案，以連接至 DC 伺服器。 使用電腦系統管理員使用者名稱 {1>azureadmin 和密碼 **Contoso! 000**, ，在建立新的 VM 時，您所指定。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

1. 執行下列命令 **DCPROMO。EXE** 命令來安裝 **corp.contoso.com** M 磁碟機上的資料目錄的網域。

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    命令完成後，VM 便會自動重新啟動。

1. 啟動遠端桌面檔案，再次連接至 DC 伺服器。 這次，以登入 **CORP\Administrator**。

1. 在系統管理員模式中開啟 [PowerShell] 視窗，並透過下列命令匯入 Active Directory PowerShell 模組：

        Import-Module ActiveDirectory

1. 執行下列命令以將三個使用者新增至網域。

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** 用來設定 SQL Server 服務執行個體、 WSFC 叢集和可用性群組相關的所有項目。 **CORP\SQLSvc1** 和 **CORP\SQLSvc2** 作為兩個 SQL Server Vm 的 SQL Server 服務帳戶。

1. 接下來，執行下列命令，以提供 **CORP\Install** 網域中建立電腦物件的權限。

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    上面指定的 GUID 即是電腦物件類型的 GUID。  **CORP\Install** 帳戶需求 **讀取全部內容** 和 **建立電腦物件** 權限，才能建立 WSFC 叢集的 Active Directory 物件。  **讀取全部內容** 權限已被 CORP\Install 根據預設，因此您不需要明確地授與它。 如需有關建立 WSFC 叢集所需的權限的詳細資訊，請參閱 [容錯移轉叢集逐步指南: 設定 Active Directory 中的帳戶](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)。

    現在 Active Directory 和使用者物件便已設定完畢，請建立兩個 SQL Server VM，並將這些 VM 加入此網域。

## 建立 SQL Server VM

1. 繼續使用在本機電腦上開啟的 [PowerShell] 視窗。 定義下列額外變數：

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP 位址 **10.10.0.4** 通常您在建立第一個 VM 指派 **10.10.0.0/16** Azure 虛擬網路的子網路。 您應該確認這是藉由執行的 DC 伺服器的位址 **IPCONFIG**。

1. 執行下列管道命令來建立第一個 VM 在 WSFC 叢集中，名為 **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    下列資訊為上述命令的相關資訊：

    - **新 AzureVMConfig** 使用所需的可用性設定組名稱建立 VM 組態。 系統會使用相同的可用性集合名稱命名之後建立的 VM，以便將它們加入相同的可用性集合。

    - **新增 AzureProvisioningConfig** 將 VM 加入您所建立的 Active Directory 網域。

    - **Set AzureSubnet** 會將 VM 放在上一步的子網路。

    - **New-azurevm** 會建立新的雲端服務，並在新的雲端服務中建立新的 Azure VM。  **Dnssettings** 參數指定新的雲端服務中的伺服器的 DNS 伺服器的 IP 位址 **10.10.0.4**, ，這是 DC 伺服器的 IP 位址。 需使用此參數，才能將雲端服務中的新 VM 成功加入 Active Directory 網域。 如果不使用此參數，就必須 在 VM 中手動進行 VM IPv4 設定，以在佈建 VM，並將該 VM 加入 Active Directory 網域後，將 DC 伺服器作為主要的 DNS 伺服器。

1. 執行下列管道命令，以建立 SQL Server Vm，名為 **ContosoSQL1** 和 **ContosoSQL2**。

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    下列資訊為上述命令的相關資訊：

    - **新 AzureVMConfig** 為 DC 伺服器時，會使用相同的可用性設定組名稱，並使用虛擬機器資源庫中的 SQL Server 2012 Service Pack 1 Enterprise Edition 映像。 也可將作業系統磁碟設為唯讀快取 (無寫入快取)。 建議您將資料庫檔案移轉至連接至 VM 的獨立資料磁碟，並將該磁碟設為無讀取或寫入快取權限。 由於無法移除讀取作業系統磁碟的讀取快取權限，所以另外一個次佳的作法就是，移除作業系統磁碟的寫入快取權限。

    - **新增 AzureProvisioningConfig** 將 VM 加入您所建立的 Active Directory 網域。

    - **Set AzureSubnet** 會將 VM 放在上一步的子網路。

    - **新增 AzureEndpoint** 加入存取端點，讓用戶端應用程式可以存取網際網路上的這些 SQL Server 服務執行個體。 系統會將不同的通訊埠指派給 ContosoSQL1 和 ContosoSQL2。

    - **New-azurevm** ContosoQuorum 相同的雲端服務中建立新的 SQL Server VM。 若想要讓所有 VM 都位於相同的可用性集合中，您必須將 VM 放置到相同的雲端服務。

1. 等候系統完整佈建新 VM ，並將其遠端桌面檔案下載至您的工作目錄。 for 迴圈會針對三個新 VM 分別執行，並針對每個 VM 執行最上層大括弧中的命令。

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    現在 SQL Server VM 已完成佈建並執行中，但這些 VM 所安裝的是使用預設值的 SQL Server。

## 初始化 WSFC 叢集 VM

在本節中，必須修改將用於 WSFC 叢集和 SQL Server 安裝中的三個伺服器。 具體而言：

- (所有伺服器)您必須安裝 **容錯移轉叢集** 功能。

- (所有伺服器)您需要新增 **CORP\Install** 與機器 **管理員**。

- (僅 ContosoSQL1 和 ContosoSQL2)您需要新增 **CORP\Install** 為 **sysadmin** 預設資料庫中的角色。

- (僅 ContosoSQL1 和 ContosoSQL2)您需要新增 **NT AUTHORITY\System** 具備下列權限的登入:

    - 更改所有可用性群組

    - 連接 SQL

    - 檢視伺服器狀態

- (僅 ContosoSQL1 和 ContosoSQL2) **TCP** SQL Server VM 上已啟用通訊協定。 但是，還是必須開啟防火牆以供 SQL Server 進行遠端存取。

您現在即可準備開始修改。 開頭為 **ContosoQuorum**, ，依照下列步驟:

1. 連接到 **ContosoQuorum** 透過啟動遠端桌面檔案。 使用電腦系統管理員使用者名稱 **{1>azureadmin** 和密碼 **Contoso! 000**, ，在建立 Vm 時，您所指定。

1. 確認電腦具有已成功加入至 **corp.contoso.com**。

1. 等候 SQL Server 安裝完成執行之前自動化的初始化工作，再繼續下一步。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

1. 安裝 [Windows 容錯移轉叢集] 功能。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. 新增 **CORP\Install** 以本機系統管理員。

        net localgroup administrators "CORP\Install" /Add

1. 登出 ContosoQuorum。 此伺服器的相關作業完成。

        logoff.exe

接下來，初始化 **ContosoSQL1** 和 **ContosoSQL2**。 針對兩個 SQL Server VM 執行下列步驟。

1. 啟動遠端桌面檔案，以連接至兩個 SQL Server VM。 使用電腦系統管理員使用者名稱 **{1>azureadmin** 和密碼 **Contoso! 000**, ，在建立 Vm 時，您所指定。

1. 確認電腦具有已成功加入至 **corp.contoso.com**。

1. 等候 SQL Server 安裝完成執行之前自動化的初始化工作，再繼續下一步。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

1. 安裝 [Windows 容錯移轉叢集] 功能。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. 新增 **CORP\Install** 以本機系統管理員

        net localgroup administrators "CORP\Install" /Add

1. 匯入 SQL Server PowerShell 提供者。

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. 新增 **CORP\Install** 成為預設 SQL Server 執行個體的 sysadmin 角色。

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. 新增 **NT AUTHORITY\System** 具備上述三項權限的登入。

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. 開啟防火牆以供 SQL Server 進行遠端存取。

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. 登出兩個 VM。

        logoff.exe

準備就緒，可以開始設定可用性群組了。 您將使用 SQL Server PowerShell 提供者上執行的所有工作 **ContosoSQL1**。

## 設定可用性群組

1. 連接到 **ContosoSQL1** 透過啟動遠端桌面檔案再次。 而不是使用登入電腦帳戶，登入使用 **CORP\Install**。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

1. 定義下列變數：

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. 匯入 SQL Server PowerShell 提供者。

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. 將 ContosoSQL1 的 SQL Server 服務帳戶變更為 CORP\SQLSvc1。

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 將 ContosoSQL2 的 SQL Server 服務帳戶變更為 CORP\SQLSvc2。

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 下載 **CreateAzureFailoverCluster.ps1** 從 [Azure VM 中的 AlwaysOn 可用性群組建立 WSFC 叢集](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) 至本機工作目錄。 此指令碼將可協助您建立功能 WSFC 叢集。 如需 WSFC 如何與 Azure 網路互動的重要資訊，請參閱 [高可用性和嚴重損壞修復的 SQL Server Azure 虛擬機器中](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

1. 變更您的工作目錄，並透過下載的指令碼建立 WSFC 叢集。

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. 預設 SQL Server 執行個體啟用 AlwaysOn 可用性群組上 **ContosoSQL1** 和 **ContosoSQL2**。

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 建立備份目錄，並將權限授與 SQL Server 服務帳戶。 此目錄將用來準備次要複本的可用性資料庫。

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. 上建立資料庫 **ContosoSQL1** 呼叫 **MyDB1**, 、 需要完整備份和記錄備份和還原 **ContosoSQL2** 與 * * WITH NORECOVERY] 選項。

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. 在 SQL Server VM 上建立可用性群組端點，並在端點上設定適當的權限。

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. 建立可用性複本。

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. 最後，建立可用性群組並將次要複本加入可用性群組。

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## 後續步驟
現在，您已透過在 Azure 中建立可用性群組的方式，成功實作 SQL Server AlwaysOn。 若要設定這個可用性群組接聽程式，請參閱 [在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)。

