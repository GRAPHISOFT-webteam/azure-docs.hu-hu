<properties
    pageTitle="在 VM 上的 SQL Server 應用程式模式 | Microsoft Azure"
    description="這篇文章涵蓋適用於 Azure VM 上的 SQL Server 應用程式模式。它們可為解決方案架構師和開發人員提供基礎良好的應用程式架構和設計。"
    services="virtual-machines"
    documentationCenter="na"
    authors="Selcin"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="12/04/2015"
    ms.author="selcint" />


# Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## 摘要：

判斷哪種或哪些應用程式模式，適用於 Azure 環境中以 SQL Server 為基礎的應用程式，是很重要的設計決策，這需要深入了解 SQL Server 和 Azure 的每個基礎結構元件是如何搭配運作。 您可以透過 Azure 基礎結構服務中的 SQL Server，輕鬆地維護和監視建置於 Windows Server 上的現有 SQL Server 應用程式，或移轉至 Azure 的虛擬機器中。

本文的目的在於為解決方案架構師和開發人員提供基礎良好的應用程式架構和設計，讓他們將現有應用程式移轉至 Azure，以及在 Azure 中開發新應用程式時可以採用。

每個應用程式模式都有提供內部部署案例、啟用雲端功能的個別解決方案，以及相關的技術建議。 此外，本文還討論特定的 Azure 開發策略，讓您可以正確地設計應用程式。 由於存在許多可能的應用程式模式，因此建議架構師和開發人員應該針對其應用程式和使用者，選擇最適合的應用程式模式。

**技術參與者：**Luis Carlos Vargas Herring、Madhan Arumugam Ramakrishnan

**技術審稿人員：**Corey Sanders、Drew McDaniel、Narayan Annamalai、Nir Mashkowski、Sanjay Mishra、Silvano Coriani、Stefan Schackow、Tim Hickey、Tim Wieman、Xin Jin

## 簡介

您可以藉由分隔出不同機器上的不同應用程式層元件，以及個別元件，開發各種類型的多層式架構應用程式。 例如，您可以將用戶端應用程式和商務規則元件放在一部機器中，前端 Web 層和資料存取層元件放在另一部機器，然後再將後端資料庫層放在另一部機器。 這種結構有助於分離各個層。 如果您變更資料來源，則您不需要變更用戶端或 Web 應用程式，只要變更資料存取層元件即可。

典型的*多層式架構*應用程式包含展示層、商務層和資料層：


| 層| 說明|
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **展示**| *展示層* (Web 層、前端層) 是使用者與應用程式互動的層。|
| **商務**| *商務層* (中間層) 是讓展示層和資料層彼此通訊的層，並含有系統的核心功能。|
| **資料**| *資料層*基本上是儲存應用程式資料的伺服器 (例如，執行 SQL Server 的伺服器)。|

