<properties
   pageTitle="使用 PowerShell 設定 ExpressRoute 線路的步驟 | Microsoft Azure"
   description="本文將逐步引導您建立和佈建 ExpressRoute 線路。 本文也會示範如何檢查狀態、更新或刪除和取消佈建線路。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/08/2015"
   ms.author="cherylmc"/>

# 使用 PowerShell 建立和修改 ExpressRoute 線路

> [AZURE.SELECTOR]
[PowerShell-傳統](expressroute-howto-circuit-classic.md)
[PowerShell-資源管理員](expressroute-howto-circuit-arm.md)

本文將逐步引導您使用 PowerShell Cmdlet 和傳統部署模型建立 ExpressRoute 線路。 下列步驟也會示範如何檢查狀態、更新或刪除和取消佈建 ExpressRoute 線路。 

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)] 


## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組。 您可以從的 PowerShell 區段下載最新的 PowerShell 模組 [Azure 下載頁面](http://azure.microsoft.com/downloads)。 按照指示 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 頁面，如需如何設定電腦以使用 Azure PowerShell 模組的逐步指引。 
- 請確定您已檢閱 [必要條件](expressroute-prerequisites.md) 頁面和 [工作流程](expressroute-workflows.md) 之前開始設定] 頁面上。

## 建立和佈建 ExpressRoute 線路

1. **匯入 ExpressRoute 的 PowerShell 模組。**

    您必須將 Azure 和 ExpressRoute 模組匯入至 PowerShell 工作階段，才能開始使用 ExpressRoute Cmdlet。 執行下列命令將 Azure 和 ExpressRoute 模組匯入至 PowerShell 工作階段。  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **取得支援的提供者、位置和頻寬清單。**

    建立 ExpressRoute 線路之前，您需要有連線提供者、支援的位置和頻寬選項等清單。 PowerShell cmdlet *Get AzureDedicatedCircuitServiceProvider* 傳回這項資訊，您將在稍後步驟中使用。

        PS C:\> Get-AzureDedicatedCircuitServiceProvider

        Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
        ----                 -------------------------      --------------------------                                                                                                                                                                                   
        Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
                             DC,Singapore                                                                                                                                                                                                                                
        AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
                             Valley,Dallas                                                                                                                                                                                                                               
        British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
                             DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
        Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
                             as,New York,Seattle,Silicon                                                                                                                                                                                                                 
                             Valley,Washington                                                                                                                                                                                                                           
                             DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
                             Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
                             Angeles,Melbourne                                                                                                                                                                                                                           
        IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
                             DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
        Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        – Cloud Connect                                                                                                                                                                                                                                                  
        Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
        Exchange                                                                                                                                                                                                                                                         
        Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
        IPVPN                                                                                                                                                                                                                                                            
        Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
                             Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
        PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        International                                                                                                                                                                                                                                                    
        Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
                             terdam,Chennai,Mumbai                                                                                                                                                                                                                       
        TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
                             Valley,Washington DC                                                                                                                                                                                                                        
        Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
        Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
        

3. **建立 ExpressRoute 線路。**

    以下示範如何透過矽谷的 Equinix 建立 200 Mbps ExpressRoute 線路。 如果您使用不同的提供者和不同的設定，請在提出要求時替換成該資訊。

    以下是新服務金鑰的要求範例：

        #Creating a new circuit
        $Bandwidth = 200
        $CircuitName = "MyTestCircuit"
        $ServiceProvider = "Equinix"
        $Location = "Silicon Valley"

        New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

    或者，如果您想要使用進階附加元件建立 ExpressRoute 線路，請使用下列範例。 請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md) premium 附加元件的詳細頁面。

        New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
    
    
    回應會包含服務金鑰。 您可以執行下列命令來取得所有參數的詳細描述：

        get-help new-azurededicatedcircuit -detailed 

4. **列出所有 ExpressRoute 線路。**

    您可以執行 *Get AzureDedicatedCircuit* 命令，以取得一份所有的 ExpressRoute 電路的您所建立。

        #Getting service key
        Get-AzureDedicatedCircuit

    回應如下列範例所示：

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : NotProvisioned
        Sku                              : Standard
        Status                           : Enabled

    您可以擷取這項資訊在任何時候使用 *Get AzureDedicatedCircuit* 指令程式。 執行呼叫時，若未指定任何參數，將會列出所有線路。 您的服務金鑰將列在 *ServiceKey* 欄位。

        PS C:\> Get-AzureDedicatedCircuit

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : NotProvisioned
        Sku                              : Standard
        Status                           : Enabled

    您可以執行下列命令來取得所有參數的詳細描述：

        get-help get-azurededicatedcircuit -detailed 

