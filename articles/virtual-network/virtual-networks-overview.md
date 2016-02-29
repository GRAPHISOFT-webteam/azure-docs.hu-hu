<properties
   pageTitle="Azure 虛擬網路 (VNet) 概觀"
   description="了解 Azure 中的虛擬網路 (VNet)"
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

# 虛擬網路概觀

Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。  它是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您可以完全控制此網路內的 IP 位址區塊、DNS 設定、安全性原則和路由表。 您可以也進一步將 VNet 分成子網路，並啟動 Azure IaaS 虛擬機器 (Vm) 和/或 [雲端服務 (PaaS 角色執行個體)](cloud-services-choose-me.md)。 另外，您可以將虛擬網路連線到內部網路使用的其中一個 [連線選項](vpn-gateway-cross-premises-options.md) Azure 中可用。 基本上，您可以將您的網路延伸至 Azure，透過 Azure 提供的企業級好處完整控制 IP 位址區塊。 

若要進一步了解 VNet，請看下圖顯示的簡化內部部署網路。

![內部部署網路](./media/virtual-networks-overview/figure01.png)

上圖顯示透過路由器連線至公用網際網路的內部部署網路。 您也可以看到在路由器與裝載 DNS 伺服器和 Web 伺服器陣列的 DMZ 之間有一道防火牆。 Web 伺服器陣列採用對網際網路公開的硬體負載平衡器來進行負載平衡，並且會耗用內部子網路的資源。 內部子網路藉由另一道防火牆來與 DMZ 分開，並裝載 Active Directory 網域控制站伺服器、資料庫伺服器和應用程式伺服器。

相同的網路可以裝載在 Azure 中，如下圖所示。

![Azure 虛擬網路](./media/virtual-networks-overview/figure02.png)

請注意 Azure 基礎結構擔任路由器角色的方式，允許從 VNet 存取公用網際網路，而不需要任何組態。 防火牆可以換成套用至每個個別子網路的網路安全性群組 (NSG)。 而實體負載平衡器可以換成 Azure 中面向網際網路的內部負載平衡器。

>[AZURE.NOTE] 在 Azure 中有兩種部署模式: 傳統 (也稱為服務管理) 和 Azure 資源管理員 (ARM)。 傳統 VNet 可以加入同質群組，或建立成區域 VNet。 如果您有 VNet 在同質群組時，建議 [移轉至區域 VNet](./virtual-networks-migrate-to-regional-vnet.md)。 

## 虛擬網路的優點

- **隔離**。 VNet 與彼此完全隔離。 這樣可以讓您為使用相同 CIDR 位址區塊的開發、測試和生產環境建立不相連的網路。

- **公用網際網路存取**。 依預設，VNet 中的所有 IaaS VM 和 PaaS 角色執行個體都可以存取公用網際網路。 您可以使用網路安全性群組 (NSG) 來控制存取。

- **存取 VNet 中 Vm**。 PaaS 角色執行個體和 IaaS VM 可以在相同虛擬網路中啟動，並可使用私人 IP 位址彼此連線，即使它們位於不同的子網路也一樣，而且不需要設定閘道器或使用公用 IP 位址。

- **名稱解析**。 Azure 會針對部署在 VNet 中的 IaaS VM 和 PaaS 角色執行個體提供內部名稱解析。 您也可以部署自己的 DNS 伺服器，並設定 VNet 來使用它們。

- **安全性**。 您可以使用網路安全性群組來控制 VNet 中進出虛擬機器和 PaaS 角色執行個體的流量。

- **連線**。 透過站對站 VPN 連線或 ExpressRoute 連線，VNet 可以彼此連線，甚至連線至您的內部部署資料中心。 若要深入了解 VPN 閘道，請瀏覽 [關於 VPN 閘道](./vpn-gateway-about-vpngateways.md)。 若要深入了解 ExpressRoute，請造訪 [ExpressRoute 技術概觀](./expressroute-introduction.md)。

    >[AZURE.NOTE] 請確定您建立 VNet，再將任何 Iaas 或 PaaS 角色執行個體部署到 Azure 環境。 ARM 架構的 VM 需要 VNet，而且如果沒有指定現有的 VNet，Azure 建立的預設 VNet 可能會與您的內部部署網路產生 CIDR 位址區塊衝突， 這會使您無法將 VNet 連線至內部部署網路。
    
