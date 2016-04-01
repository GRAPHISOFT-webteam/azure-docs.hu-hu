<properties 
    pageTitle="Azure 中的企業營運應用程式 | Microsoft Azure" 
    description="了解 Azure 中企業營運應用程式的價值、設定測試環境，以及部署高可用性組態。" 
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
    ms.date="11/09/2015" 
    ms.author="josephd"/>

# Azure 基礎結構服務工作負載：高可用性企業營運應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


在 Microsoft Azure 中設定第一個或下一個以網路為基礎的內部網路專用企業營運應用程式，這樣您就可以利用簡化組態的優點，還能夠快速擴大應用程式，使其增加新的容量。
 
您可以使用 Azure 基礎結構服務的虛擬機器和虛擬網路功能，快速部署和執行可由組織使用者存取的企業營運應用程式。 例如，您可以如此設定。

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
由於 Azure 虛擬網路是內部網路的延伸模組，並備妥所有正確命名與流量路由，您的使用者將以如同企業營運應用程式的伺服器位於內部部署資料中心的相同方式來進行存取。

這種組態可讓您透過新增新 Azure 虛擬機器輕鬆擴充企業營運應用程式，其中投入的硬體和維護成本會低於您資料中心所執行的相同企業營運應用程式。

下一步是設定裝載在 Azure 中的開發/測試企業營運應用程式。

## 建立裝載在 Azure 中的開發/測試企業營運應用程式。

跨單位虛擬網路會透過站台對站台 VPN 或 ExpressRoute 連接到內部部署網路。 如果您想要建立開發/測試環境，以模擬最後組態和實驗存取應用程式並執行遠端系統管理，透過 VPN 連線，請參閱 [進行測試的混合式雲端中設定 web 型 LOB 應用程式](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)。 

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
您可以免費建立此開發/測試環境，與您 [MSDN 訂閱](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) 或 [Azure 試用版訂閱](http://azure.microsoft.com/pricing/free-trial/)。

下一步是在 Azure 中建立高可用性的企業營運應用程式。

## 在 Azure 中部署企業營運應用程式。

在 Azure 中代表高可用性企業營運應用程式組態的基準線如下所示。

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
其中包括：

- 在 Web 和資料庫層中具有兩部伺服器的內部網路專用企業營運應用程式。
- 具有兩個執行 SQL Server 的虛擬機器以及叢集中多數節點電腦的 SQL Server AlwaysOn 組態。
- 虛擬網路中具有兩個複本網域控制站的 Active Directory 網域服務。

如需企業營運應用程式的概觀，請參閱 [列的應用程式架構藍圖](http://msdn.microsoft.com/dn630664)。

### 用料表

基準組態需要下列 Azure 服務和元件的設定組：

- 七部虛擬機器
- 四個額外資料磁碟，適用於網域控制站和執行 SQL Server 的虛擬機器。
- 三個可用性集合
- 一個跨單位虛擬網路
- 兩個儲存體帳戶

以下是適用於此組態的虛擬機器及其預設大小。

項目 | 虛擬機器描述 | 資源庫映像 | 預設大小 
--- | --- | --- | --- 
1. | 第一網域控制站 | Windows Server 2012 R2 Datacenter | D1
2. | 第二網域控制站 | Windows Server 2012 R2 Datacenter | D1
3. | 主要資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4. | 次要資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5. | 叢集的多數節點 | Windows Server 2012 R2 Datacenter | D1
6. | 第一 Web 伺服器 | Windows Server 2012 R2 Datacenter | D3
7. | 第二 Web 伺服器 | Windows Server 2012 R2 Datacenter | D3

若要計算估計的成本，這項設定，請參閱 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)。 

1. 在 **模組**, ，按一下 [ **計算**, ，然後按一下 [ **虛擬機器** 足夠時間，以建立七個虛擬機器的清單。
2. 針對每一個虛擬機器，選取：
    - 您想要的區域
    - **Windows** 類型
    - **標準** 定價層
    - 在上表或您想要的大小的預設大小 **執行個體大小**

> [AZURE.NOTE] Azure 定價計算機不包含執行 SQL Server 2014 Enterprise 的兩個虛擬機器的 SQL Server 授權的其他成本。 請參閱 [虛擬機器定價 SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql) 如需詳細資訊。

### 部署階段

若要部署此組態，請使用下列程序：

- 第 1 階段：設定 Azure。 

    使用 Azure PowerShell 來建立儲存體帳戶、可用性集合和跨單位虛擬網路。 如需詳細的設定步驟，請參閱 [第 1 階段](virtual-machines-workload-high-availability-LOB-application-phase1.md)。

- 第 2 階段：設定網域控制站 

    為虛擬網路設定兩個 Active Directory 複本網域控制站和 DNS 設定。 如需詳細的設定步驟，請參閱 [第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)。

- 第 3 階段：設定 SQL Server 基礎結構。  

    建立執行 SQL Server 和叢集的虛擬機器。 如需詳細的設定步驟，請參閱 [第 3 階段](virtual-machines-workload-high-availability-LOB-application-phase3.md)。

- 第 4 階段：設定 Web 伺服器。

    建立 Web 伺服器虛擬機器並將您的企業營運應用程式加入其中。 如需詳細的設定，請參閱 [第 4 階段](virtual-machines-workload-high-availability-LOB-application-phase4.md)。

- 第 5 階段：設定 SQL Server AlwaysOn 可用性群組。

    準備應用程式資料庫，並建立 SQL Server AlwaysOn 可用性群組，然後將應用程式資料庫新增至其中。 如需詳細的設定步驟，請參閱 [第 5 階段](virtual-machines-workload-high-availability-LOB-application-phase5.md)。

一旦設定之後，您可以透過新增更多的 Web 伺服器或執行 SQL Server 至叢集的虛擬機器，輕鬆擴充此企業營運應用程式。

## 其他資源

[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)

[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 基礎結構服務工作負載：SharePoint Server 2013 陣列](virtual-machines-workload-intranet-sharepoint-farm.md)