5. **將金鑰傳送給連線提供者來佈建。**

    當您建立新的 ExpressRoute 線路時，線路會是下列狀態：
    
        ServiceProviderProvisioningState : NotProvisioned
        
        Status                           : Enabled

     *ServiceProviderProvisioningState* 提供佈建服務提供者端和狀態的目前狀態的詳細資訊可在 Microsoft 端提供狀態。 ExpressRoute 線路必須處於下列狀態，才可供您使用。

        ServiceProviderProvisioningState : Provisioned
        
        Status                           : Enabled

    當連線提供者正在為您啟用線路時，線路會處於下列狀態。 

        ServiceProviderProvisioningState : Provisioned
        
        Status                           : Enabled



5. **定期檢查線路金鑰的情況和狀態。**

    這樣可讓您知道提供者何時已啟用您的線路。 設定循環之後 *ServiceProviderProvisioningState* 將會顯示為 *已佈建* 如下列範例所示。

        PS C:\> Get-AzureDedicatedCircuit

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

6. **建立路由設定。**
    
    請參閱 [ExpressRoute 電路的路由組態 (建立及修改電路對等互連)](expressroute-howto-routing-classic.md) 畫面來取得逐步指示。 

7. **將 VNet 連結到 ExpressRoute 線路。** 

    接下來，將 VNet 連結到 ExpressRoute 線路。 請參閱 [連結 ExpressRoute 電路的 Vnet](expressroute-howto-linkvnet-classic.md) 的逐步指示。 如果您需要建立 ExpressRoute 的虛擬網路，請參閱 [建立虛擬網路，expressroute](expressroute-howto-createvnet-classic.md) 如需相關指示。

##  取得 ExpressRoute 線路的狀態

您可以擷取這項資訊在任何時候使用 *Get-azurecircuit* 指令程式。 執行呼叫時，若未指定任何參數，將會列出所有線路。 

        PS C:\> Get-AzureDedicatedCircuit

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

        Bandwidth                        : 1000
        CircuitName                      : MyAsiaCircuit
        Location                         : Singapore
        ServiceKey                       : #################################
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

您可以將服務金鑰當作參數傳遞給呼叫，以取得特定 ExpressRoute 線路的詳細資訊。

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled


您可以執行下列命令來取得所有參數的詳細描述：

        get-help get-azurededicatedcircuit -detailed 

##  修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。 

您可以執行下列動作： 

- 在完全不停機的情況下，啟用或停用 ExpressRoute 線路的 ExpressRoute Premium 附加元件。
- 在完全不停機的情況下，增加 ExpressRoute 線路的頻寬。

請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md) 如需限制的詳細資訊。 

### 如何啟用 ExpressRoute Premium 附加元件

您可以使用下列 PowerShell Cmdlet，為現有的線路啟用 ExpressRoute Premium 附加元件：
    
        PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
        
        Bandwidth                        : 1000
        CircuitName                      : TestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Premium
        Status                           : Enabled

您的線路現在將啟用 ExpressRoute Premium 附加功能。 請注意，順利執行命令後，我們會開始向您收取進階附加元件功能的費用。

### 如何停用 ExpressRoute Premium 附加元件

您可以使用下列 PowerShell Cmdlet，為現有的線路停用 ExpressRoute Premium 附加元件：
    
        PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
        
        Bandwidth                        : 1000
        CircuitName                      : TestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

您的線路現在已經停用進階附加元件。 

>[AZURE.IMPORTANT] 如果您使用的資源大於什麼標準的循環允許這項作業可能會失敗。
>
>- 從高階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
- 您必須取消連結其他地理政治區域中的所有虛擬網路。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
- 就私用對等而言，路由表必須是少於 4000 個路由。 如果路由表大小超過 4000 個路由，BGP 工作階段將會中斷，而且在通告的首碼數目降到 4000 以下之前不會重新啟用。


### 如何更新 ExpressRoute 線路頻寬

檢查 [ExpressRoute 常見問題集](expressroute-faqs.md) 頁面，以您的提供者支援的頻寬選項。 您可以挑選任何比現有電路規模還大的大小。 一旦決定需要的大小後，您可以使用下列命令來調整線路大小。

        PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
        
        Bandwidth                        : 1000
        CircuitName                      : TestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

我們會在 Microsoft 端調整電路的大小。 您必須連絡連線提供者，將他們那邊的設定更新為符合這項變更。 請注意，從這個階段起，我們將開始計算更新頻寬選項的費用。

>[AZURE.IMPORTANT] 您不能減少不中斷地 ExpressRoute 電路的頻寬。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。

##  刪除和佈建 ExpressRoute 線路

您可以執行下列命令來刪除 ExpressRoute 線路：

        PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"    

請注意，您必須取消連結 ExpressRoute 的所有虛擬網路，此作業才會成功。 如果此作業失敗，請檢查您是否有任何虛擬網路連結至線路。

如果已啟用 ExpressRoute 電路服務提供者佈建狀態，狀態會移至 *停用* 從啟用狀態。 您必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

如果服務提供者已取消佈建電路 (服務提供者佈建狀態設定為 *未佈建*) 執行上述指令程式之前，我們將會取消佈建電路，並停止計費。 

## 後續步驟

- [設定路由](expressroute-howto-routing-classic.md)


