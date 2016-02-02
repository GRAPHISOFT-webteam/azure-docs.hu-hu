<properties
    pageTitle="Azure 中的混合式雲端測試環境 | Microsoft Azure"
    description="尋找說明如何為您的 Azure 架構混合式雲端建置開發/測試或概念證明 IT 專業人員環境的文章。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="index-page"
    ms.date="09/16/2015"
    ms.author="josephd"/>


# Azure 混合式雲端測試環境

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


開發/測試或概念證明，混合式雲端測試環境會使用本機的網際網路連線，以及其中一個公用 IP 位址，並引導您逐步設定正常運作，跨單位 Azure 虛擬網路 (VNet)。 完成後，您可以開發和測試應用程式、試驗簡化的 IT 工作負載，以及根據您在網際網路上的位置，評估相對的站對站虛擬私人網路 (VPN) 連線效率。

## 混合式雲端基本設定

[混合式雲端基本設定](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) 所組成:

- 簡化的內部部署網路與 4 個虛擬機器 (網域控制器、應用程式伺服器、用戶端電腦以及執行 Windows Server 和「路由及遠端存取」的 VPN 裝置)
- Azure 虛擬網路與複本網域控制站
- 站對站 VPN 連線

## 混合式雲端中的 SharePoint 內部網路伺服器陣列

[混合式雲端測試環境中的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) 將 SQL Server 2014 伺服器和 SharePoint Server 2013 伺服器加入至混合式雲端基本設定。 這會建立兩層式 SharePoint 伺服器陣列，您可以從簡化的內部部署網路上的用戶端電腦存取。

## 混合式雲端中的網頁型 LOB 應用程式

[混合式雲端測試環境中的 web 型 LOB 應用程式](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) 將 SQL Server 2014 伺服器和網際網路資訊服務 (IIS) 伺服器加入至混合式雲端基本設定。 這樣會建立基礎結構，讓您可以在其中部署和測試分層式網頁型 LOB 應用程式。

## 混合式雲端中的 Office 365 目錄同步作業 (DirSync) 伺服器

[混合式雲端測試環境中的 Office 365 DirSync 伺服器](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) 將 DirSync 伺服器加入至混合式雲端基本設定，並且示範 Office 365 DirSync 與試用版的 Office 365 訂用帳戶的密碼同步處理。

## 模擬混合式雲端測試環境

組織和個人的直接網際網路連線和公用 IP 位址尚未就緒時， [模擬混合式雲端測試環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) 會建立在不同的 Azure 虛擬網路的簡化的內部網路，然後利用 VNet 對 VNet VPN 連線連接兩個虛擬網路。


## 其他資源

[在 Azure 基礎結構服務中裝載的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 基礎結構服務工作負載: 高可用性系列商務應用程式](virtual-machines-workload-high-availability-LOB-application.md)

[部署 Office 365 目錄同步作業 (DirSync) 在 Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)





