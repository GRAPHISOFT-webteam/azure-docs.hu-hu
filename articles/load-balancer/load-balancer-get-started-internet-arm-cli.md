<properties 
   pageTitle="使用 Azure CLI 在資源管理員中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 Azure CLI 在資源管理員中建立網際網路面向的負載平衡器"
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
   ms.date="11/16/2015"
   ms.author="joaoma" />


# 開始使用 Azure CLI 建立網際網路面向的負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [了解如何建立網際網路面向的負載平衡器使用傳統部署](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

其中將說明必須完成才能建立負載平衡器的個別工作順序，並詳細說明若要達到此目標，需要做些什麼。


## 若要建立網際網路面向的負載平衡器，需要哪些項目？

您需要建立和設定下列物件以部署負載平衡器。

- 前端 IP 組態 - 包含傳入網路流量的公用 IP 位址。

- 後端位址集區 - 包含虛擬機器的網路介面 (NIC)，可從負載平衡器接收網路流量。

- 負載平衡規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中的連接埠的規則。

- 輸入 NAT 規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。

- 探查 - 包含用來檢查後端位址集區中虛擬機器執行個體可用性的健全狀態探查。

您可以取得詳細資訊有關負載平衡器元件與 Azure 資源管理員在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)。

## 設定 CLI 以使用資源管理員

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。

2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

        azure config mode arm

    預期的輸出：

        info:    New mode is arm


## 建立前端 IP 集區的虛擬網路和公用 IP 位址

### 步驟 1

使用名為 *NRPRG* 的資源群組，在美國東部位置建立名為 *NRPVnet* 的虛擬網路 (VNet)。

    azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

建立名為 *NRPVnetSubnet* 的子網路，其中包含 *NRPVnet* 中 10.0.0.0/24 的 CIDR 區塊。

    azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### 步驟 2

建立名為 *NRPPublicIP* 的公用 IP 位址，讓具有 DNS 名稱 *loadbalancernrp.eastus.cloudapp.azure.com* 的前端 IP 集區使用。 下列命令使用靜態配置類型和 4 分鐘的閒置逾時。

    azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

>[AZURE.IMPORTANT] 負載平衡器將使用公用 IP 的網域標籤做為其 FQDN。 這樣會變更傳統部署，該部署使用雲端服務做為負載平衡器 FQDN。 
>在此範例中，FQDN 是 *loadbalancernrp.eastus.cloudapp.azure.com*。

## 建立負載平衡器

在下列範例中，下列命令會在「美國東部」**Azure 位置中的 *NRPRG* 資源群組內，建立名為 *NRPlb* 的負載平衡器。

    azure network lb create NRPRG NRPlb eastus

## 建立前端 IP 集區與後端位址集區

下列範例會建立前端 IP 集區，此集區將接收傳入到負載平衡器和後端 IP 集區的網路流量，其中的前端集區會傳送經負載平衡後的網路流量。

### 步驟 1

建立與在上個步驟中建立的公用 IP 相關聯的前端 IP 集區與負載平衡器。

    azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### 步驟 2

設定後端位址集區以用於接收來自前端 IP 集區的傳入流量。

    azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## 建立 LB 規則、NAT 規則及探查

以下範例會建立下列項目。

- 轉譯至連接埠 3389 的連接埠 3441 的所有連入流量的 NAT 規則<sup>1</sup>
- NAT 規則，將連接埠 3442 上的所有傳入流量轉譯至連接埠 3389
- 負載平衡器規則，將連接埠 80 上的所有傳入流量，負載平衡至後端集區中位址的連接埠 80。
- 探查規則，將在名為 *HealthProbe.aspx* 的頁面上檢查健全狀態。

<sup>1</sup> NAT 規則相關聯的負載平衡器後方的特定虛擬機器執行個體。 傳入到連接埠 3341 的網路流量，將會使用與下方範例中 NAT 規則關聯的連接埠 3389，傳送至特定的虛擬機器。 您必須針對 NAT 規則選擇 UDP 或 TCP 通訊協定。 無法將兩種通訊協定指派到相同的連接埠。

### 步驟 1

建立 NAT 規則。

    azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp1 -p tcp -f 3441 -b 3389
    azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp2 -p tcp -f 3442 -b 3389

參數：

- **-g** - 資源群組名稱
- **-l** - 負載平衡器名稱
- **-n** -資源名稱是否為 nat 規則、探查或 lb 規則。
- **-p** - 通訊協定 (可以是 TCP 或 UDP)
- **-f** - 要使用的前端連接埠 (探查命令會使用 -f 來定義探查路徑)
- **-b** - 要使用的後端連接埠

### 步驟 2

建立負載平衡器規則。

    azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

### 步驟 3

建立健全狀況探查。

    azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

**-g** -資源群組 
**-l** -負載平衡器集的名稱
**-n** -健全狀況探查的名稱
**-p** -健全狀況探查所使用的通訊協定
**-i** -探查間隔 (秒)
**-c** 檢查項目數目

### 步驟 4

檢查您的設定。

    azure network lb show nrprg nrplb

