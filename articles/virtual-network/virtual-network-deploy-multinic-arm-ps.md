<properties 
   pageTitle="在資源管理員中使用 PowerShell 部署多個 NIC VM | Microsoft Azure"
   description="了解如何在資源管理員中使用 PowerShell 部署多個 NIC VM"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

#使用 PowerShell 部署多個 NIC VM

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](virtual-network-deploy-multinic-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

因為在這個時間點，您不能讓配備單一 NIC 的 VM 與配備多個 NIC 的 VM 位在同一個資源群組中，您要在與所有其他元件不同的資源群組中實作後端伺服器。 下列步驟使用的資源群組名稱 *IaaSStory* 主要資源群組，以及 *IaaSStory 後端* 的後端伺服器。

## 必要條件

您必須為此案例部署含有所有必要資源的主要資源群組，然後才可以部署後端伺服器。 若要部署這些資源，請遵循下列步驟。

1. 瀏覽至 [範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在右邊的 [範本] 頁面中 **父資源群組**, ，按一下 [ **部署至 Azure**。
3. 視需要變更參數值，然後依照 Azure Preview 入口網站中的步驟部署資源群組。

> [AZURE.IMPORTANT] 請確定您的儲存體帳戶名稱是唯一的。 在 Auzre 中不能有重複的儲存體帳戶名稱。 

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 部署後端 VM

後端 VM 有賴於建立下列資源。

- **資料磁碟的儲存體帳戶**。 為取得更佳的效能，資料庫伺服器上的資料磁碟會使用需要進階儲存體帳戶的固態硬碟 (SSD) 技術。 請確定 Azure 的部署位置，以支援進階儲存體。
- **Nic**。 每部 VM 都會有兩個 NIC，一個用於資料庫存取，另一個用於管理。
- **可用性設定組**。 所有的資料庫伺服器都會加入單一的可用性設定組，確保在維護期間至少有一部 VM 啟動並執行。  

### 步驟 1：啟動指令碼

您可以下載完整的 PowerShell 指令碼使用 [這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/multinic.ps1)。 請遵循下列步驟來變更指令碼來讓指令碼在環境中運作。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 變更變數的值以下根據您現有的資源群組中部署上述 [必要條件](#Prerequisites)。

        $existingRGName        = "IaaSStory"
        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"
        $remoteAccessNSGName   = "NSG-RemoteAccess"
        $stdStorageAccountName = "wtestvnetstoragestd"

2. 根據後端部署要使用的值，變更下列變數值。

        $backendRGName         = "IaaSStory-Backend"
        $prmStorageAccountName = "wtestvnetstorageprm"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $publisher             = "MicrosoftSQLServer"
        $offer                 = "SQL2014SP1-WS2012R2"
        $sku                   = "Standard"
        $version               = "latest"
        $vmNamePrefix          = "DB"
        $osDiskPrefix          = "osdiskdb"
        $dataDiskPrefix        = "datadisk"
        $nicNamePrefix         = "NICDB"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

3. 擷取部署所需的現有資源。

        $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
        $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
        $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
        $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### 步驟 2：為 VM 建立必要的資源

您需要建立新的資源群組、資料磁碟的儲存體帳戶，以及所有 VM 的可用性設定組。 您也需要每部 VM 的本機系統管理員帳戶認證。 若要建立這些資源，請執行下列步驟。

1. 建立新的資源群組。

        New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. 在上述建立的資源群組中建立新的進階儲存體帳戶。

        $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
            -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. 建立新的可用性設定組。

        $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. 取得每部 VM 要使用的本機系統管理員帳戶認證。

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

### 步驟 3：建立 NIC 和後端 VM

您需要使用迴圈建立所需數量的 VM，並在迴圈中建立必要的 NIC 和 VM。 若要建立 NIC 和 VM，請執行下列步驟。

1. 啟動 `for` 迴圈，根據 `$numberOfVMs` 變數的值，視需要的次數重複命令來建立一部 VM 和兩個 NIC。

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. 建立用於資料庫存取的 NIC。
        
            $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
            $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
            $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. 建立用於遠端存取的 NIC。 請注意，此 NIC 與 NSG 關聯的方式。

            $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
            $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
            $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
                -NetworkSecurityGroupId $remoteAccessNSG.Id

4. 建立 `vmConfig` 物件。

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. 每部 VM 建立兩個資料磁碟。 請注意，資料磁碟位於稍早建立的進階儲存體帳戶中。

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
                -VhdUri $data1VhdUri -CreateOption empty -Lun 0
        
            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"    
            $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
                -VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. 設定作業系統，以及要用於 VM 的映像。
            
            $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
            $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. 將上述建立的兩個 NIC 加到 `vmConfig` 物件。

            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. 建立作業系統磁碟並建立 VM。 請注意，`}` 會結束 `for` 迴圈。 

            $osDiskName = $vmName + "-" + $osDiskSuffix
            $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
            $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
            New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
        }

### 步驟 4：執行指令碼

既然您已根據需求下載並變更指令碼，請執行指令碼來建立有多個 NIC 的後端資料庫 VM。

1. 儲存您的指令碼並執行從 **PowerShell** 命令提示字元，或 **PowerShell ISE**。 您會看到初始的輸出，如下所示。

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        Permissions       : 
                            Actions  NotActions
                            =======  ==========
                            *                  
                            
        ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. 請稍候幾分鐘，填寫 [認證提示並按一下 **確定**。 以下的輸出代表單一 VM。 請注意，整個程序花費 8 分鐘完成。

        ResourceGroupName            : 
        Id                           : 
        Name                         : DB2
        Type                         : 
        Location                     : 
        Tags                         : 
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   : 
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 : 
        InstanceViewText             : null
        NetworkProfile               : 
        NetworkProfileText           : null
        OSProfile                    : 
        OSProfileText                : null
        Plan                         : 
        PlanText                     : null
        ProvisioningState            : 
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          : 
        RequestId                    : 
        StatusCode                   : 0
        
        
        ResourceGroupName            : 
        Id                           : 
        Name                         : DB2
        Type                         : 
        Location                     : 
        Tags                         : 
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   : 
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 : 
        InstanceViewText             : null
        NetworkProfile               : 
        NetworkProfileText           : null
        OSProfile                    : 
        OSProfileText                : null
        Plan                         : 
        PlanText                     : null
        ProvisioningState            : 
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          : 
        RequestId                    : 
        StatusCode                   : 0
        
        
        EndTime             : 10/30/2015 9:30:03 AM -08:00
        Error               : 
        Output              : 
        StartTime           : 10/30/2015 9:22:54 AM -08:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK




