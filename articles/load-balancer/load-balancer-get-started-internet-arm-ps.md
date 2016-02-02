<properties 
   pageTitle="使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma" />


# 開始使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [了解如何建立網際網路面向的負載平衡器使用傳統部署模型](load-balancer-get-started-internet-classic-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

下列步驟將說明如何搭配 PowerShell 使用 Azure 資源管理員，建立網際網路面向的負載平衡器。 使用 Azure 資源管理員時，會個別設定建立網際網路面向的負載平衡器所需的項目，然後放置在一起，以建立資源。

其中將說明必須完成才能建立負載平衡器的個別工作順序，並詳細說明若要達到此目標，需要做些什麼。

## 若要建立網際網路面向的負載平衡器，需要哪些項目？

您需要建立和設定下列物件以部署負載平衡器：

- 前端 IP 組態 - 包含傳入網路流量的公用 IP 位址。

- 後端位址集區 - 包含虛擬機器的網路介面 (NIC)，可從負載平衡器接收網路流量。

- 負載平衡規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中的連接埠的規則。

- 輸入 NAT 規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。

- 探查 - 包含用來檢查後端位址集區中虛擬機器執行個體可用性的健全狀態探查。

您可以取得詳細資訊有關負載平衡器元件與 Azure 資源管理員在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)。


## 設定 PowerShell 以使用資源管理員

請確定您的 PowerShell 的 Azure 模組具有最新的實際執行版本，以及正確設定 PowerShell 以存取您的 Azure 訂用帳戶。

### 步驟 1

        PS C:\> Login-AzureRmAccount

### 步驟 2

檢查帳戶的訂用帳戶

        PS C:\> get-AzureRmSubscription 

您將使用您的認證提示進行驗證。<BR>

### 步驟 3

選擇您要使用的 Azure 訂閱。 <BR>


        PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## 建立前端 IP 集區的虛擬網路和公用 IP 位址

### 步驟 1

建立子網路和虛擬網路。

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 步驟 2

建立名為 *PublicIP* 的公用 IP 位址 (PIP)，讓具有 DNS 名稱 *loadbalancernrp.westus.cloudapp.azure.com* 的前端 IP 集區使用。 下列命令會使用靜態配置類型。

    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] 負載平衡器將使用公用 IP 的網域標籤做為其 FQDN 的前置詞。 這樣會變更傳統部署模型，該部署使用雲端服務作為負載平衡器 FQDN。 
>在此範例中，FQDN 是 *loadbalancernrp.westus.cloudapp.azure.com*。

## 建立前端 IP 集區與後端位址集區

### 步驟 1

建立名為 *LB-Frontend* 的前端 IP 集區，它使用 *PublicIp* PIP。

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 步驟 2

建立名為 *LB-backend* 的後端位址集區。

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## 建立 LB 規則、NAT 規則、探查及負載平衡器

上述範例會建立下列項目：

- NAT 規則，將連接埠 3441 上的所有傳入流量轉譯至連接埠 3389
- NAT 規則，將連接埠 3442 上的所有傳入流量轉譯至連接埠 3389。
- 負載平衡器規則，將連接埠 80 上的所有傳入流量，負載平衡至後端集區中位址的連接埠 80。
- 探查規則，將在名為 *HealthProbe.aspx* 的頁面上檢查健全狀態。
- 負載平衡器，使用上述所有物件。



### 步驟 1

建立 NAT 規則。

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389
    
    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 步驟 2

建立負載平衡器規則。

    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 步驟 3

建立健全狀況探查。

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 步驟 4

使用上方建立的物件建立負載平衡器。

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## 建立 NIC

您需要建立 NIC (或修改現有的) 並將它們關聯至 NAT 規則、負載平衡器規則和探查。

### 步驟 1

取得需要在其中建立 NIC 的 VNet 和子網路。

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 步驟 2

建立名為 *lb-nic1-be* 的 NIC，並將它與第一個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 步驟 3

建立名為 *lb-nic2-be* 的 NIC，並將它與第二個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。

    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 步驟 4

檢查 NIC。


    PS C:\> $backendnic1

預期的輸出：

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False

### 步驟 5

使用 `新增 AzureRmVMNetworkInterface` 指令程式可將 Nic 指派給不同的 Vm。

您可以尋找指引如何建立虛擬機器，並指派中的 NIC [建立和預先設定 Windows 虛擬機器利用資源管理員和 Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), ，使用此範例中的選項 5。

## 更新現有負載平衡器

### 步驟 1

使用上述範例中的負載平衡器，透過 Get-AzureLoadBalancer 將負載平衡器物件指派給變數 $slb

    $slb=get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 步驟 2

在下列範例中，您會使用前端的連接埠 81 和後端集區的連接埠 8181，將新的輸入 NAT 規則加入現有的負載平衡器

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp

### 步驟 3

使用 Set-AzureLoadBalancer 儲存新組態

    $slb | Set-AzureRmLoadBalancer

## 移除負載平衡器

使用命令移除 AzureLoadBalancer 刪除名為 「 NRP-LB 」 資源群組中名為"NRP-RG"先前建立的負載平衡器

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 您可以使用選擇性參數 -Force 以避免出現刪除提示。

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)