預期的輸出：

    info:    Executing command network lb show
    + Looking up the load balancer "nrplb"
    + Looking up the public ip "NRPPublicIP"    
    data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
    data:    Name                            : nrplb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : eastus
    data:    Provisioning State              : Succeeded
    data:    Frontend IP configurations:
    data:      Name                          : NRPfrontendpool
    data:      Provisioning state            : Succeeded
    data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
    data:      Public IP allocation method   : Static
    data:      Public IP address             : 40.114.13.145
    data:
    data:    Backend address pools:
    data:      Name                          : NRPbackendpool
    data:      Provisioning state            : Succeeded
    data:
    data:    Load balancing rules:
    data:      Name                          : HTTP
    data:      Provisioning state            : Succeeded
    data:      Protocol                      : Tcp
    data:      Frontend port                 : 80
    data:      Backend port                  : 80
    data:      Enable floating IP            : false
    data:      Idle timeout in minutes       : 4
    data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
    data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
    data:
    data:    Inbound NAT rules:
    data:      Name                          : rdp1
    data:      Provisioning state            : Succeeded
    data:      Protocol                      : Tcp
    data:      Frontend port                 : 3441
    data:      Backend port                  : 3389
    data:      Enable floating IP            : false
    data:      Idle timeout in minutes       : 4
    data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
    data:
    data:      Name                          : rdp2
    data:      Provisioning state            : Succeeded
    data:      Protocol                      : Tcp
    data:      Frontend port                 : 3442
    data:      Backend port                  : 3389
    data:      Enable floating IP            : false
    data:      Idle timeout in minutes       : 4
    data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
    data:
    data:    Probes:
    data:      Name                          : healthprobe
    data:      Provisioning state            : Succeeded
    data:      Protocol                      : Http
    data:      Port                          : 80
    data:      Interval in seconds           : 15
    data:      Number of probes              : 4
    data:
    info:    network lb show command OK

## 建立 NIC

您需要建立 NIC (或修改現有的) 並將它們關聯至 NAT 規則、負載平衡器規則和探查。

### 步驟 1

建立名為 *lb-nic1-be* 的 NIC，並將它與 *rdp1* NAT 規則及 *NRPbackendpool* 後端位址集區產生關聯。

    azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

參數：

- **-g** - 資源群組名稱
- **-n** - NIC 資源的名稱
- **--subnet-name** - 子網路的名稱
- **--subnet-vnet-name** - 虛擬網路的名稱
- **-d** -後端集區資源的識別碼-開頭 /subscription/ {subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>
- **-e** -NIC 資源相關聯的 NAT 規則的識別碼-開頭 /subscriptions/ # # # /resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>


預期的輸出：

    info:    Executing command network nic create
    + Looking up the network interface "lb-nic1-be"
    + Looking up the subnet "nrpvnetsubnet"
    + Creating network interface "lb-nic1-be"
    + Looking up the network interface "lb-nic1-be"
    data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    data:    Name                            : lb-nic1-be
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Enable IP forwarding            : false
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 10.0.0.4
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
    data:      Load balancer backend address pools
    data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
    data:      Load balancer inbound NAT rules:
    data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
    data:
    info:    network nic create command OK

### 步驟 2

建立名為 *lb-nic2-be* 的 NIC，並將它與 *rdp2* NAT 規則及 *NRPbackendpool* 後端位址集區產生關聯。

    azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### 步驟 3

建立名為 *web1* 的虛擬機器 (VM)，並將它與名為 *lb-nic1-be* 的 NIC 產生關聯。 系統先建立了名為 *web1nrp* 的儲存體帳戶，再執行下方命令。

    azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] 負載平衡器中的 VM 必須在相同的可用性設定組中。 使用 `azure availset 建立` 建立可用性設定組。 

輸出將如下所示：

    info:    Executing command vm create
    + Looking up the VM "web1"
    Enter username: azureuser
    Enter password for azureuser: *********
    Confirm password: *********
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Looking up the storage account web1nrp
    + Looking up the availability set "nrp-avset"
    info:    Found an Availability set "nrp-avset"
    + Looking up the NIC "lb-nic1-be"
    info:    Found an existing NIC "lb-nic1-be"
    info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
    info:    This is a NIC without publicIP configured
    + Creating VM "web1"
    info:    vm create command OK

>[AZURE.NOTE] 預期會顯示「此 NIC 未設有 publicIP」****訊息，因為針對負載平衡器建立的 NIC 會使用負載平衡器公用 IP 位址連線到網際網路。 

由於 *lb-nic1-be* NIC 會與 *rdp1* NAT 規則相關聯，因此您可以使用 RDP 透過負載平衡器上的連接埠 3441 連線至 *web1*。

### 步驟 4

建立名為 *web2* 的虛擬機器 (VM)，並將它與名為 *lb-nic2-be* 的 NIC 產生關聯。 系統先建立了名為 *web1nrp* 的儲存體帳戶，再執行下方命令。

    azure vm create --resource-group nrprg --name web2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## 更新現有負載平衡器

您可以新增參考現有負載平衡器的規則。 在下列範例中，新的負載平衡器規則會新增至現有的負載平衡器 **NRPlb**

    azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

參數：

**-g** -資源群組名稱<br>
**-l** -負載平衡器名稱<BR> 
**-n** -負載平衡器規則名稱<BR>
**-p** -通訊協定<BR>
**-f** -front 結束連接埠<BR>
**-b** -回結束連接埠<BR>
**-t** -前端集區名稱<BR>
**-b** -回端集區名稱<BR>

## 刪除負載平衡器

若要移除負載平衡器，請使用下列命令

    azure network lb delete -g nrprg -n nrplb 

其中 **nrprg** 是資源群組，而 **nrplb** 是負載平衡器名稱。

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)