## 子網路

子網路是 VNet 中某個範圍的 IP 位址，您可以將 VNet 分割成多個子網路以便進行組織和獲得安全性。 部署至 VNet 內 (相同或不同) 子網路的 VM 和 PaaS 角色執行個體不需要進行額外設定就可以彼此通訊。 您也可以設定子網路的路由表和 NSG。

## IP 位址


有兩種類型的 IP 位址指派給 Azure 中的資源: *公用* 和 *私人*。 公用 IP 位址允許網際網路和其他 Azure 公開服務進行通訊的 Azure 資源像 [Azure Redis 快取](https://azure.microsoft.com/services/cache/), ，[Azure 事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)。 私人 IP 位址可讓虛擬網路中的資源以及透過 VPN 連線的資源彼此通訊，而不必使用可路由的 IP 位址。 

若要深入了解在 Azure 中的 IP 位址，請瀏覽 [虛擬網路中的 IP 位址](virtual-network-ip-addresses-arm.md)

## Azure 負載平衡器

虛擬網路中的虛擬機器和雲端服務可以使用 Azure 負載平衡器對網際網路公開。 對內提供的企業營運應用程式則可以使用內部負載平衡器來平衡負載。 

- **外部負載平衡器**。 您可以使用外部負載平衡器，以提供從公用網際網路存取 IaaS VM 和 PaaS 角色執行個體時的高可用性。

- **內部負載平衡器**。 您可以使用內部負載平衡器，以提供從 VNet 中其他服務存取 IaaS VM 和 PaaS 角色執行個體時的高可用性。

若要深入了解 Azure 中的負載平衡，請造訪 [負載平衡器概觀](../load-balancer-overview.md)。

## 網路安全性群組 (NSG)

您可以建立 NSG 來控制對網路介面 (NIC)、VM 和子網路的輸入和輸出存取。 每個 NSG 都有包含一個或多個規則，指定是否要根據來源 IP 位址、來源連接埠、目的地 IP 位址和目的地連接埠來核准或拒絕流量。 若要深入了解 Nsg，請瀏覽 [什麼是網路安全性群組](../virtual-networks-nsg.md)。

## 虛擬應用裝置

虛擬應用裝置只是 VNet 中的另一個 VM，負責執行以軟體為基礎的應用裝置功能，例如防火牆、WAN 最佳化或入侵偵測。 您可以在 Azure 中建立路由，將 VNet 流量的路由設定為透過虛擬應用裝置來使用其功能。

比方說，NSG 可以用來在 VNet 上提供安全性。 不過，NSG 會提供第 4 層存取控制清單 (ACL) 給傳入和傳出的封包。 如果您想要使用第 7 層安全性模型，則需要使用防火牆應用裝置。

虛擬應用裝置相依於 [使用者定義的路由和 IP 轉送](../virtual-networks-udr-overview.md)。

## 限制
允許在訂閱中的虛擬網路數目限制，請參閱 [Azure 網路限制](azure-subscription-service-limits.md#networking-limits) 如需詳細資訊。

## 定價
在 Azure 中使用虛擬網路並沒有其他費用。 啟動 Vnet 中的計算執行個體將會收取標準費率中所述 [Azure VM 定價](https://azure.microsoft.com/pricing/details/virtual-machines/)。  [VPN 閘道](https://azure.microsoft.com/pricing/details/vpn-gateway/) 和 [公用 IP 位址] (https://azure.microsoft.com/pricing/details/ip-addresses/) 用在 VNet 中也會充電的標準費率。 

## 後續步驟

- [建立 VNet](../virtual-networks-create-vnet-arm-pportal.md) 和子網路。
- [在 VNet 中建立 VM](../virtual-machines-windows-tutorial.md)。
- 深入了解 [Nsg](../virtual-networks-nsg.md)。
- 深入了解 [負載平衡器](../load-balancer-overview.md)。
- [保留內部 IP 位址](../virtual-networks-reserved-private-ip.md)
- [保留公用 IP 位址](../virtual-networks-reserved-public-ip.md)。
- 深入了解 [使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)。


