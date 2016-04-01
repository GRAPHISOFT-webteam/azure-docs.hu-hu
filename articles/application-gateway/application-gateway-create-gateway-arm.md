<properties 
   pageTitle="使用 Azure 資源管理員建立、啟動或刪除應用程式閘道 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# 使用 Azure 資源管理員建立、啟動或刪除應用程式閘道

應用程式閘道是第 7 層負載平衡器。 它提供在不同伺服器之間進行容錯移轉、效能路由傳送 HTTP 要求，而不論它們是在雲端或內部部署中。 應用程式閘道具有下列應用程式傳遞功能：HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、SSL 卸載。 


> [AZURE.SELECTOR]
- [Azure 傳統 PowerShell 步驟](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager 範本 ](application-gateway-create-gateway-arm-template.md)


<BR>


本文將逐步引導您完成建立和設定、啟動及刪除應用程式閘道的步驟。


>[AZURE.IMPORTANT] 您使用的 Azure 資源之前，務必了解 Azure 目前有兩種部署模型 ︰ 資源管理員，以及傳統。 請確定您了解 [部署模型和工具](azure-classic-rm.md) 之前使用的任何 Azure 資源。 您可以按一下本文頂端的索引標籤，檢視不同工具的文件。本文件將說明使用 Azure 資源管理員建立應用程式閘道的方式。 若要使用傳統的版本，請移至 [建立應用程式閘道傳統部署使用 PowerShell](application-gateway-create-gateway.md)。



## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以下載並安裝最新版的 **Windows PowerShell** 區段 [下載頁面](http://azure.microsoft.com/downloads/)。
2. 您將建立應用程式閘道的虛擬網路和子網路。 請確定沒有虛擬機器或是雲端部署正在使用子網路。 應用程式閘道必須單獨在虛擬網路子網路中。
3. 您將要設定為使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是指派公用 IP/VIP。

## 建立應用程式閘道需要什麼？
 

- **後端伺服器集區 ︰** 的後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。 
- **後端伺服器集區設定 ︰** 每個集區有設定，例如連接埠、 通訊協定和 cookie 為基礎的親和性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠 ︰** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式 ︰** 接聽程式具有前端連接埠、 通訊協定 （Http 或 Https，這些都區分大小寫） 和 SSL 憑證名稱 （如果已設定 SSL 卸載）。 
- **規則 ︰** 規則繫結接聽程式和後端伺服器集區，並定義哪個後端伺服器集區達到特定接聽程式時，應該要導向流量。 目前，只有 *基本* 規則受支援。  *基本* 規則是循環配置資源負載散發。


 
## 建立新的應用程式閘道

使用「Azure 傳統」和「Azure 資源管理員」的差別，在於您建立應用程式閘道和需設定項目的順序。

使用資源管理員，組成應用程式閘道的所有項目都將個別設定，然後放在一起建立應用程式閘道資源。


以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP
3. 建立應用程式閘道組態物件
4. 建立應用程式閘道資源


## 建立資源管理員的資源群組

請確定您切換 PowerShell 模式來使用 ARM Cmdlet。 詳細資訊，可在使用 [Windows Powershell 與資源管理員](powershell-azure-resource-manager.md)。

### 步驟 1

        PS C:\> Login-AzureRmAccount



### 步驟 2

檢查帳戶的訂用帳戶 

        PS C:\> get-AzureRmSubscription 

系統會提示使用您的認證進行驗證。<BR>

### 步驟 3 

選擇要使用哪一個 Azure 訂用帳戶。 <BR>


        PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。 

## 建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路： 

### 步驟 1  
    
    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

指派用於建立虛擬網路的位址範圍 10.0.0.0/24 給子網路變數 subnet

### 步驟 2  
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 ("West US") 區域的 "appw-rg" 資源群組中建立名為 "appgwvnet" 的虛擬網路 

### 步驟 3
    
    $subnet=$vnet.Subnets[0]

## 建立前端組態的公用 IP 位址

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

在美國西部區域的 "appw-rg" 資源群組中建立公用 IP 資源 "publicIP01"。 


## 建立應用程式閘道組態物件

### 步驟 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。 當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。
 
### 步驟 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

