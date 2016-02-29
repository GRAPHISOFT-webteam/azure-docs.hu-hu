<properties 
   pageTitle="將虛擬網路連結到 ExpressRoute 循環 |Microsoft Azure"
   description="本文件提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 循環的概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/09/2015"
   ms.author="ganesr" />

# 將虛擬網路連結到 ExpressRoute 電路

> [AZURE.SELECTOR]
- [PowerShell-傳統](expressroute-howto-linkvnet-classic.md)
- [PowerShell 的資源管理員](expressroute-作法-linkvnet-arm.md)
- [資源管理員範本](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

本文提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 線路的概觀。 虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。 本文適用於使用傳統部署模型部署的 VNet。 如果您想要連結已使用 Azure 資源管理員部署方法進行部署的虛擬網路，您可以使用範本來執行。 請參閱上述索引標籤以瀏覽至範本。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)] 

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (版本 1.0 或更新版本)。 
- 請確定您已檢閱 [必要條件](expressroute-prerequisites.md) ] 頁面上， [路由需求](expressroute-routing.md) 頁面和 [工作流程](expressroute-workflows.md) 之前開始設定] 頁面上。
- 您必須擁有作用中的 ExpressRoute 循環。 請依照下列指示 [建立的 ExpressRoute 電路](expressroute-howto-circuit-classic.md) 和有電路啟用您的連線提供者，才能繼續。 ExpressRoute 線路必須處於已佈建和已啟用狀態，您才能執行如下所述的 Cmdlet。
- 您必須擁有作用中的 ExpressRoute 循環。 
    - 請依照下列指示 [建立的 ExpressRoute 電路](expressroute-howto-circuit-arm.md) 和有連線提供者所啟用的電路。 
    - 確定您已針對循環設定了 Azure 私用對等。 請參閱 [設定路由](expressroute-howto-routing-arm.md) 路由指示的文章。 
    - Azure 私用對等必須設定，且在您的網路與 Microsoft 之間的 BGP 對等必須為您啟用端對端連線。
    - 您必須有已建立且完整佈建的虛擬網路和虛擬網路閘道。 依照指示建立 [vpn 閘道](../articles/vpn-gateway-create-site-to-site-rm-powershell.md)

您最多可以將 10 個虛擬網路連結至 ExpressRoute 電路。 所有的 ExpressRoute 循環都必須位於同一個地理區域。 如果您已啟用 ExpressRoute 高階附加元件，則可將更大量的虛擬網路連結到您的 ExpressRoute 循環。 簽出 [常見問題集](expressroute-faqs.md) 如需有關 premium 附加元件。 

## 將同一個 Azure 訂用帳戶中的 VNet 連結到 ExpressRoute 電路

您可以使用下列 Cmdlet，將虛擬網路閘道連結到 ExpressRoute 電路。 執行 Cmdlet 之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## 將不同 Azure 訂用帳戶中的虛擬網路連結到 ExpressRoute 電路

多個訂用帳戶間可共用一個 ExpressRoute 循環。 此圖顯示簡單的圖解，示範如何在多個訂用帳戶間共用 ExpressRoute 循環的方式。 大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。 組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 循環，以連接回內部部署網路。 單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。 組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

>[AZURE.NOTE] 專用電路的連線和頻寬費用將會套用至 ExpressRoute 電路擁有者。 所有虛擬網路都會共用相同的頻寬。

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 系統管理

 *電路擁有者* 是 ExpressRoute 電路資源的授權的進階使用者。 電路擁有者可以建立可由折的授權 *電路使用者*。 *電路使用者* 擁有者的虛擬網路閘道 (也就不在相同 ExpressRoute 電路的訂閱)。 *電路使用者* 可以兌換授權 (每個虛擬網路的一個授權。

 *電路擁有者* 有修改，並隨時撤銷授權。 如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結連線均會被刪除。

### 循環擁有者作業 

#### 建立授權
    
電路擁有者會建立授權。 這樣即會建立授權金鑰，讓電路使用者可用來將其虛擬網路閘道連接到 ExpressRoute 電路。 一個授權僅適用於一個連線。

下列 Cmdlet 程式碼片段示範如何建立授權。 

        Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

        $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
        

對此動作的回應將包含授權金鑰和狀態

        Name                   : MyAuthorization1
        Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
        Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
        AuthorizationKey       : ####################################
        AuthorizationUseStatus : Available
        ProvisioningState      : Succeeded

        

#### 檢閱授權

循環擁有者可以藉由執行下列 Cmdlet，來檢閱特定循環上發出的所有授權。

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
    

#### 新增授權

電路擁有者可以使用下列 Cmdlet 來新增授權。

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

    
#### 刪除授權

循環擁有者可以使用下列 Cmdlet 來撤銷/刪除使用者的授權。

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### 循環使用者作業

電路使用者需要具備對等識別碼以及電路擁有者所提供的授權金鑰。 電路金鑰看起來與下列內容類似：


授權金鑰是 GUID。

#### 兌換連線授權

循環使用者可以執行下列 Cmdlet 來兌換連結授權。

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### 釋出連線授權

您可以藉由刪除將 ExpressRoute 電路連結到虛擬網路的連線來釋出授權。

## 後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

