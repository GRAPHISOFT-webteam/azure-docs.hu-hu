<properties
   pageTitle="使用 Azure 傳統入口網站建立具有站對站 VPN 閘道連線的虛擬網路 | Microsoft Azure"
   description="使用傳統部署模型，針對跨部署與混合式組態建立 VNet 搭配 S2S VPN 閘道連線。"
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="cherylmc"/>


# 使用 Azure 傳統入口網站建立具有站對站 VPN 連線的虛擬網路

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


本文將逐步引導您建立虛擬網路以及內部部署網路的站對站 VPN 連線。 本文適用於傳統部署模型。 如果您要尋找此組態的不同部署模型，請使用以上的索引標籤來選取您想要的文章。 如果您想要將 Vnet 連接在一起，但不是會建立連線至內部部署位置，請參閱 [設定 VNet 對 VNet 連線](virtual-networks-configure-vnet-to-vnet-connection.md)。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 開始之前

在開始設定之前，請確認您具備下列項目。

- 相容的 VPN 裝置 (以及能夠進行設定的人員)。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您將需要與能夠提供那些詳細資料的人協調。

-  您的 VPN 裝置對外開放的公用 IP 位址。 此 IP 位址不能位於 NAT 後方。

- Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用您 [MSDN 訂閱者權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 或申請 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。


## 建立虛擬網路

1. 登入 **Azure 傳統入口網站**。

2. 按一下螢幕左下角的 [新增]****。 在導覽窗格中依序按一下 [網路服務]**** 和 [虛擬網路]****。 按一下 [Custom Create]**** 開始組態精靈。

3. 在下列頁面上填寫資訊來建立 VNet。

## 虛擬網路詳細資料頁面

輸入以下資訊：

- **名稱**：為虛擬網路命名。 例如，*EastUSVNet*。 當您部署 VM 和 PaaS 執行個體時，將會使用此虛擬網路名稱，因此您可能不會想要太過複雜的名稱。
- **位置**：位置會與您要存放資源 (VM) 的實體位置 (區域) 直接相關。 例如，如果想要您部署到此虛擬網路的 VM 實際上位於 *美國東部*，請選取該位置。 建立關聯之後，您就無法變更與虛擬網路相關聯的區域。

## DNS 伺服器和 VPN 連線能力頁面

輸入下列資訊，然後按一下右下角的 [下一步] 箭頭。

- **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從捷徑功能表中選取先前註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器，它可讓您指定您想要用於此虛擬網路的名稱解析的 DNS 伺服器。
- **設定站台對站台 VPN**：選取 [**設定站台對站台 VPN**] 的核取方塊。
- **區域網路**：表示實體內部部署位置的本機網路。 您可以選取先前建立的區域網路，或者可以建立新的區域網路。 不過，如果您真的選取使用您先前建立的區域網路，您會想要移至 [**區域網路**] 組態頁面，並確定您對此連線使用之 VPN 裝置的 IP 位址 (公開 IPv4 位址) 正確無誤。

## 站台對站台連線能力頁面

如果您正在建立新的區域網路，將看到 [**站台對站台連線能力**] 頁面。 如果您想要使用先前建立的區域網路，此頁面不會出現在精靈中，而您可以移至下一節。

輸入下列資訊，然後按一下 [下一步] 箭頭。

-   **名稱**：您想要命名區域 (內部部署) 網站的名稱。
-   **VPN 裝置 IP 位址**：這是您將用來連接到 Azure 之內部部署 VPN 裝置的公開 IPv4 位址。 VPN 裝置不能位於 NAT 後方。
-   **位址空間**：包含起始 IP 和 CIDR (位址計數)。 這是您指定想要透過虛擬網路閘道，傳送至本機內部部署位置的位址範圍位置。 如果目的地 IP 位址落在此處指定的範圍內，將會透過虛擬網路閘道進行路由傳送。
-   **加入位址空間**：如果您有多個想要透過虛擬網路閘道傳送的位址範圍，這是您指定每個額外位址範圍的位置。 稍後您可以在 [區域網路]**** 頁面上新增或移除範圍。

## 虛擬網路位址空間頁面

指定您想要用於虛擬網路的位址範圍。 這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。

特別重要的是，您選取的範圍不得與用於內部部署網路的任何範圍重疊。 您必須與您的網路管理員協調。 您的網路管理員可能需要從內部部署網路位址空間中切割出 IP 位址範圍，以供您用於虛擬網路。

輸入下列資訊，然後按一下右下角的核取記號來設定您的網路。

- **位址空間**：包括起始 IP 和位址計數。 請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。
- **新增子網路**：包括起始 IP 和位址計數。 不需要其他子網路，但是您可以為將擁有靜態 DIP 的 VM 建立個別的子網路。 或者，您可以讓您的 VM 位於與其他角色執行個體不同的子網路中。
- **新增閘道子網路**：按一下以新增閘道子網路。 閘道器子網路僅用於虛擬網路閘道，而且為這個組態的必要項目。

按一下頁面底部的核取記號，然後您的虛擬網路即會開始建立。 完成時，您將在 Azure 傳統入口網站的 [網路]**** 頁面上看到 [狀態]**** 下列出 [已建立]****。 建立了 VNet 之後，您便可設定虛擬網路閘道。

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## 設定虛擬網路閘道

接著，您將設定虛擬網路閘道器，以便建立安全的站對站 VPN 連線。 請參閱 [Azure 傳統入口網站中設定虛擬網路閘道](vpn-gateway-configure-vpn-gateway-mp.md)。

## 後續步驟

您可以將虛擬機器加入您的虛擬網路。 請參閱 [如何建立自訂的虛擬機器](../virtual-machines/virtual-machines-create-custom.md)。

如果您想要設定傳統的虛擬網路與使用 Azure 資源管理員模式所建立的虛擬網路之間的連線，請參閱 [傳統 Vnet 連接到資源管理員的 Azure Vnet](../virtual-network/virtual-networks-arm-asm-s2s-howto.md)。