這個步驟會設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "134.170.185.46, 134.170.188.221,134.170.185.50"。 這些 IP 位址將接收來自前端 IP 端點的網路流量。 您要取代上述 IP 位址，加入您自己的應用程式 IP 位址端點。

### 步驟 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

設定後端集區中負載平衡網路流量的應用程式閘道設定 "poolsetting01"。

### 步驟 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

在此案例中是設定公用 IP 端點的前端 IP 連接埠 "frontendport01"。

### 步驟 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

建立名為 "fipconfig01" 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。

### 步驟 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

### 步驟 7 

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

### 步驟 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

設定應用程式閘道的執行個體大小

>[AZURE.NOTE]  預設值為 *InstanceCount* 是 2，最大值為 10。 預設值為 *GatewaySize* 是 Medium。 您可以在 Standard_Small、Standard_Medium 和 Standard_Large 之間選擇。

## 使用 New-AzureApplicationGateway 建立應用程式閘道

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

從上述步驟的所有組態項目建立應用程式閘道。 範例中的應用程式閘道名為 "appgwtest"。 


## 啟動應用程式閘道

設定閘道之後，請使用 `Start-AzureRmApplicationGateway` Cmdlet 來啟動閘道。 成功啟動閘道之後，會開始應用程式閘道計費。 


**注意 ︰**  `Start-AzureRmApplicationGateway` cmdlet 可能需要 15-20 分鐘才能完成。 

在以下範例中，應用程式閘道名為 "appgwtest"，資源群組為 "app-rg"：


### 步驟 1

取得應用程式閘道物件，並關聯至變數 "$getgw"：
 
    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### 步驟 2
     
使用 `Start-AzureRmApplicationGateway` 啟動應用程式閘道：

     Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

    

## 確認應用程式閘道狀態

使用 `Get-AzureRmApplicationGateway` Cmdlet 來檢查閘道狀態。 如果 *Start-azureapplicationgateway* 在上一個步驟成功，則狀態應該是 *執行*, ，且 Vip 和 DnsName 應該具備有效的項目。 

這個範例示範已啟動、正在執行且準備好將流量傳送到 `http://<generated-dns-name>.cloudapp.net` 的應用程式閘道。 

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
    GatewayIPConfigurations           : {gatewayip01}
    SslCertificates                   : {}
    FrontendIPConfigurations          : {frontendip01}
    FrontendPorts                     : {frontendport01}
    BackendAddressPools               : {pool01}
    BackendHttpSettingsCollection     : {setting01}
    HttpListeners                     : {listener01}
    RequestRoutingRules               : {rule01}
    OperationalState                  : 
    ProvisioningState                 : Succeeded
    GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
    SslCertificatesText               : []
    FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
    FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
    BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
    BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
    HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
    RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/\"ddb0408e-a54c-4501-a7f8-8487c3530bd7\"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
    ResourceGroupName                 : appgw-rg
    Location                          : westus
        Tag                               : {}
    TagsTable                         : 
    Name                              : appgwtest
    Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
    Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## 刪除應用程式閘道

若要刪除應用程式閘道，您需要依序執行下列動作：

1. 使用 `Stop-AzureRmApplicationGateway` Cmdlet 停止閘道。 
2. 使用 `Remove-AzureRmApplicationGateway` Cmdlet 移除閘道。
3. 使用 `Get-AzureRmApplicationGateway` Cmdlet 確認已移除閘道。


### 步驟 1

取得應用程式閘道物件，並關聯至變數 "$getgw"：
 
    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 步驟 2
     
使用 `Stop-AzureRmApplicationGateway` 停止應用程式閘道：

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


應用程式閘道處於「已停止」狀態之後，請使用 `Remove-AzureRmApplicationGateway` Cmdlet 移除服務。


    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

    

>[AZURE.NOTE] "-強制 「 參數可用來隱藏移除確認訊息
>

若要確認已移除服務，您可以使用 `Get-AzureRmApplicationGateway` Cmdlet。 這不是必要步驟。


    Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

    


## 後續步驟

如果您想要設定 SSL 卸載，請參閱 [設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與 ILB 搭配使用，請參閱 [具有內部負載平衡器 (ILB) 建立應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)



