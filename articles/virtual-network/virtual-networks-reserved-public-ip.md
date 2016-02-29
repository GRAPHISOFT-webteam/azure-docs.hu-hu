<properties 
   pageTitle="保留的 IP |Microsoft Azure"
   description="了解保留的 IP 以及如何管理"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 保留的 IP 概觀
Azure 中的 IP 位址分為兩個類別：動態和保留。 依預設由 Azure 管理的公用 IP 位址是動態的。 這表示當資源時關閉或解除配置時，用於指定雲端服務 (VIP)，或直接存取 VM 或角色執行個體 (ILPIP) 的 IP 位址可以隨時變更。

若要防止 IP 位址變更，您可以保留 IP 位址。 保留的 IP 僅能用作 VIP，即使資源都關閉或解除配置，也能確保雲端服務的 IP 位址將會相同。 此外，您可以轉換現有的動態 IP，作為保留的 IP 位址的 VIP。

## 何時需要保留的 IP？
- **您想要確保 IP 會保留在您的訂閱**。 如果您想要保留 IP 位址，使其在任何情況下將不會從訂用帳戶釋放，您應該使用保留的公用 IP。  
- **您想要繼續使用您的雲端服務之間甚至停止或解除配置狀態 (Vm) IP**。 如果您想要讓服務可以使用 IP 位址來存取，且即使雲端服務中的 VM 已停止或解除配置，該 IP 位址也不會變更。
- **您想要確保源自 Azure 的輸出流量使用可預測的 IP 位址**。 您可能必須設定內部部署防火牆，以便僅允許來自特定 IP 位址的流量。 藉由保留 IP，您將會知道來源 IP 位址，且不必因為 IP 變更而更新您的防火牆規則。

## 常見問題集
1. 我可以針對所有 Azure 服務使用保留的 IP 嗎？  
  - 保留的 IP 僅可用於 VM 和雲端服務透過 VIP 公開的執行個體角色。
1. 我可以有多少保留的 IP？  
  - 目前，所有 Azure 訂用帳戶已獲授權可使用 20 個保留的 IP。 不過，您可以要求其他保留的 IP。 請參閱 [訂用帳戶和服務限制](../azure-subscription-service-limits/) 如需詳細資訊。
1. 保留的 IP 是否會收取費用？ 
  - 請參閱 [保留 IP 位址定價詳細資料](http://go.microsoft.com/fwlink/?LinkID=398482) 如需定價資訊。
1. 我該如何保留 IP 位址？ 
  - 您可以使用 PowerShell 或 [Azure 管理 REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) 從特定區域要求保留的 IP。 Azure 將保留該區域的 IP 位址，並與您的訂用帳戶相互關聯。 接著您可以在該區域使用保留的 IP。 您無法使用管理入口網站來保留 IP 位址。
1. 我可以使用這個搭配以同質群組為基礎的 VNet 嗎？ 
  - 保留的 IP 僅在區域 VNet 才受支援。 不支援與同質群組相關聯的 VNet。 如需有關將 VNet 與區域或同質群組產生關聯的詳細資訊，請參閱 [關於區域 Vnet 和同質群組](virtual-networks-migrate-to-regional-vnet.md)。 

## 如何管理保留的 VIP

您必須將保留的 IP 新增至訂用帳戶才能使用。 若要從中可用的公用 IP 位址集區建立保留的 IP *中部* 位置，執行下列 PowerShell 命令:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

但是請注意，您無法指定正在保留的 IP。 若要檢視哪些 IP 位址會保留在您的訂閱，請執行下列 PowerShell 命令，並注意值 *ReservedIPName* 和 *位址*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                : 
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          : 
    DeploymentName       : 
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

一旦保留 IP，其就會與您的訂用帳戶相關聯，直到刪除為止。 若要刪除如上所示之保留的 IP，請執行下列 PowerShell 命令：

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## 如何建立保留的 IP 至新雲端服務的關聯
下面的指令碼會建立新保留的 IP，然後將其關聯至新的雲端服務，名為 *TestService*。

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] 當您建立保留的 IP 至雲端服務中使用時，您仍然需要參照此 VM 使用 *VIP: & lt; 連接埠號碼 >* 針對輸入通訊。 保留 IP 並不表示您可以直接連接至 VM。 保留的 IP 會指派給已部署 VM 的雲端服務。 如果您想要透過 IP 直接連接到 VM，您必須設定執行個體層級公用 IP。 執行個體層級公用 IP 是一種公用 IP 類型 (稱為 ILPIP)，其會直接指派給您的 VM。 此類型 IP 無法保留。 請參閱 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 如需詳細資訊。

## 如何從執行中部署移除保留的 IP
若要針對上述指令碼中建立的新服務，移除新增至其中之保留的 IP，請執行下列 PowerShell 命令：

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] 從執行中部署移除保留的 IP 並不會從您的訂用帳戶移除保留項目。 這僅會釋出 IP，以便訂用帳戶中的其他資源可以使用。

## 如何建立保留的 IP 至執行中部署的關聯
下面的指令碼會建立新的雲端服務，名為 *TestService2* 名為的新 vm *TestVM2*, ，然後建立名為現有的保留的 IP 的關聯和 *MyReservedIP* 至雲端服務。

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## 如何使用服務組態檔建立保留的 IP 至雲端服務的關聯
您也可以使用服務組態 (CSCFG) 檔建立保留的 IP 至雲端服務的關聯。 下列範例 xml 示範如何設定雲端服務，以使用保留的 VIP，名為 *MyReservedIP*: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## 後續步驟

- 深入了解 [保留私人 IP 位址](../virtual-networks-reserved-private-ip)。

- 深入了解 [執行個體層級公用 IP (ILPIP) 位址](../virtual-networks-instance-level-public-ip)。

- 檢查 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

