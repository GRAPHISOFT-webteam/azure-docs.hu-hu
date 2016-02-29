<properties 
   pageTitle="執行個體層級公用 IP (ILPIP) | Microsoft Azure"
   description="了解 ILPIP (PIP) 及管理它們的方法"
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

# 執行個體層級公用 IP 概觀
執行個體層級公用 IP (ILPIP) 是您可以直接指派至 VM 或角色執行個體的 IP 位址，而不是指派至 VM 或角色執行個體所在的雲端服務。 這不會取代指派給雲端服務的 VIP (虛擬 IP)。 應該說是您可以用來直接連接到 VM 或角色執行個體的其他 IP 位址。

>[AZURE.NOTE] 在過去，ILPIP 被稱為 PIP，表示公用 IP。 

![ILPIP 和 VIP 之間的差異](./media/virtual-networks-instance-level-public-ip/Figure1.png)

如圖 1 所示，儘管通常是使用 VIP:&lt;連接埠號碼&gt; 來存取個別 VM，但還是會使用 VIP 來存取雲端服務。 將 ILPIP 指派給特定的 VM，就能直接使用該 IP 位址來存取 VM。

當您在 Azure 中建立雲端服務時，對應的 DNS A 記錄即會自動建立，以允許透過完整格式的網域名稱 (FQDN) 存取服務，而不需使用實際的 VIP。 相同程序也適用於 ILPIP，但可改為透過 FQDN 而不是 ILPIP 來允許存取 VM 或角色執行個體。 比方說，如果您建立雲端服務，名為 *contosoadservice*, ，並設定名為的 web 角色 *contosoweb* 有兩個執行個體，Azure 會登錄下列 A 記錄的執行個體:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] 您可以指派每個 VM 或角色執行個體只能有一個 ILPIP。 您可以針對每個訂用帳戶最多使用 5 個 ILPIP。 ILPIP 目前不支援多個 NIC 的 VM。

## 為什麼我應該要求 ILPIP？
如果您想要能夠連線到您的 VM 或角色執行個體直接指派給它的 IP 位址，而不是使用雲端服務 VIP: & l t; 使用量數字的連接埠; 要求 ILPIP 的 VM 或角色執行個體。
- **被動 FTP** -ILPIP 讓您的 vm，您可以接收任何連接埠的流量，您不需要開啟端點就可以接收流量。 這可讓動態選擇連接埠是被動 FTP 等案例。
- **輸出 IP** -源自 VM 的輸出流量使用 ILPIP 做為來源所送出，這可唯一識別為外部實體 VM。

## 如何在建立 VM 期間要求 ILPIP
下列 PowerShell 指令碼會建立新的雲端服務，名為 *FTPService*, ，然後從 Azure 擷取映像，並建立名為 VM *FTPInstance* 使用擷取的映像，設定要使用 ILPIP，VM，並將 VM 加入新的服務:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## 如何擷取 VM 的 ILPIP 資訊
若要檢視使用上述指令碼建立之 VM 的 ILPIP 資訊，請執行下列 PowerShell 命令，並觀察值 *PublicIPAddress* 和 *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## 如何從 VM 移除 ILPIP
若要移除在上述指令碼中加入 VM 的 ILPI，請執行下列 PowerShell 命令：
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Remove-AzurePublicIP `
    | Update-AzureVM

## 如何將 ILPIP 加入現有的 VM
若要將 ILPIP 加入使用上述指令碼建立的 VM，請執行下列命令：

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Set-AzurePublicIP -PublicIPName ftpip2 `
    | Update-AzureVM

## 如何使用服務組態檔來將 ILPIP 關聯至 VM
您也可以使用服務組態檔 (CSCFG) 來將 ILPIP 關聯至 VM。 下列範例 xml 示範如何設定雲端服務，以使用保留的 IP，名為 *MyReservedIP* 做為角色執行個體的 ILPIP: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyReservedIP" domainNameLabel="MyReservedIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## 後續步驟

[保留的 IP](../virtual-networks-reserved-public-ip)

[保留的 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