應用程式層描述應用程式中功能與元件的邏輯群組；而層則描述個別實體伺服器、電腦、網路或遠端位置上功能與元件的實體分佈。 應用程式層可位於同一部實體電腦 (同一層)，或分散在不同電腦 (多層式架構)，而每個層中的元件可透過定義明確的介面，與其他層中的元件進行通訊。 您可以將「層」這個詞彙視為實體分佈模式，例如兩層、三層和多層式架構。 **2 層應用程式模式**包含兩個應用程式層：應用程式伺服器和資料庫伺服器。 應用程式伺服器和資料庫伺服器之間會進行直接通訊。 應用程式伺服器包含 Web 層和商務層元件。 在 **3 層應用程式模式**中，有三個應用程式層：Web 伺服器、應用程式伺服器 (其中包含商務邏輯層和/或商務層資料存取元件)，以及資料庫伺服器。 Web 伺服器和資料庫伺服器之間會透過應用程式伺服器進行通訊。 如需應用程式層級和層級的詳細資訊，請參閱 [Microsoft 應用程式架構指南](https://msdn.microsoft.com/library/ff650706.aspx)。

開始閱讀本文之前，您應該先了解 SQL Server 和 Azure 的基本概念。 如需資訊，請參閱 [SQL Server 線上叢書 》](https://msdn.microsoft.com/library/bb545450.aspx), ，[Azure 虛擬機器中 SQL Server](virtual-machines-sql-server-infrastructure-services.md) 和 [Azure.com](http://azure.microsoft.com)。

本文會說明一些應用程式模式，它們適用於簡單的應用程式以及複雜度高的企業應用程式。 之前詳述每個模式，我們建議，您應該熟悉可用的資料儲存體服務在 Azure 中，例如 [Azure 儲存體](../storage/storage-introduction.md), ，[Azure SQL Database](../sql-database/sql-database-technical-overview.md), ，和 [Azure 虛擬機器中的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。 若要為應用程式做出最佳設計決策，您必須清楚地了解何時要使用哪一種資料儲存體服務。

### 若有下列情況，請選擇使用 Azure 虛擬機器中的 SQL Server：

- 您需要控制 SQL Server 和 Windows。 例如，這可能包括 SQL Server 版本、特殊的 Hotfix、效能組態等等。

- 您需要與 SQL Server 內部部署完全相容，且想要將現有應用程式按原狀移至 Azure。

- 您想要利用 Azure 環境的功能，但 Azure SQL Database 不支援您應用程式需要的所有功能。 這可能包括下列部分：

    - **資料庫大小**：本文更新時，SQL Database 支援的資料庫最多可有 500 GB 的資料。 如果應用程式需要超過 500 GB 的資料，且您不想要實作自訂分區化解決方案，則建議使用 Azure 虛擬機器中的 SQL Server。 如需最新資訊，請參閱 [向外擴充 Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx) 和 [Azure SQL Database 服務層和效能層級](../sql-database/sql-database-service-tiers.md)。
    - **HIPAA 符合性**: 醫療保健客戶和獨立軟體廠商 (Isv) 可能會選擇 [Azure 虛擬機器中 SQL Server](virtual-machines-sql-server-infrastructure-services.md) 而不是 [Azure SQL Database](../sql-database/sql-database-technical-overview.md) 因為 Azure 虛擬機器中的 SQL Server 所涵蓋的 HIPAA 商務關聯協議 (BAA)。 如需相容性資訊，請參閱 [Microsoft Azure 信任中心: 相容性](http://azure.microsoft.com/support/trust-center/compliance/)。
    - **執行個體層級功能**：目前 SQL Database 不支援存在於資料庫外的功能 (例如連結的伺服器、代理程式作業、FileStream、Service Broker 等等)。 如需詳細資訊，請參閱 [Azure SQL Database 方針和限制](https://msdn.microsoft.com/library/azure/ff394102.aspx)。

## 1 層 (簡單)：單一虛擬機器

在此應用程式模式中，您會將 SQL Server 應用程式和資料庫部署到 Azure 中的獨立虛擬機器。 同一個虛擬機器會包含您的用戶端/Web 應用程式、商務元件、資料存取層和資料庫伺服器。 展示、商務和資料存取的程式碼會以邏輯方式分隔，但實際上位於同一部伺服器機器中。 大部分客戶會從此應用程式模式開始，然後將更多 Web 角色或虛擬機器加入系統，以向外延展。

若有下列情況，此應用程式模式會很適用：

- 您想要執行簡單的移轉到 Azure 平台，以評估該平台是否會回應應用程式的需求。

- 您想要將所有應用程式層裝載在同一 Azure 資料中心的同一個虛擬機器中，以降低各層之間的延遲。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

下圖說明簡單的內部部署案例，以及您如何能夠在 Azure 的單一虛擬機器內，部署其啟用雲端功能的解決方案。

![1 層應用程式模式](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728008.png)

在與展示層相同的實體層上部署商務層 (商務邏輯和資料存取元件) 可以將應用程式效能最大化，除非您因為延展性或安全性問題，必須使用不同的層。

因為若要開始，這是很常見的模式，會很有用的下列文件移轉的將資料移至您的 SQL Server VM: [資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database.md)。

## 3 層 (簡單)：多個虛擬機器

在此應用程式模式中，您會將每個應用程式層放在不同的虛擬機器中，以在 Azure 中部署 3 層應用程式。 這麼做可提供彈性的環境，以便輕鬆地向上和向外延展案例。 當一個虛擬機器包含您的用戶端/Web 應用程式時，另一個可裝載您的商務元件，而另一個則可裝載資料庫伺服器。

若有下列情況，此應用程式模式會很適用：

- 您想要將複雜的資料庫應用程式移轉至 Azure 虛擬機器。

- 您想要在不同的區域裝載不同的應用程式層。 例如，您可能會為了報告用途，而有部署到多個區域的共用資料庫。

- 您想要將企業應用程式，從內部部署虛擬化平台移動到 Azure 虛擬機器。 企業應用程式的詳細討論，請參閱 [企業應用程式是什麼](https://msdn.microsoft.com/library/aa267045.aspx)。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

下圖說明如何將每個應用程式層放在不同的虛擬機器中，以在 Azure 內設置簡單的 3 層應用程式。

![3 層應用程式模式](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728009.png)

在此應用程式模式中，每一層只有一個虛擬機器 (VM)。 如果您在 Azure 中有多個 VM，我們建議您設定虛擬網路。 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 建立信任的安全性界限，也可讓 Vm 彼此之間通訊的私人 IP 位址。 此外，請務必確認所有網際網路連線只會進入展示層。 這表示您應該開啟展示層 (而不是其他層) 上的公用端點。 採用此應用程式模式時，您也必須在該公用連接埠上設定網路存取控制清單 (ACL)，以允許存取特定的 IP 位址。 如需詳細資訊，請參閱 [端點上管理 ACL](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint)。

在圖表中，網際網路通訊協定可以是 TCP、UDP、HTTP 或 HTTPS。
>[AZURE.NOTE] 在 Azure 中設定虛擬網路是免費的。 不過，您必須為連接到內部部署的 VPN 閘道付費。 此費用取決於佈建和可用的連接時間量。

## 展示層向外延展的 2 層和 3 層

在此應用程式模式中，您會將每個應用程式層放在不同的虛擬機器中，以便將 2 層或 3 層資料庫應用程式部署到 Azure 虛擬機器。 此外，因為傳入的用戶端要求數量增加，所以您必須向外延展展示層。

若有下列情況，此應用程式模式會很適用：

- 您想要將企業應用程式，從內部部署虛擬化平台移動到 Azure 虛擬機器。

- 因為傳入的用戶端要求數量增加，所以想要向外延展展示層。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

- 您想要擁有可依需求相應增加和降低規模的基礎結構環境。

下圖說明為了因應傳入的用戶端要求數量增加，如何向外延展展示層，以在 Azure 的多個虛擬機器中設置應用程式層。 如圖所示，Azure 負載平衡器會負責將流量分配到多個虛擬機器，也會決定要連接到哪部 Web 伺服器。 在負載平衡器後方有多個 Web 伺服器執行個體，可確保展示層的高可用性。

![應用程式模式 - 展示層向外延展](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728010.png)

### 一層內有多個 VM 之 2 層、3 層或多層式架構模式的最佳作法

建議您將屬於同一層的虛擬機器放在相同雲端服務中，並使用相同的可用性設定組。 例如，將一組 Web 伺服器放在 **CloudService1** 和 **AvailabilitySet1**，然後將一組資料庫伺服器放在 **CloudService2** 和 **AvailabilitySet2**。 Azure 中的可用性設定組可讓您將高可用性節點放入分隔的容錯網域和升級網域。

若要利用某一層中的多個 VM 執行個體，您必須在應用程式層之間設定 Azure 負載平衡器。 若要設定每一層的負載平衡器，請分別在每一層的 VM 上建立負載平衡的端點。 若為特定的某一層，請先在相同雲端服務中建立 VM。 這樣可確保它們都有相同的公用虛擬 IP 位址。 接下來，在該層的其中一個虛擬機器上建立端點。 然後將同一個端點指派到該層上的其他虛擬機器，以進行負載平衡。 您可以藉由建立負載平衡集，將流量分配到多個虛擬機器，並在後端 VM 節點失敗時，讓負載平衡器決定要連接的節點。 例如，在負載平衡器後方有多個 Web 伺服器執行個體，可確保展示層的高可用性。

就最佳作法而言，請務必使所有網際網路連線先進入展示層。 展示層會存取商務層，而商務層會存取資料層。 例如，開啟展示層上的端點。 每個端點都有一個公用連接埠和一個私人連接埠。 虛擬機器會在內部利用私人連接埠來接聽該端點上的流量。 公用連接埠是從 Azure 外部進行通訊的進入點，並且由 Azure 負載平衡器使用。 建議您設定網路存取控制清單 (ACL) 來定義規則，以協助隔離和控制任一應用程式層上，所有公用端點之公用連接埠上的傳入流量。 如需詳細資訊，請參閱 [端點上管理 ACL](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint)。

請注意，Azure 中的負載平衡器運作方式類似內部部署環境中的負載平衡器。 如需詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡](virtual-machines-load-balance.md)。

此外，我們建議您使用 Azure 虛擬網路，為虛擬機器設定私人網路。 這可讓虛擬機器透過私人 IP 位址彼此通訊。 如需詳細資訊，請參閱 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

## 商務層向外延展的 2 層和 3 層

在此應用程式模式中，您會將每個應用程式層放在不同的虛擬機器中，以便將 2 層或 3 層資料庫應用程式部署到 Azure 虛擬機器。 此外，您可能因為應用程式的複雜性，而想要將應用程式伺服器元件分配到多個虛擬機器。

若有下列情況，此應用程式模式會很適用：

- 您想要將企業應用程式，從內部部署虛擬化平台移動到 Azure 虛擬機器。

- 您因為應用程式的複雜性，而想要將應用程式伺服器元件分配到多個虛擬機器。

- 您想要將商務邏輯大型內部部署 LOB (企業營運) 應用程式移動到 Azure 虛擬機器。 LOB 應用程式是一套對企業營運而言十分重要的電腦應用程式，例如會計、人力資源 (HR)、薪資、供應鏈管理和資源規劃應用。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

- 您想要擁有可依需求相應增加和降低的基礎結構環境。

下圖說明某個內部部署案例以及其啟用雲端功能的解決方案。 在此案例中，您會藉由向外延展商務層 (其中包含商務邏輯層和資料存取元件)，在 Azure 的多個虛擬機器中放置應用程式層。 如圖所示，Azure 負載平衡器會負責將流量分配到多個虛擬機器，也會決定要連接到哪部 Web 伺服器。 在負載平衡器後方有多個應用程式伺服器執行個體，可確保商務層的高可用性。 如需詳細資訊，請參閱 [一層中有多個虛擬機器的 2 層、 3 層或多層式架構應用程式模式的最佳作法](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)。

![商務層向外延展的應用程式模式](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728011.png)

## 展示層和商務層向外延展的 2 層和 3 層以及 HADR

在此應用程式模式中，您會藉由將展示層 (Web 伺服器) 和商務層 (應用程式伺服器) 元件分散到多個虛擬機器，在 Azure 虛擬機器中部署 2 層或 3 層資料庫應用程式。 此外，您會針對 Azure 虛擬機器中的資料庫，實作高可用性和災害復原解決方案。

若有下列情況，此應用程式模式會很適用：

- 您想要藉由實作 SQL Server 高可用性和災害復原功能，將企業應用程式從虛擬化平台的內部部署移至 Azure。

- 由於傳入的用戶端要求數量增加和應用程式的複雜性，您想要向外延展展示層和商務層。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

- 您想要擁有可依需求相應增加和降低的基礎結構環境。

下圖說明某個內部部署案例以及其啟用雲端功能的解決方案。 在此案例中，您會在 Azure 的多個虛擬機器中，向外延展展示層和商務層元件。 此外，您會針對 Azure 中的 SQL Server 資料庫，實作高可用性和災害復原 (HADR) 技術。

請在不同的 VM 中執行應用程式的多個複本，確認可以負載平衡它們的要求。 當您有多個虛擬機器時，必須確定可以存取所有 VM，且 VM 均能在某個時間點同時執行。 如果設定負載平衡，Azure 負載平衡器會追蹤 VM 的健全狀況，然後將傳入的呼叫正確引導至運作狀況良好的 VM 節點。 如需如何設定虛擬機器的負載平衡，請參閱 [Azure 基礎結構服務的負載平衡](virtual-machines-load-balance.md)。 在負載平衡器後方有多個 Web 和應用程式伺服器執行個體，可確保展示層和商務層的高可用性。

![向外延展和高可用性](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728012.png)

### 需要 SQL HADR 之應用程式模式的最佳作法

當您設定 SQL Server 高可用性和嚴重損壞修復解決方案在 Azure 虛擬機器時，設定您使用的虛擬機器的虛擬網路 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) ] 是必要欄位。 即使在服務停機期間，虛擬網路內的虛擬機器也會有一個穩定的私人 IP 位址，讓您可以避免 DNS 名稱解析所需的更新時間。 此外，虛擬網路可讓您將內部部署網路延伸至 Azure，並建立信任的安全性界限。 例如，如果應用程式 (例如，Windows 驗證，Active Directory) 的公司網域限制設定 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 是必要。

大部分在 Azure 上執行實際程式碼的客戶，都會在 Azure 中保留主要和次要複本。

完整的資訊及高可用性和災害復原技術的教學課程，請參閱 [高可用性和嚴重損壞修復的 SQL Server Azure 虛擬機器中](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

## 使用 Azure VM 和雲端服務的 2 層和 3 層

此應用程式模式，在您部署方式是同時使用第 2 層或 3 層應用程式至 Azure [Azure 雲端服務](cloud-services-choose-me.md#tellmecs) (web 和背景工作角色的平台即服務 (PaaS)) 和 [Azure 虛擬機器](virtual-machines/) (基礎結構即服務 (IaaS))。 使用 [Azure 雲端服務](http://azure.microsoft.com/documentation/services/cloud-services/) 展示層/商務層和中的 SQL Server [Azure 虛擬機器](virtual-machines-about.md) 資料層是在 Azure 上執行的大多數應用程式很有幫助。 原因是在雲端服務上執行的計算執行個體，可讓您輕鬆管理、部署、監視和向外延展。

有了雲端服務，Azure 就能為您維護基礎結構、執行例行性維護、修補作業系統，以及嘗試從服務和硬體故障中復原。 當您的應用程式需要向外延展時，藉由增加或減少應用程式所使用的執行個體或虛擬機器數目，即可為您的雲端服務專案自動或手動向外延展。 此外，您還可以使用內部部署 Visual Studio，將應用程式部署至 Azure 中的雲端服務專案。

簡而言之，如果您不想要產生大量的展示層/商業層管理工作，且應用程式不需要對軟體或作業系統做任何複雜的設定，請使用 Azure 雲端服務。 如果 Azure SQL Database 不支援您需要的所有功能，請針對資料層使用 Azure 虛擬機器中的 SQL Server。 在 Azure 雲端服務上執行應用程式，並將資料儲存在 Azure 虛擬機器中，可結合這兩個服務的優點。 如需詳細的比較，請參閱本主題中的區段上 [比較在 Azure 中的開發策略](#comparing-web-development-strategies-in-azure)。

在此應用程式模式中，展示層包含 Web 角色，這是在 Azure 執行環境中執行的雲端服務元件，且是針對 Web 應用程式的程式設計而自訂，並受 IIS 和 ASP.NET 的支援。 商務層或後端層包含背景工作角色，這是在 Azure 執行環境中執行的雲端服務元件，對於通用開發而言很實用，此外也可針對 Web 角色執行背景處理。 資料庫層位於 Azure 中的 SQL Server 虛擬機器內。 展示層和資料庫層之間會透過商務層 (背景工作角色元件)，直接進行通訊。

若有下列情況，此應用程式模式會很適用：

- 您想要藉由實作 SQL Server 高可用性和災害復原功能，將企業應用程式從虛擬化平台的內部部署移至 Azure。

- 您想要擁有可依需求相應增加和降低的基礎結構環境。

- Azure SQL Database 不支援您應用程式或資料庫所需的所有功能。

- 您想要針對各種工作負載層級執行壓力測試，但同時又不想一直擁有及維護許多實體機器。

下圖說明某個內部部署案例以及其啟用雲端功能的解決方案。 在此案例中，您會在 Web 角色中放置展示層、在背景工作角色中放置商務層，但在 Azure 虛擬機器中放置資料層。 在不同的 Web 角色中執行多個展示層的複本，可確保能負載平衡它們的要求。 當您合併 Azure 雲端服務與 Azure 虛擬機器時，我們建議您設定總 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 以及。 使用 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md), ，您可以擁有穩定與持續性的私人 IP 位址，在相同定域機組中雲端服務。 當您定義虛擬機器和雲端服務的虛擬網路後，它們就可以開始透過私人 IP 位址彼此通訊。 此外，需要在相同的虛擬機器和 Azure web/背景工作角色 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 提供低延遲和更安全的連線。 如需詳細資訊，請參閱 [什麼是雲端服務](../cloud-services/fundamentals-application-models.md)。

如圖所示，Azure 負載平衡器會將流量分配到多個虛擬機器，也會決定要連接到哪部 Web 伺服器或應用程式伺服器。 在負載平衡器後方有多個 Web 和應用程式伺服器執行個體，可確保展示層和商務層的高可用性。 如需詳細資訊，請參閱 [最佳作法的應用程式模式需要 SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr)。

![應用程式模式搭配雲端服務](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728013.png)

實作此應用程式模式的另一種方法是使用合併的 Web 角色 (包含展示層和商務層元件)，如下圖所示。 如果應用程式需要可設定狀態的設計，此應用程式模式就很實用。 由於 Azure web 和背景工作角色提供無狀態的計算節點，建議您實作邏輯，以儲存工作階段狀態使用的下列技術之一: [Azure 快取](http://azure.microsoft.com/documentation/services/redis-cache/), ，[Azure 資料表儲存體](../storage/storage-dotnet-how-to-use-tables.md) 或 [Azure SQL Database](../sql-database/sql-database-technical-overview.md)。

![應用程式模式搭配雲端服務](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728014.png)

## Azure VM、Azure SQL Database 與 Azure Web 應用程式的模式

此應用程式模式的主要目標是示範如何在解決方案中，將 Azure 基礎結構即服務 (IaaS) 元件與 Azure 平台即服務元件 (PaaS) 加以結合。 此模式著重於關聯式資料存放區的 Azure SQL Database。 這不包括 Azure 虛擬機器中的 SQL Server，因為其已包含在 Azure 基礎結構即服務的供應項目中。

在此應用程式模式中，您會將展示層和商務層放在同一個虛擬機器中，並存取 Azure SQL Database (SQL Database) 伺服器中的資料庫，藉此將資料庫應用程式部署到 Azure。 您可以使用傳統的 IIS 型 Web 解決方案來實作展示層。 或者，您可以藉由實作合併的簡報與商業層 [Azure Web Apps](http://azure.microsoft.com/documentation/services/app-service/web/)。

若有下列情況，此應用程式模式會很適用：

- 您在 Azure 中已設定了現有的 SQL Database 伺服器，且想要快速測試應用程式。

- 您想要測試 Azure 環境的功能。

- 您想要在短時間內快速佈建開發和測試環境。

- 您的商務邏輯和資料存取元件可以獨立在 Web 應用程式內。

下圖說明某個內部部署案例以及其啟用雲端功能的解決方案。 在此案例中，您會將應用程式層放在 Azure 的單一虛擬機器內，並存取 Azure SQL Database 中的資料。

![混合應用程式模式](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728015.png)

如果您選擇使用 Azure Web 應用程式實作結合的 Web 和應用程式層，則建議您在 Web Apps 的內容中，將中間層或應用程式層保留為動態連結程式庫 (DLL)。

此外，檢閱中提供的建議 [比較在 Azure 中的 web 開發策略](#comparing-web-development-strategies-in-azure) 這篇文章，若要深入了解程式設計技術的最後一節。

## 多層式架構混合式應用程式模式

在多層式架構混合式應用程式模式中，您會在分散於內部部署和 Azure 間的多個層中，實作應用程式。 因此，您會建立有彈性且可重複使用的混合式系統，藉此修改或新增特定的層，而不必變更其他層。 若要將公司網路延伸至雲端，您使用 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 服務。

若有下列情況，此混合式應用程式模式會很適用：

- 您想要建置部分在雲端中執行，而部分在內部部署中執行的應用程式。

- 您想要將現有內部部署應用程式的部分或所有項目移轉至雲端。

- 您想要將企業應用程式，從內部部署虛擬化平台移動到 Azure。

- 您想要擁有可依需求相應增加和降低的基礎結構環境。

- 您想要在短時間內快速佈建開發和測試環境。

- 您想要以符合成本效益的方式，取得企業資料庫應用程式的備份。

下圖示範橫跨內部部署和 Azure 的多層式架構混合式應用程式模式。 如圖所示，在內部部署基礎結構包含 [Active Directory 網域服務](https://technet.microsoft.com/library/hh831484.aspx) 網域控制站，以支援使用者驗證和授權。 請注意圖示說明的案例，其中資料層的某些部分位於內部部署資料中心，而某些部分則位於 Azure。 您可以根據應用程式的需求，實作數個其他混合式案例。 例如，您可能需要在內部部署環境中保留展示層和商務層，但在 Azure 中保留資料層。

![多層式架構的應用程式模式](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728016.png)

在 Azure 中，您可以使用 Active Directory 的獨立式雲端目錄做為您的組織，或您也可以整合現有的內部部署 Active Directory 與 [Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/)。 如圖所示，商業層元件可以存取多個資料來源，例如 [Azure 中的 SQL Server](virtual-machines-sql-server-infrastructure-services.md) 私用的內部 IP 位址，透過內部 SQL Server 透過 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md), ，或 [SQL Database](../sql-database/sql-database-technical-overview) 使用.NET Framework 資料提供者技術。 在此圖中，Azure SQL Database 是選用的資料儲存體服務。

在多層式架構混合式應用程式模式中，您可以依指定的順序，實作下列工作流程：

1. 識別需要移至雲端使用的企業資料庫應用程式 [Microsoft Assessment and Planning (MAP) Toolkit](http://microsoft.com/map)。 MAP Toolkit 會從您考慮虛擬化的電腦上，收集詳細目錄和效能資料，並提供容量和評估規劃的建議。

1. Azure 平台中需要規劃資源和組態，例如儲存體帳戶和虛擬機器。

1. 設定公司網路內部之間的網路連線能力和 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 若要設定公司網路內部部署與 Azure 虛擬機器之間的連線，請使用下列兩種方法之一：

    1. 透過 Azure 虛擬機器上的公用端點，建立內部部署與 Azure 之間的連線。 此方法提供簡單的設定，並可讓您在虛擬機器中使用 SQL Server 驗證。 此外，請在公用連接埠上設定網路存取控制清單 (ACL)，以允許存取特定 IP 位址。 如需詳細資訊，請參閱 [端點上管理 ACL](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint)。

    1. 透過 Azure 虛擬私人網路 (VPN) 通道，在內部部署與 Azure 之間建立連線。 此方法可讓您將網域原則延伸至 Azure 中的虛擬機器。 此外，您可以設定防火牆規則，並在虛擬機器中使用 Windows 驗證。 Azure 目前支援安全的網站間 VPN 和點對站 VPN 連線：

        - 您可以透過安全的網站間連線，在內部部署網路與 Azure 虛擬網路之間建立網路連線。 建議您將內部部署資料中心環境連接到 Azure。

        - 您可以透過安全的點對站連線，在 Azure 虛擬網路與在任何一處執行的電腦之間，建立網路連線。 此方法多半建議用於開發和測試。

    如需如何在 Azure 中的 SQL server 連接資訊，請參閱 [連接至 SQL Server 虛擬機器在 Azure 上](virtual-machines-sql-server-connectivity.md)。

1. 設定排程工作和警示，以在 Azure 的虛擬機器磁碟中備份內部部署資料。 如需詳細資訊，請參閱 [SQL Server 備份及還原與 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148.aspx) 和 [備份和還原 SQL Server Azure 虛擬機器中的](virtual-machines-sql-server-backup-and-restore.md)。

1. 您可以依據應用程式的需求，實作下列三種常見案例之一：

    1. 您可以在 Azure 中保留 Web 伺服器、應用程式伺服器及資料庫伺服器內的非機密資料，然後將機密資料保留在內部部署。

    1. 您可以將 Web 伺服器和應用程式伺服器保留在內部部署，而在 Azure 虛擬機器中保留資料庫伺服器。

    1. 您可以將資料庫伺服器、Web 伺服器和應用程式伺服器保留在內部部署，而在 Azure 虛擬機器中保留資料庫複本。 此設定可讓內部部署 Web 伺服器或報告應用程式存取 Azure 中的資料庫複本。 因此，您可以在內部部署資料庫中達到較低的工作負載。 我們建議您針對讀取密集的工作負載和開發用途，實作此案例。 如需在 Azure 中建立資料庫複本，請參閱在 AlwaysOn 可用性群組 [高可用性和嚴重損壞修復的 SQL Server Azure 虛擬機器中](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

## 比較 Azure 中的 Web 開發策略

若要在 Azure 中實作和部署以多層式 SQL Server 為基礎的應用程式，可以使用下列兩種程式設計方法其中之一：

- 在 Azure 中設定傳統 Web 伺服器 (IIS - 網際網路資訊服務)，並存取位於 Azure 虛擬機器中 SQL Server 內的資料庫。

- 實作雲端服務並將其部署至 Azure。 然後請確定此雲端服務可以存取 Azure 虛擬機器中 SQL Server 內的資料庫。 雲端服務可以包含多個 Web 角色和背景工作角色。

關於 Azure 虛擬機器中的 SQL Server，下表提供傳統 Web 開發與 Azure 雲端服務和 Azure Web Apps 的比較。 表格中包含 Azure Web Apps，因為它可以透過其公用虛擬 IP 位址或 DNS 名稱，使用 Azure VM 中的 SQL Server 當做 Azure Web Apps 的資料來源。

| | Azure 虛擬機器中的傳統 web 開發| Azure 中雲端服務| 使用 Azure Web 應用程式裝載的 web|
|---|---|---|---|
| **從 [內部部署應用程式移轉**| 為現有的應用程式層是。| 應用程式需要 web 和背景工作角色。| 為現有的應用程式-但適用於獨立的 web 應用程式和 web 服務需要快速擴充。|
| **開發和部署**| Visual Studio、 WebMatrix、 Visual Web Developer、 WebDeploy、 FTP、 TFS、 IIS 管理員、 PowerShell。| Visual Studio、 Azure SDK、 TFS、 PowerShell。每一個雲端服務都有兩個環境，可供您部署服務封裝和組態：預備環境和生產環境。在將雲端服務升級到生產環境之前，您可以先在預備環境中測試雲端服務。| Visual Studio、 WebMatrix、 Visual Web Developer、 FTP、 GIT、 BitBucket、 CodePlex、 DropBox、 GitHub、 Mercurial、 TFS、 Web Deploy、 PowerShell。|
| **管理與安裝程式**| 您必須負責在應用程式、 資料、 防火牆規則、 虛擬網路，以及作業系統上的系統管理工作。| 您必須負責在應用程式、 資料、 防火牆規則，以及虛擬網路上的系統管理工作。| 您必須負責在僅限資料與應用程式的系統管理工作。|
| **高可用性和災害復原 (HADR)**| 我們建議您將虛擬機器，在相同的可用性設定組和相同的雲端服務中。將 VM 置於相同的可用性設定組中，可讓 Azure 將高可用性節點放入分隔的容錯網域和升級網域。同樣地，將 Vm 放在相同的雲端服務可啟用負載平衡和 Vm 可以透過 Azure 資料中心內的本機網路直接與彼此通訊。<br/><br/>您必須負責實作高可用性和災害復原解決方案，以 SQL Server 在 Azure 虛擬機器以避免任何停機時間。支援的 HADR 技術，請參閱 [高可用性和嚴重損壞修復的 SQL Server Azure 虛擬機器中](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。<br/><br/>您負責備份自己的資料和應用程式。<br/><br/>Azure 可以移動虛擬機器，如果主機電腦中的資料中心會因硬體問題而失敗。此外，當主機電腦因安全性或軟體更新而有更新時，VM 可能會按計劃停機。因此，建議您在每個應用程式層中至少維護兩個 VM ，以確保持續可用性。Azure 沒有為單一虛擬機器提供 SLA。如需詳細資訊，請參閱 [Azure 業務持續性技術指引](https://msdn.microsoft.com/library/azure/hh873027.aspx)。| Azure 會管理從基礎硬體或作業系統軟體而導致的失敗。我們建議您實作多個 Web 角色或背景工作角色執行個體，以確保應用程式的高可用性。資訊，請參閱 [雲端服務、 虛擬機器和虛擬網路服務等級協定](http://www.microsoft.com/download/details.aspx?id=38427) 和 [嚴重損壞修復及高可用性 Azure 應用程式](https://msdn.microsoft.com/library/azure/dn251004.aspx)<br/><br/>您負責備份自己的資料和應用程式。<br/><br/>位於 Azure VM 中 SQL Server 資料庫中的資料庫，您必須負責實作高可用性和災害復原解決方案，以避免任何停機時間。適用於支援 HDAR 技術，請參閱在 Azure 虛擬機器的 SQL Server 的高可用性和嚴重損壞修復。<br/><br/>**SQL Server 資料庫鏡像**: Azure 雲端服務 (web/背景工作角色) 搭配使用。SQL Server VM 和雲端服務專案可位於相同的 Azure 虛擬網路。如果 SQL Server VM 不在相同的虛擬網路中，您便必須建立 SQL Server 別名，以將通訊路由傳送至 SQL Server 執行個體。此外，別名名稱必須符合 SQL Server 名稱。| 高可用性被繼承自 Azure 背景工作角色、 Azure blob 儲存體和 Azure SQL Database。例如，Azure 儲存體會維護所有 Blob、資料表和佇列資料的三個複本。不論何時，Azure SQL Database 都會保留三個執行中的資料複本—一個主要複本和兩個次要複本。如需詳細資訊，請參閱 [Azure 儲存體](http://azure.microsoft.com/documentation/services/storage) 和 [Azure SQL Database](../sql-database/sql-database-technical-overview.md)。<br/><br/>使用 SQL Server 時 Azure VM 中做為資料來源的 Azure Web 應用程式，請記住，Azure Web 應用程式不支援 Azure 虛擬網路。換言之，從 Azure Web Apps 到 Azure 中 SQL Server VM 的所有連線，都必須透過虛擬機器的公用端點。這可能會對高可用性和災害復原案例造成一些限制。例如，在 Azure Web Apps 上使用資料庫鏡像連接到 SQL Server VM 的用戶端應用程式，會無法連接至新的主要伺服器，因為您必須在 Azure 中的 SQL Server 主機 VM 之間設定 Azure 虛擬網路才能使用資料庫鏡像。因此，使用 **SQL Server 資料庫鏡像** 與 Azure Web 應用程式目前不支援。<br/><br/>**SQL Server AlwaysOn 可用性群組**: 在 Azure 中的 SQL Server Vm 搭配使用 Azure Web 應用程式時，您可以設定 AlwaysOn 可用性群組。但是，您需要設定 AlwaysOn 可用性群組接聽程式，將通訊路由至主要複本透過公用負載平衡的端點。|
| **跨單位連線**| 您可以使用 Azure 虛擬網路連線到內部部署上。| 您可以使用 Azure 虛擬網路連線到內部部署上。| 支援 azure 虛擬網路。如需詳細資訊，請參閱 [Web 應用程式虛擬網路整合](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)。|
| **延展性**| 藉由增加虛擬機器大小，或新增更多磁碟可向上延展。如需虛擬機器大小的詳細資訊，請參閱 [Azure 的虛擬機器大小](virtual-machines-size-specs.md)。<br/><br/>**資料庫伺服器的**: 向外延展是可透過資料庫資料分割技術和 SQL Server AlwaysOn 可用性群組。<br/><br/>大量的讀取工作負載，您可以使用 [AlwaysOn 可用性群組](https://msdn.microsoft.com/library/hh510230.aspx) 上多個次要節點，以及 SQL Server 複寫。<br/><br/>大量的寫入工作負載，您可以跨多個實體伺服器，以提供應用程式向外延展實作水平資料分割。<br/><br/>此外，您可以實作標尺外使用 [SQL Server 與資料相依路由](https://technet.microsoft.com/library/cc966448.aspx)。使用資料依存路由 (DDR) 時，您必須在用戶端應用程式 (通常是在商務層) 中，實作資料分割機制，以將資料庫要求路由傳送至多個 SQL Server 節點。業務層包含資料分割的方式和哪個節點包含的資料對應。<br/><br/>您可以調整執行虛擬機器的應用程式。如需詳細資訊，請參閱 [如何調整應用程式](../cloud-services/cloud-services-how-to-scale.md)。<br/><br/>**重要事項**: **自動調整** 在 Azure 中的功能可讓您自動增加或減少虛擬機器所使用的應用程式。這項功能可保證使用者的體驗不會在尖峰期間受到影響，且 VM 不會在需求降低時關機。如果雲端服務含有 SQL Server VM，建議您不要為雲端服務設定「自動調整規模」選項。原因是「自動調整規模」功能會讓 Azure 在該 VM 中的 CPU 使用率高於某個臨界值時，開啟虛擬機器；以及在 CPU 使用率低於該臨界值時，關閉虛擬機器。「自動調整規模」功能對於無狀態的應用很實用，例如 Web 伺服器，其中所有 VM 均可管理工作負載，且不用參考任何先前的狀態。不過，自動調整 」 功能不適用於可設定狀態的應用程式，例如 SQL Server，允許只有一個執行個體寫入資料庫。| 向上延展可透過使用多個 web 和背景工作角色。如需 web 角色和背景工作角色的虛擬機器大小的詳細資訊，請參閱 [雲端服務的設定大小](../cloud-services/cloud-services-sizes-specs.md)。<br/><br/>時使用 **雲端服務**, ，您可以定義多個角色來分散處理也達到彈性調整應用程式。每個雲端服務包含一或多個 Web 角色和/或背景工作角色，且各有自己的應用程式檔案和組態。您可以增加針對某個角色而部署的角色執行個體 (虛擬機器) 數目，藉此調升雲端服務的規模；或是減少角色執行個體數目，以調降雲端服務的規模。如需詳細資訊，請參閱 [Azure 執行模型](fundamentals-application-models.md)。<br/><br/>向外延展是可透過內建的 Azure 高可用性支援透過 [雲端服務、 虛擬機器和虛擬網路服務等級協定](http://www.microsoft.com/download/details.aspx?id=38427) 和負載平衡器。<br/><br/>多層式應用程式中，我們建議您 web/背景工作角色應用程式資料庫伺服器 Vm 透過 Azure 虛擬網路連接。此外，Azure 會為相同雲端服務中的 VM 提供負載平衡，以平均分配使用者要求。如此一來連接虛擬機器可以彼此直接透過 Azure 資料中心內的本機網路。<br/><br/>可以設定 **自動調整** Azure 傳統入口網站，以及排程的時間。如需詳細資訊，請參閱 [如何調整應用程式](../cloud-services/cloud-services-how-to-scale.md)。| **擴大和縮小**: 您可以增加/減少保留給您的網站執行個體 (VM) 的大小。<br/><br/>向外延展: 您可以為您的網站新增保留的執行個體 (Vm)。<br/><br/>可以設定 **自動調整** 入口網站，以及排程的時間。如需詳細資訊，請參閱 [如何調整 Web 應用程式](../app-service-web/web-sites-scale.md)。|

如需這些程式設計方法之間選擇的詳細資訊，請參閱 [Azure Web 應用程式、 雲端服務和 Vm: 使用?](../app-service-web/choose-web-site-cloud-service-vm.md).

## 後續步驟

如需有關如何在 Azure 虛擬機器中執行 SQL Server 的詳細資訊，請參閱 [SQL Server 的 Azure 虛擬機器總覽](virtual-machines-sql-server-infrastructure-services.md)。





