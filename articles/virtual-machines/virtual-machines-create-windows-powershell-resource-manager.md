<properties
    pageTitle="建立 Windows VM | Microsoft Azure"
    description="使用 Azure PowerShell 和資源管理員範本，輕鬆建立新的 Windows 虛擬機器。"
    services="virtual-machines"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="davidmu"/>

# 以資源管理員和 PowerShell 建立 Windows VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](virtual-machines-ps-create-preconfigure-windows-vms.md)。

這個主題描述如何使用 Azure 資源管理員和 PowerShell 快速建立 Microsoft Azure 虛擬機器。

## 建立 Windows 虛擬機器

如果您已安裝 Azure PowerShell，其必須是 Azure PowerShell 1.0.0 或更新的版本。 您可以在 Azure PowerShell 命令提示字元下使用這個命令來檢查已安裝的 Azure PowerShell 版本。

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

首先，您必須使用此命令登入 Azure。

    Login-AzureRmAccount

在 [Microsoft Azure 登入] 對話方塊中，指定 Azure 帳戶的電子郵件地址和密碼。

接下來，如果您有多個 Azure 訂用帳戶，請設定 Azure 訂用帳戶。 如果想查看目前的訂閱帳戶清單，請執行這個命令。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

現在，取代引號中，包括裡面 < 和 > 個字元，包含正確的訂用帳戶名稱，然後執行這些命令。

    $subscrName="<subscription name>"
    Select-AzureRmSubscription -SubscriptionName $subscrName –Current

接下來，請建立儲存體帳戶 您選取的名稱不可以和其他名稱重複而且只能使用小寫字母和數字。 您可以使用這個命令測試儲存體帳戶名稱是否不重複。

    Test-AzureName -Storage <Proposed storage account name>

如果這個命令傳回「False」，表示您設定的名稱不重複。

您必須指定 Azure 資料中心的位置。 要取得 Azure 資料中心清單，請執行這個命令。

    Get-AzureLocation | sort Name | Select Name

現在，將以下的 PowerShell 命令區塊複製到文字編輯器中。 填寫您選擇的儲存體帳戶和位置，取代引號中，包括裡面 < 和 > 字元。

    $stName = "<chosen storage account name>"
    $locName = "<chosen Azure location name>"
    $rgName = "TestRG"
    New-AzureRmResourceGroup -Name $rgName -Location $locName
    $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
    $vnet = New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    $vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

最後，將上述命令複製到剪貼簿，然後按滑鼠右鍵，開啟 Azure PowerShell 命令提示字元。 這樣發出的命令集就像是一連串的 PowerShell 命令，系統會提示您輸入本機系統管理員帳戶的名稱和密碼，然後建立 Azure 虛擬機器。

以下是該檔案的範例：

    PS C:\> $stName="contosost"
    PS C:\> $locName="West US"
    PS C:\> $rgName="TestRG"
    PS C:\> New-AzureRmResourceGroup -Name $rgName -Location $locName
    VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


    ResourceGroupName : TestRG
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *

    ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


    PS C:\> $storageAcc=New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
    PS C:\> $singleSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
    PS C:\> $vnet=New-AzureRmVirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    PS C:\> $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    PS C:\> $vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
    PS C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    PS C:\> $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    PS C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
    PS C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
    PS C:\> New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm


    EndTime             : 4/28/2015 1:00:05 PM -07:00
    Error               :
    Output              :
    StartTime           : 4/28/2015 12:52:52 PM -07:00
    Status              : Succeeded
    TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
    RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
    StatusCode          : OK

## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure Resource Manager 概觀](resource-group-overview.md)

[利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[以 Powershell 和傳統部署模型建立 Windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](install-configure-powershell.md)

