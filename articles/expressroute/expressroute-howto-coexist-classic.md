<properties
   pageTitle="設定可以並存的 ExpressRoute 和站對站 VPN 連線 | Microsoft Azure"
   description="本文會引導您設定可以並存的 ExpressRoute 和站對站 VPN 連線。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="cherylmc"/>

# 設定 ExpressRoute 和站對站 VPN 連線並存於 VNet

能夠設定站對站 VPN 和 ExpressRoute 有諸多好處。 您可以將站對站 VPN 設定為 ExressRoute 的安全容錯移轉路徑，或使用站對站 VPN 來連線至不在您網路中但透過 ExpressRoute 連接的網站。 本文中將說明設定這兩個案例的步驟。 本文適用於使用傳統部署模型所建立的連線。 

>[AZURE.IMPORTANT] 請務必了解 Azure 目前使用兩種部署模型 ︰ 資源管理員，以及傳統。 開始您的組態之前，請確定您瞭解部署模型和工具。 部署模型的相關資訊，請參閱 [Azure 部署模型](../azure-classic-rm.md)


在執行下列指示之前，必須事先設定 ExpressRoute 線路。 請確定您已遵循指南 [建立的 ExpressRoute 電路](expressroute-howto-circuit-classic.md) 和 [設定路由](expressroute-howto-routing-classic.md) 遵循下列步驟之前。

## 限制

- **不支援傳輸路由 ︰** 您無法透過 ExpressRoute 連線區域網路，以及您透過站台對站 VPN 連線的區域網路之間路由傳送 （透過 Azure)。
- **點對站台不支援 ︰** 您無法啟用點對站台連結到 ExpressRoute 之相同 vnet 的 VPN 連線。 點對站 VPN 和 ExpressRoute 不能並存在相同的 VNet。
- **無法啟用強制通道上的站台對站 VPN 閘道 ︰** 您可以只 「 強制 」 所有網際網路繫結流量回到您的內部部署網路透過 ExpressRoute。 
- **只有 standard 或更高的效能閘道 ︰** 您必須使用標準或高的效能閘道 ExpressRoute 閘道和站台對站 VPN 閘道。 請參閱 [閘道 Sku](../vpn-gateway/vpn-gateway-about-vpngateways.md) 的閘道器 Sku 的相關資訊。
- **靜態路由需求 ︰** 如果您的區域網路連線到 ExpressRoute 和站台對站台 VPN，您必須將站台對站台 VPN 連接路由傳送至公用網際網路區域網路設定靜態路由。
- **ExpressRoute 閘道器必須先設定 ︰** 之前必須先建立 ExpressRoute 閘道器先新增站台對站 VPN 閘道。

## 設定站對站 VPN 作為 ExpressRoute 的容錯移轉路徑

您可以設定站對站 VPN 連線作為 ExpressRoute 的備用連線。 這僅適用於連結至 Azure 私用對等路徑的虛擬網路。 對於可透過 Azure 公用和 Microsoft 對等存取的服務，沒有 VPN 架構的容錯移轉解決方案。 ExpressRoute 線路一律為主要連結。 只在當 ExpressRoute 線路故障時，資料才能流經站對站 VPN 路徑。 

![並存](media/expressroute-howto-coexist-classic/scenario1.jpg)

## 設定站對站 VPN 來連線到未透過 ExpressRoute 連接的網站

您可以將網路設定成有些網站透過站對站 VPN 直接連接到 Azure，而有些網站透過 ExpressRoute 來連線。 

![並存](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] 您無法設定虛擬網路為轉送路由器。

## 建立和設定

如果要設定可並存的連線，有兩組不同的程序可供選擇。 您所選取的設定程序取決於您是已經有想要連線的現有虛擬網路，還是想要建立新的虛擬網路。

- **建立新的虛擬網路以及並存的連線：**
    
    如果您還沒有虛擬網路，這個程序將會引導您建立新的虛擬網路，並建立新的 ExpressRoute 和站對站 VPN 連線。 若要設定，請依照下列文章一節的步驟 **ExpressRoute 和站台對站連線，以建立新的虛擬網路**。

- **設定現有的虛擬網路讓連線並存：**

    您可能已經有虛擬網路，而且使用現有的站對站 VPN 連線或 ExpressRoute 連線。  **設定並存的現有虛擬網路的連線** 一節將會引導您刪除閘道，並建立新的 ExpressRoute 和站台對站 VPN 連線。 請注意，建立新的連線時，您必須以非常特定的順序來完成這些步驟。 請勿使用其他文章中的指示建立閘道器和連線。

    在此程序中，建立可以並存的連線將會要求您刪除閘道器，然後設定新的閘道器。 這表示當您刪除和重新建立閘道器與連線時，將會有跨設備連線的停機時間，但您不需要將任何 VM 或服務移轉至新的虛擬網路。 您的 VM 和服務仍能透過負載平衡器對外通訊，而您能夠在這兩者設定為這樣做時進行閘道器設定。


## 建立具有 ExpressRoute 和站對站 VPN 連線能力的新虛擬網路

此程序會引導您建立 VNet，並建立將並存的站對站和 ExpressRoute 連線。

