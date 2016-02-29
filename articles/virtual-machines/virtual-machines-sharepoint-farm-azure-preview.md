<properties
    pageTitle="建立 SharePoint 伺服器陣列 | Microsoft Azure"
    description="透過 Azure 入口網站的 Marketplace，快速建立新的基本或高可用性 SharePoint Server 2013 伺服器陣列。"
    services="virtual-machines"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="josephd"/>

# 建立 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統的模型。

透過 Microsoft Azure 入口網站的 Marketplace，您可以快速建立預先設定的 SharePoint Server 2013 伺服器陣列。 當您在開發和測試環境中需要基本或高可用性 SharePoint 伺服器陣列時，或是您要評估將 SharePoint Server 2013 做為組織的共同作業方案時，這將可為您省下許多時間。

> [AZURE.NOTE]  **SharePoint 伺服器陣列** Azure 入口網站的 Azure Marketplace 中的項目已移除。

基本的 SharePoint 伺服器陣列由這個組態中的三部虛擬機器所組成。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

此伺服器陣列組態可用於 SharePoint 應用程式開發的簡化設定，或用於您第一次的 SharePoint 2013 評估。

若要建立基本 (三部伺服器) 的 SharePoint 伺服器陣列：

1. 按一下 [ [這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)。
2. 按一下 [ **部署**。
3. 在 **SharePoint 2013 非高可用性伺服器陣列** ] 窗格中，按一下 [ **建立**。
4. 指定的步驟 7 上設定 **建立 SharePoint 2013 非高可用性伺服器陣列** ] 窗格中，然後再按一下 **建立**。

高可用性 SharePoint 伺服器陣列由下列組態中的九個虛擬機器組成。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

此伺服器陣列組態可用來測試較高的用戶端負載、外部 SharePoint 網站的高可用性，以及 SharePoint 伺服器陣列的 SQL Server AlwaysOn。 此組態也可用於高可用性環境中的 SharePoint 應用程式開發。

若要建立高可用性 (九部伺服器) 的 SharePoint 伺服器陣列：

1. 按一下 [ [這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)。
2. 按一下 [ **部署**。
3. 在 **Sharepoint 2013 HA** ] 窗格中，按一下 [ **建立**。
4. 指定的步驟 7 上設定 **建立 SharePoint 2013 高可用性伺服器陣列** ] 窗格中，然後再按一下 **建立**。

> [AZURE.NOTE] Microsoft 已發行 SharePoint Server 2016 IT Preview。 若要輕鬆安裝和測試此預覽版，您可以搭配 SharePoint Server 2016 IT 預覽版和其預先安裝的必要元件來使用 Azure 虛擬機器資源庫映像。 如需詳細資訊，請參閱 [測試 Azure 中的 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 管理 SharePoint 伺服器陣列

您可以透過遠端桌面連接管理這些伺服器陣列的伺服器。 如需詳細資訊，請參閱 [登入虛擬機器](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine)。

在「管理中心 SharePoint」網站中，您可以設定「我的網站」、SharePoint 應用程式和其他功能。 如需詳細資訊，請參閱 [設定 SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx)。

> [AZURE.NOTE] Azure 入口網站會建立這兩種伺服器陣列僅限雲端虛擬網路中具有網際網路對向網站空間。 沒有任何站對站 VPN 或 ExpressRoute 連線會連回您的組織網路。

## 其他資源

[在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

