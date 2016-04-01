<properties 
   pageTitle="如何使用 PowerShell 在傳統模式中設定靜態私人 IP| Microsoft Azure"
   description="了解靜態私人 IP (DIP) 以及如何在傳統模式與 PowerShell 中使用 CLI 進行管理"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 如何在 PowerShell 設定靜態私人 IP 位址 (傳統)

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [管理資源管理員部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-arm-ps)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例 PowerShell 命令會預期已經建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，先建立測試環境中所述 [建立 vnet](virtual-networks-create-vnet-classic-netcfg-ps.md)。

## 如何驗證特定 IP 位址是否可用
若要驗證 IP 位址 *192.168.1.101* 位於名為 vnet *TestVnet*, ，執行下列 PowerShell 命令，並確認的值 *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

預期的輸出：

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## 建立 VM 時如何指定靜態私人 IP 位址
下列 PowerShell 指令碼會建立新的雲端服務，名為 *TestService*, ，然後從 Azure 擷取映像，建立名為 VM *DNS01* 新的雲端服務中使用擷取的映像設定名為子網路中的 VM *前端*, ，並設定 *從 192.168.1.7* VM 的靜態私人 IP 位址為 ︰

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames FrontEnd `
  	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVNet

預期的輸出：

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## 如何擷取 VM 的靜態私人 IP 位址資訊
若要檢視使用上述指令碼建立之 VM 的靜態私人 IP 位址資訊，請執行下列 PowerShell 命令，並觀察值 *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

預期的輸出：

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## 如何移除 VM 的靜態私人 IP 位址
若要移除上述指令碼中新增至 VM 的靜態私人 IP 位址，請執行下列 PowerShell 命令：
    
    Get-AzureVM -ServiceName TestService -Name DNS01 `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

預期的輸出：

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述指令碼建立之 VM，請執行下列命令：

    Get-AzureVM -ServiceName TestService -Name DNS01 `
  	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
  	| Update-AzureVM

預期的輸出：

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## 後續步驟

- 深入了解 [保留公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