1. 請確認您擁有最新版本的 PowerShell Cmdlet。 您可以從下載並安裝最新的 PowerShell cmdlet 的 PowerShell 區段 [下載頁面](http://azure.microsoft.com/downloads/)。

2. 建立虛擬網路的結構描述。 如需使用網路組態檔的詳細資訊，請參閱 [設定虛擬網路使用網路組態檔](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。 如需組態結構描述的詳細資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

    當您建立結構描述時，請務必使用下列值：

    - 虛擬網路的閘道器子網路必須是 /27 或更短的首碼 (例如 /26 或 /25)。
    - 閘道器連線類型為「專用」。

              <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
                <AddressSpace>
                  <AddressPrefix>10.17.159.192/26</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Subnet-1">
                    <AddressPrefix>10.17.159.192/27</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.17.159.224/27</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>

3. 建立和設定 XML 結構描述檔案後，請上傳該檔案。 這樣會建立您的虛擬網路。

    使用下列 Cmdlet 來上傳檔案，將該值替換為您自己的值。

        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. 建立 ExpressRoute 閘道器。 請務必將 gatewaysku 指定為 *標準* 或 *HighPerformance* 並將 gatewaytype 指定為 *DynamicRouting*。

    使用以下範例，將該值替換為您自己的值。

        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. 將 ExpressRoute 閘道器連結到 ExpressRoute 電路。 完成這個步驟之後，內部部署網路和 Azure 之間的連線 (透過 ExpressRoute ) 便會建立。

        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. 接下來，建立站對站 VPN 閘道。 GatewaySKU 必須是 *標準* 或 *HighPerformance* 且 GatewayType 必須是 *DynamicRouting*。

        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

    若要擷取虛擬網路閘道器的設定 (包括閘道器識別碼和公用 IP)，請使用 `Get-AzureVirtualNetworkGateway` Cmdlet。

        Get-AzureVirtualNetworkGateway

        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded

7. 建立本機的站台 VPN 閘道實體。 此命令不會設定內部部署 VPN 閘道。 相反地，它可讓您提供本機閘道器設定 (例如公用 IP 與內部位址空間)，使 Azure VPN 閘道能夠與其連線。

    > [AZURE.IMPORTANT] 未在 netcfg 中定義站台對站台 VPN 的本機站台。 您必須改為使用此 Cmdlet 來指定本機站台參數。 您無法使用 Azure 傳統入口網站或 Netcfg 檔來定義參數。

    使用下列範例，將該值替換為您自己的值。

    `New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

    > [AZURE.NOTE] 如果您的區域網路有多個路由，您可以傳遞它們全都放在做為陣列。  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  


    To retrieve the virtual network gateway settings, including the gateway ID and the public IP, use the `Get-AzureVirtualNetworkGateway` cmdlet. See the following example.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


8. 設定本機 VPN 裝置以連線到新的閘道器。 當您設定 VPN 裝置時，請使用於步驟 6 所抓取的資訊。 如需有關 VPN 裝置組態的詳細資訊，請參閱 [VPN 裝置組態](http://go.microsoft.com/fwlink/p/?linkid=615099)。

9. 將 Azure 上的站對站 VPN 閘道連結至本機閘道器。

    在此範例中，connectedEntityId 是本機閘道器識別碼，您可以透過執行 `Get-AzureLocalNetworkGateway` 找到此識別碼。 您可以使用 `Get-AzureVirtualNetworkGateway` Cmdlet 來尋找 virtualNetworkGatewayId。 完成這個步驟之後，區域網路和 Azure 之間的連線 (透過站對站 VPN 連線) 便會建立。


    `New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## 為您現有的虛擬網路設定並存的連線

如果您現有的虛擬網路已透過 ExpressRoute 或站對站 VPN 連線進行連線，則為了啟用這兩個連線以連接到現有虛擬網路，您必須先刪除現有閘道器。 這表示當您進行此設定時，本機設備將會與使用該閘道器的虛擬網路中斷連線。

**開始設定之前 ︰** 確認您擁有足夠的 IP 位址在虛擬網路中，因此能夠增加閘道子網路的大小。

1. 下載最新版的 PowerShell Cmdlet。 您可以從下載並安裝最新的 PowerShell cmdlet 的 PowerShell 區段 [下載頁面](http://azure.microsoft.com/downloads/)。

2. 刪除現有的站對站 VPN 閘道。 使用下列 Cmdlet，將該值替換為您自己的值。

    `Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. 匯出虛擬網路的結構描述。 使用下列 PowerShell Cmdlet，將該值替換為您自己的值。

    `Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

3. 編輯網路組態檔結構描述，讓閘道器子網路是 /27 或更短的首碼 (例如 /26 或 /25)。 請參閱下列範例。 如需使用網路組態檔的詳細資訊，請參閱 [設定虛擬網路使用網路組態檔](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。 如需組態結構描述的詳細資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

4. 如果您先前的閘道是站台對站台 VPN，您也必須變更連線類型 **專用**。

                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>

5. 此時，您必須使用沒有閘道器的 VNet。 若要建立新的閘道，並完成連線，您可以繼續進行 **步驟 4** 本文中，這一節中 [ExpressRoute 和站台對站連線，以建立新的虛擬網路](#create-a-new-virtual-network-with-both-expressroute-and-site-to-site-connectivity)。

## 後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)


