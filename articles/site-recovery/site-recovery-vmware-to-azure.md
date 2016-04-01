<properties
    pageTitle="設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護" 
    description="描述 Azure Site Recovery，以協調內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器至 Azure 的複寫、容錯移轉和復原。" 
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/23/2015"
    ms.author="raynew"/>

# 設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護

本文說明如何將 Site Recovery 部署至：

- **保護 VMware 虛擬機器**— 協調複寫、 容錯移轉和復原的內部部署 VMware 虛擬機器復原到 Azure
- **保護實體伺服器**— 協調複寫、 容錯移轉和復原的內部部署實體 Windows 和 Linux 伺服器至 Azure 中使用 Azure Site Recovery 服務。

文章包含概觀、部署必要條件和設定指示。 在文章結尾，您的 VMware 虛擬機器或實體伺服器將被複寫至 Azure。
如果您遇到問題時，您張貼至 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 什麼是 Azure Site Recovery？

Azure Site Recovery 可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。 機器可以複寫至 Azure，或次要的內部部署資料中心。 深入了解 [Azure Site Recovery](site-recovery-overview.md)。

## 它如何保護內部部署資源？

站台復原可協助保護您的內部部署資源協調、 簡化複寫、 容錯移轉和容錯回復數種 [部署案例](site-recovery-overview.md)。 如果想要保護您的內部部署 VMware 虛擬機器或 Windows 或 Linux 實體伺服器，以下是 Site Recovery 如何能夠幫助：

- 可讓 VMware 使用者將虛擬機器複寫到 Azure。
- 允許將內部部署實體伺服器複寫至 Azure。
- 提供單一位置來設定和管理複寫、容錯移轉和復原。
- 提供從內部部署基礎結構容錯移轉至 Azure，以及從 Azure 至內部部署容錯回復 (還原) 的簡單方式。
- 實作復原計劃，針對連結在多部機器上的工作負載輕鬆容錯移轉。
- 提供多 VM 一致性，讓執行特定工作負載的虛擬機器和實體伺服器可以同時復原到一致的資料點。
- 支援透過網際網路、透過站對站 VPN 連線或透過 Azure ExpressRoute 進行的資料複寫。
- 提供 VMware 虛擬機器自動化的探索。


## 我需要什麼？

此圖表顯示部署元件。

![新增保存庫](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

以下是您需要的內容：

**元件** | **Deployment** | **詳細資料**
--- | --- | ---
**設定伺服器** | <p>將部署為 Azure 的標準 A3 虛擬機器在相同站台復原的訂閱。</p> <p>您在 Site Recovery 入口網站中設定</p> | 這個伺服器可在 Azure 中的受保護機器、處理序伺服器與主要目標伺服器之間協調通訊。 它會在容錯移轉發生時在 Azure 中設定複寫並協調復原。
**主要目標伺服器** | <p>部署為 Azure 虛擬機器 — OpenLogic CentOS 6.6 組件庫映像 （以保護 Linux 機器） 為基礎的其中一個 Windows server 架構的 Windows Server 2012 R2 資源庫映像 （以保護 Windows 機器） 或 Linux 伺服器。</p> <p>三個調整大小選項可供使用-標準 A4、 標準 D14 和標準 DS4。<p><p>此伺服器可與相同的 Azure 網路連接，以做為設定伺服器。</p><p>您在 Site Recovery 入口網站中設定</p> | <p>它會接收並保留複寫的資料從受保護機器使用 Azure 儲存體帳戶中的 blob 儲存體上建立的附加的 Vhd。</p> <p>選取標準 DS4 專為設定保護的工作負載需要一致的高效能和低延遲使用高階儲存體帳戶。</p>
**處理序伺服器** | <p>為執行 Windows Server 2012 R2 的內部部署虛擬或實體伺服器部署</p> <p>建議您將它放置在與您要保護之機器相同的網路與 LAN 區段上，只要受保護的機器具有 L3 網路可見性，它就可以在不同的網路上運作。<p>設定它，並註冊至 Site Recovery 入口網站中設定伺服器。</p> | <p>受保護的機器將複寫資料傳送至內部部署處理序伺服器。 它具有磁碟快取功能，可快取本身接收的複寫資料。 會對該資料執行一些動作。</p><p>它會以最佳化資料快取、 壓縮和加密，再將它傳送到主要目標伺服器。</p><p>它會處理行動服務推入安裝。</p><p>它會執行自動探索的 VMware 虛擬機器。</p>
**內部部署機器** | 在 VMware hypervisor，或是執行 Windows 或 Linux 實體伺服器上執行的內部部署虛擬機器。 | 您會設定適用虛擬機器和伺服器的複寫設定。 您可以讓個別機器容錯移轉，或更常見地，隨著包含多個虛擬機器的復原方案一起容錯移轉。
**行動服務** | <p>每個虛擬機器或您想要保護實體伺服器上安裝</p><p>可以手動安裝或推入並保護已啟用伺服器時自動安裝處理序伺服器。 | 行動服務會隨著初始複寫 (重新同步處理) 傳送資料到處理序伺服器。一旦伺服器到達受保護的狀態 (重新同步處理完成之後)，行動服務會執行寫入磁碟的記憶體內擷取，並將它傳送到處理序伺服器。 Windows 伺服器的應用程式一致性是利用 VSS 架構來達成。
**Azure Site Recovery 保存庫** | 訂閱 Site Recovery 服務之後設定。 | 您在 Site Recovery 保存庫中註冊伺服器。 保存庫可在您的內部部署網站與 Azure 之間協調資料複寫、容錯移轉及復原等作業。
**複寫機制** | <p>**透過網際網路**— 從受保護的內部部署伺服器與 Azure 透過公用網際網路連線以安全的 SSL/TLS 通訊通道的通訊與複寫資料。 這是預設選項。</p><p>**VPN/ExpressRoute**— 通訊與複寫資料在內部部署伺服器與 Azure 之間的 VPN 連線。 您必須設定站台對站 VPN 或 [ExpressRoute](../expressroute-introduction.md) 內部部署站台與 Azure 網路之間的連線。</p><p>您可以選取您想要在 Site Recovery 部署期間複寫的方式。 機制設定後若不會影響已受保護伺服器上的保護，您便無法變更機制。| <p>這兩個選項需要您開啟受保護機器上的任何輸入的網路連接埠。 所有的網路通訊是從內部部署站台起始。</p> 

您可以了解 Site Recovery 元件、 提供者和代理程式在 [Site Recovery 元件](site-recovery-components.md)。

## 容量規劃

考量的主要領域為：

- **來源環境**— VMware 基礎結構、 來源機器設定和需求。
- **元件伺服器**— 處理序伺服器、 設定伺服器和主要目標伺服器 

### 來源環境的考量

- **最大磁碟大小**— 可以連接至虛擬機器的磁碟的目前大小上限為 1 TB。 因此，可以複寫的來源磁碟的最大大小也是限制為 1 TB。
- **每個來源的大小上限**— 單一來源機器的大小上限是 31 TB （含 31 個磁碟） 和主要目標伺服器佈建一個 D14 執行個體。 
- **每個主要目標伺服器的來源數目**— 一個主要目標伺服器可以保護多個來源機器。 不過，無法跨多個主要目標伺服器保護單一來源機器，因為在磁碟複寫時，會在 Azure Blob 儲存體上建立可反映磁碟大小的 VHD，並將其附加至主要目標伺服器做為資料磁碟。  
- **每個來源的最大每日變更率**— 有三個需要考量每個來源的建議的變更速率時考量的因素。 針對目標式考量，來源上每個作業的目標磁碟需要兩個 IOP。 這是因為目標磁碟上將會發生舊資料的讀取和新資料的寫入。 
    - **每日變更率的處理序伺服器支援**— 來源機器不能跨越多個處理序伺服器。 單一處理序伺服器可以支援多達 1 TB 的每日變更率。 因此 1 TB 是來源機器支援的每日資料變更率上限。 
    - **目標磁碟支援的最大輸送量**— 每一來源磁碟的最大變換不能超過 144 GB/天 （以 8k 寫入大小）。 請參閱主要目標小節中的資料表，以取得各種寫入大小的目標的輸送量和 IOP。 必須將此數字除以 2，因為每個來源 IOP 會在目標磁碟上產生 2 個 IOP。 請參閱 [延展性和效能目標時使用高階儲存體](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) 設定高階儲存體帳戶的目標時。
    - **儲存體帳戶所支援的最大輸送量**— 來源不能跨越多個儲存體帳戶。 假設某個儲存體帳戶每秒可接受最多 20,000 個要求，並且每個來源 IOP 會在主要目標伺服器上產生 2 個 IOP，建議您將來源的 IOP 數目保留為 10,000。 請參閱 [延展性和效能目標時使用高階儲存體](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) 時來源高階儲存體帳戶的設定。

### 元件伺服器的考量

表 1 摘要說明設定和主要目標伺服器的虛擬機器大小。

**元件** | **部署的 Azure 執行個體** | **核心** | **記憶體** | **最大磁碟** | **磁碟大小**
--- | --- | --- | --- | --- | ---
設定伺服器 | 標準 A3 | 4 | 7 GB | 8 | 1023 GB
主要目標伺服器 | 標準 A4 | 8 | 14 GB | 16 | 1023 GB
 | 標準 D14 | 16 | 112 GB | 32 | 1023 GB
 | 標準 DS4 | 8 | 28 GB | 16 | 1023 GB

**表 1**

#### 處理序伺服器考量

通常處理序伺服器的大小取決於跨所有受保護的工作負載的每日變更率。 主要的考量事項包括：

-   您需要足夠的計算才能執行內嵌壓縮和加密之類的工作。
-   處理序伺服器使用磁碟快取。 確定建議的快取空間和磁碟輸送量可供使用，以在加速網路瓶頸或中斷時促進儲存的資料變更。 
-   這樣的處理序伺服器可以將資料上傳到主要目標伺服器提供持續的資料保護，請確定有足夠的頻寬。 

表 2 提供處理序伺服器指導方針的摘要。

**資料變更率** | **CPU** | **記憶體** | **快取磁碟大小**| **快取磁碟輸送量** | **輸入/輸出頻寬**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5GHz) | 4 GB | 600 GB | 每秒 7 至 10 MB | 30 Mbps/21 Mbps
300 至 600 GB | 8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz) | 6 GB | 600 GB | 每秒 11 至 15 MB | 60 Mbps/42 Mbps
600 GB 至 1 TB | 12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz) | 8 GB | 600 GB | 每秒 16 至 20 MB | 100 Mbps/70 Mbps
> 1 TB | 部署另一個處理序伺服器 | | | | 

**資料表 2**

其中： 

- 輸入是下載頻寬 (來源與處理序伺服器之間的內部網路)。
- 輸出是上傳頻寬 (處理序伺服器與主要目標伺服器之間的網際網路)。 輸出數目假設平均 30% 的處理序伺服器壓縮。
- 針對快取磁碟，建議所有的處理序伺服器的個別作業系統磁碟最少有 128 GB。
- 針對快取磁碟輸送量，使用了下列儲存體進行基準測試：採用 RAID 10 配置的 8 個 10000 轉 RPM 的 SAS 磁碟機。

#### 設定伺服器考量

每個設定伺服器可以利用 3 到 4 個磁碟區支援多達 100 個來源機器。 若已超過這些數字，建議您部署另一個設定伺服器。 請參閱表 1 以取得設定伺服器的預設虛擬機器屬性。 

#### 主要目標伺服器與儲存體帳戶的考量

每個主要目標伺服器的儲存體是由作業系統磁碟、保留磁碟區和資料磁碟組成。 在 Site Recovery 入口網站中所定義的保留時段持續時間中，保留磁碟機會維護磁碟變更的日誌。  如需虛擬機器伺服器屬性的主要目標，請參閱表 1。 表 3 顯示如何使用 A4 的磁碟。

**執行個體** | **作業系統磁碟** | **保留** | **資料磁碟**
--- | --- | --- | ---
 | | **保留** | **資料磁碟**
標準 A4 | 1 個磁碟 (1 * 1023 GB) | 1 個磁碟 (1 * 1023 GB) | 15 個磁碟 (15 * 1023 GB)
標準 D14 |  1 個磁碟 (1 * 1023 GB) | 1 個磁碟 (1 * 1023 GB) | 31 個磁碟 (15 * 1023 GB)
標準 DS4 |  1 個磁碟 (1 * 1023 GB) | 1 個磁碟 (1 * 1023 GB) | 15 個磁碟 (15 * 1023 GB)

**資料表 3**

主要目標伺服器的容量計劃仰賴於：

- Azure 儲存體效能和限制
    - 單一儲存體帳戶中標準層 VM 的高度使用的磁碟數目上限約為 40 (每一磁碟 20,000/500 IOPS)。 請參閱 [標準儲存體帳戶的延展性目標](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) 如需詳細資訊。 同樣地，請參閱 [高階儲存體帳戶的延展性目標](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) 如需有關高階儲存體帳戶。
-   每日變更率 
-   保留磁碟區儲存體。

請注意：

- 一個來源不能跨越多個儲存體帳戶。 這會套用到屬於您設定保護時選取的儲存體帳戶的資料磁碟。 作業系統和保留磁碟通常屬於自動部署的儲存體帳戶。
- 需要的保留儲存體磁碟區取決於每日變更率和保留的天數。 每個主要目標伺服器需要的保留儲存體 = 每日來自來源的變換總計 * 保留天數。 
- 每個主要目標伺服器只有一個保留磁碟區。 保留磁碟區會在連接到主要目標伺服器的磁碟之間共用。 例如：
    - 如果有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8K 大小)，則這可轉譯為每一磁碟 240 個 IOP (每一來源 IO 目標磁碟上 2 個作業)。 每一磁碟的 IOP 限制 500，在 Azure 內是 240 個 IOP。
    - 在保留磁碟區上，這會變成 120 * 5 = 600 個 IOP，而這可能會變成瓶頸。 在此案例中，加入更多磁碟到保留的磁碟區，並跨越以做為 RAID 等量磁碟區設定是不錯的策略。 因為 IOP 會分散到多個磁碟機，這將可改進效能。 要加入至保留磁碟區的磁碟機數目如下所示：
        - 來自來源環境的 IOP 總計 / 500
        - 每日來自來源環境的變換總計 (未壓縮) / 287 GB。 287 GB 是每日的目標磁碟支援的最大輸送量。 此度量會隨著寫入大小 8K 的因數而異，因為在此情況下，8K 是假設的寫入大小。 例如，如果寫入大小是 4K，則輸送量會是 287/2。 而且如果寫入大小為 16K，那麼輸送量會是 287*2。
- 儲存體帳戶所需的數目 = 來源 IOP 總計 / 10000。


## 開始之前

**元件** | **需求** | **詳細資料**
--- | --- | --- 
**Azure 帳戶** | 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。 您可以開始 [免費試用版](pricing/free-trial/)。
**Azure 儲存體** | <p>您將需要 Azure 儲存體帳戶來儲存複寫的資料</p><p>其中一個帳戶應為 [標準的地理區域備援儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage) 或 [高階儲存體帳戶](../storage/storage-premium-storage-preview-portal.md)。</p><p>此帳戶應與 Azure 站台復原服務位於相同的區域，且與相同的訂閱相關聯。</p><p>若要深入閱讀 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)</p>
**Azure 虛擬網路** | 您需要 Azure 虛擬網路以部署設定伺服器與主要目標伺服器。 需與 Azure Site Recovery 保存庫位於相同的訂用帳戶與區域中。 如果您想要透過 ExpressRoute 或 VPN 連接來複寫資料，Azure 虛擬網路必須透過 ExpressRoute 連線或站對站 VPN 連接到內部部署網路。
**Azure 資源** | 請確認您有足夠的 Azure 資源以部署所有元件。 深入了解 [Azure 訂用帳戶限制](../azure-subscription-service-limits.md)。
**Azure 虛擬機器** | <p>您想要保護的虛擬機器應該要符合 [Azure 必要條件](site-recovery-best-practices.md)。</p><p>**磁碟計數**-31 個磁碟最多可以支援單一的受保護伺服器上</p><p>**磁碟大小**— 個別磁碟容量不可超過 1023 GB</p><p>**叢集**— 不支援叢集伺服器</p><p>**開機**— 整合可延伸韌體介面 (UEFI) / 可延伸韌體 Interface(EFI) 開機不支援</p><p>**磁碟區**— 不支援加密的磁碟區的 Bitlocker</p><p> **伺服器名稱**— 名稱應包含介於 1 到 63 個字元 （字母、 數字和連字號）。 名稱必須以字母或數字開頭，並以字母或數字結尾。 機器受到保護之後，您可以修改 Azure 的名稱。</p>
**設定伺服器** | <p>Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器將建立在設定伺服器的訂閱。 它會建立為新的雲端服務中的第一個執行個體。 如果您選取公用網際網路連線類型做為設定伺服器會使用保留的公用 IP 位址建立雲端服務。</p><p>安裝路徑應該只有英文字元。</p>
**主要目標伺服器** | <p>Azure 虛擬機器，標準 A4、 D14 或 DS4。</p><p>安裝路徑應該只有英文字元。 路徑必須是例如 **/usr/local/ASR** 執行 Linux 的主要目標伺服器。</p></p>
**處理序伺服器** | <p>您可以在執行最新版更新之 Windows Server 2012 R2 的實體或虛擬機器上部署處理序伺服器。 在 C:/ 上安裝。</p><p>建議您將此伺服器放在與您要保護的機器相同的網路與子網路上。</p><p>處理序伺服器上安裝 VMware vSphere CLI 5.5.0。 VMware vSphere CLI 元件，才能處理序伺服器上探索由 vCenter 伺服器管理的虛擬機器或 ESXi 主機上執行的虛擬機器。</p><p>安裝路徑應該只有英文字元。</p><p>不支援 reFS 檔案系統。</p>
**VMware** | <p>VMware vCenter 伺服器，管理您的 VMware vSphere hypervisor。 它應該執行 vCenter 5.1 或 5.5 的版本與最新的更新。</p><p>您想要保護一或多個 vSphere hypervisor，包含 VMware 虛擬機器。 Hypervisor 應該執行 ESX/ESXi 5.1 或 5.5 版與最新的更新。</p><p>VMware 虛擬機器應該已安裝並執行 VMware 工具。</p>  
**Windows 機器** | <p>受保護實體伺服器或執行 Windows 的 VMware 虛擬機器有一些需求。</p><p>支援的 64 位元作業系統 ︰ **Windows Server 2012 R2**, ，**Windows Server 2012**, ，或 **Windows Server 2008 R2 在至少為 SP1**。</p><p>主機名稱、 掛接點、 裝置名稱、 Windows 系統路徑 (例如 ︰ C:\Windows) 僅可使用英文。</p><p>作業系統應該安裝在 C:\ 磁碟機上。</p><p>僅支援基本磁碟。 不支援動態磁碟。</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>您必須提供系統管理員帳戶 （必須是本機系統管理員在 Windows 電腦上的） 以推入安裝 Windows 伺服器上的行動服務。 如果提供的帳戶是非網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要執行此動作，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要從 CLI 開啟 cmd 或 powershell 加入登錄項目，然後輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 [了解詳細](https://msdn.microsoft.com/library/aa826699.aspx) 關於存取控制。</p><p>容錯移轉之後，如果您想要使用遠端桌面連線到 Azure 中的 Windows 虛擬機器，請確認內部部署機器的遠端桌面已啟用。 如果您不透過 VPN 連線、 防火牆規則應該允許遠端桌面連線透過網際網路。</p>
**Linux 機器** | <p> 支援 64 位元作業系統 ︰ **Centos 6.4、 6.5、 6.6**; **Oracle Enterprise Linux 6.4、 6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)**, ，**SUSE Linux Enterprise Server 11 SP3**。</p><p>受保護機器上的防火牆規則應該允許它們連線到 Azure 中的設定和主要目標伺服器。</p><p>受保護的機器上的 /etc/hosts 檔案應該包含本機主機名稱對應到所有 Nic 相關都聯的 IP 位址的項目 </p><p>如果您想要連接到 Azure 虛擬機器執行 Linux，使用安全殼層用戶端 (ssh) 的容錯移轉之後，請確定受保護的電腦上的安全殼層服務設定為在系統開機時自動啟動，且防火牆規則允許 ssh 連線到它。</p><p>主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (eg /etc/; /usr) 僅可使用英文。</p><p>可以使用下列儲存體的內部部署機器啟用保護:-<br>檔案系統 ︰ EXT3、 ETX4、 ReiserFS、 XFS<br>多重路徑軟體裝置對應工具 （多重路徑）<br>磁碟區管理員 ︰ LVM2<br>不支援使用 HP CCISS 控制站儲存體的實體伺服器。</p>
**第三方** | 在這個案例中某些部署元件取決於第三方廠商軟體才能正常運作。 如需完整清單，請參閱 [協力廠商軟體注意事項和資訊](#third-party)

## Deployment

圖形摘要列出部署步驟。

![部署步驟](./media/site-recovery-vmware-to-azure/VMWare2AzureSteps.png)

## 網路連線類型

有兩個選項可設定您的內部部署網站與部署您的基礎結構元件 (設定伺服器、主要目標伺服器) 所在的 Azure 虛擬網路之間的網路連線 。 您必須決定要使用的網路連線選項，之後才能部署設定伺服器。 這是部署時的選項，之後無法變更。

**公用網際網路 ︰** 通訊與複寫的內部部署伺服器 （處理序伺服器、 受保護的伺服器） 與 Azure 基礎結構元件伺服器 （設定伺服器，主要目標伺服器） 之間的資料會透過安全 SSL/TLS 連線從內部公用端點來設定伺服器和主要目標伺服器上。 (唯一的例外狀況是 TCP 連接埠 9080 上處理序伺服器與主要目標伺服器之間的連線未加密。 在此連接上只有與用來設定複寫相關的複寫通訊協定相關的控制資訊會加以交換。)

![部署圖表網際網路](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentInternet.png)

**VPN:** 通訊與複寫的內部部署伺服器 （處理序伺服器、 受保護的伺服器） 與 Azure 基礎結構元件伺服器 （設定伺服器，主要目標伺服器） 之間的資料會透過內部網路和 Azure 虛擬網路的部署設定伺服器與主要目標伺服器之間的 VPN 連線。 確定您的內部部署網路透過 ExpressRoute 連線或站對站 VPN 連線連接到 Azure 虛擬網路。

![部署圖表 VPN](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentVPN.png)


## 步驟 1：建立保存庫

1. 登入 [管理入口網站](https://portal.azure.com)。


2. 展開 **Data Services** > **復原服務** 按一下 **Site Recovery 保存庫**。


3. 按一下 [ **新建** > **快速建立**。

4. 在 **名稱**, ，輸入易記名稱來識別保存庫。

5. 在 **區域**, ，選取保存庫的地理區域。 若要檢查支援的地區，請參閱在上市 [Azure Site Recovery 定價詳細資料](pricing/details/site-recovery/)

6. 按一下 [ **建立保存庫**。

    ![新增保存庫](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫會被列為 **Active** 在主要 **復原服務** 頁面。

## 步驟 2：部署設定伺服器

### 設定伺服器設定

1. 在 [復原服務]**** 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。 您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. 在下拉式清單中，選取 **VMware/實體伺服器與 Azure 的內部部署站台間**。
3. 在 **準備目標 （azure） 資源** 按一下 **部署設定伺服器**。

    ![部署設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS2.png)

4. 在 **新組態伺服器詳細資料** 指定 ︰

    - 設定伺服器的名稱和連接到它的認證。
    - 在 [網路連線類型] 下拉式清單中，選取 [公用網際網路] 或 [VPN]。
    [AZURE.NOTE] 這是設定部署時的選項讓，以及之後無法變更。  
    - 選取伺服器所在地方的 Azure 網路。 如果您指定 VPN 做為網路連線類型，請確定此 Azure 虛擬網路是透過 ExpressRoute 連線或站對站 VPN 連線到內部部署網站。
    - 指定要指派給伺服器的內部 IP 位址與子網路。 請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。 使用任何其他可用的 IP 位址。
    
    ![部署設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMware_CSDetails2.png)

5. 當您按一下 **確定** Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器將建立在設定伺服器的訂閱。 它會建立為新的雲端服務中的第一個執行個體。 如果您將網路連線類型指定為 [公用網際網路]，則會使用保留的公用 IP 位址建立雲端服務。 您可以監視進度 **工作** ] 索引標籤。

    ![監視進度](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

6.  **僅在您的連線類型是 [公用網際網路] 時，此步驟才適用。** 設定伺服器後是已部署的注意公用 IP 位址指派給在 **虛擬機器** Azure 入口網站中的頁面。 接著，在 **端點** 索引標籤上注意公用 HTTPS 連接埠對應至私人連接埠 443。 稍後當您在主要目標與處理序伺服器上註冊此設定伺服器的時候，您會需要這個資訊。 這個設定伺服器利用這些端點進行部署：

    - HTTPS：公用連接埠是用於透過網際網路協調元件伺服器與 Azure 之間的通訊。 私用連接埠 443 是用來透過 VPN 協調元件伺服器與 Azure 之間的通訊。
    - 自訂：公用連接埠用於透過網際網路的容錯回復工具通訊
    - 。 私人連接埠 9443 是用於透過 VPN 的容錯回復工具通訊。
    - PowerShell：私人連接埠 5986
    - 遠端桌面：私人連接埠 3389
    
    ![VM 端點](./media/site-recovery-vmware-to-azure/ASRVMWare_VMEndpoints.png)

    >[AZURE.WARNING] 切勿刪除或變更任何設定伺服器部署期間建立之端點的公用或私用通訊埠編號。

設定伺服器使用保留的 IP 位址部署在自動建立的 Azure 雲端服務。 需要保留的位址才能確保雲端服務上的虛擬機器 (包括設定伺服器) 重新開機後，設定伺服器雲端服務  IP 位址仍是相同的。 當解除委任設定伺服器時，將必須手動將保留的公用 IP 位址取消保留，否貝它仍將維持保留。 每一訂用帳戶保留的公用 IP 位址預設限制為 20。 [深入了解](../virtual-network/virtual-networks-reserved-private-ip.md) 有關保留的 IP 位址。 

### 在保存庫中註冊設定伺服器

1. 在 **快速入門** 頁面上，按一下 **準備目標資源** > **下載註冊金鑰**。 金鑰檔案會隨即自動產生。 該金鑰在產生後會維持 5 天有效。 將它複製到設定伺服器。
2. 在 **虛擬機器** 從虛擬機器清單中選取設定伺服器。 開啟 **儀表板** ] 索引標籤上，按一下 [ **連接**。 **開啟** 下載的 RDP 檔案登入設定伺服器使用遠端桌面。 如果設定伺服器是部署在 VPN 網路上，則使用設定伺服器的內部 IP 位址 (這是當您部署設定伺服器時指定的 IP 位址，並且也可在設定伺服器虛擬機器的虛擬機器儀表板頁面上看到)，以從您的內部部署網路進行遠端桌面到它。 當您第一次登入時，Azure Site Recovery 設定伺服器安裝精靈會自動執行。

    ![註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationSplashscreen.png)

3. 在 **協力廠商軟體安裝** 按一下 **我接受** 來下載並安裝 MySQL。

    ![MySQL 安裝](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLEULA.png)

4. 在 **MySQL 伺服器詳細資料** 建立認證來登入 MySQL server 執行個體。

    ![MySQL 認證](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLPWD.png)

5. 在 **網際網路設定** 指定設定伺服器連線到網際網路的方式。 請注意：

    - 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。
    - 當您按一下 **下一步** 會執行測試以檢查 proxy 連線。
    - 如果您使用自訂 Proxy，或者您的預設 Proxy 需要驗證，您必須輸入 Proxy 詳細資料，包含位址、連接埠和認證。
    - 下列 URL 應可透過 Proxy 存取：
        - *.hypervrecoverymanager.windowsazure.com
        - *.accesscontrol.windows.net
        - *.backup.windowsazure.com
        - *.blob.core.windows.net
        - *.store.core.windows.net
    - 如果您有 IP 位址為基礎的防火牆規則確保規則均設定為允許來自設定伺服器通訊中所述的 IP 位址 [Azure Datacenter IP 範圍](https://msdn.microsoft.com/library/azure/dn175718.aspx) 和 HTTPS (443) 通訊協定。 您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

    ![Proxy 註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationProxy.png)

6. 在 **提供者錯誤訊息當地語系化設定** 指定要在哪一種語言中出現的錯誤訊息。

    ![錯誤訊息註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationLocale.png)

7. 在 **Azure Site Recovery 註冊** 瀏覽並選取金鑰檔複製到伺服器。

    ![金鑰檔註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVault.png)

8. 在精靈的完成頁面上選取這些選項：

    - 選取 **啟動帳戶管理對話方塊** 來指定完成精靈後，應該開啟 [管理帳戶] 對話方塊。
    - 選取 **為 Cspsconfigtool 建立桌面圖示** 到組態伺服器上加入桌面捷徑，以便您可以開啟 **管理帳戶** ] 對話方塊，在任何時間，而不必重新執行精靈。

    ![完成註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationFinal.png)

9. 按一下 [ **完成** 來完成精靈。 隨即產生複雜密碼。 將其複製到安全的位置。 您在設定伺服器上驗證與註冊處理序與主要目標伺服器時將會需要這個密碼。 這個密碼也可以用來確認設定伺服器通訊中的通道完整性。 您可以重新產生複雜密碼，但是屆時您也必須以新的複雜密碼再次註冊主要目標與處理序伺服器。

    ![複雜密碼](./media/site-recovery-vmware-to-azure/passphrase.png)

註冊後，設定伺服器會列在 **組態伺服器** 保存庫中的頁面。

### 設定和管理帳戶

在部署期間，Site Recovery 會對下列動作要求認證：

- 當您新增 vCenter 伺服器，以由 vCenter 伺服器管理用於虛擬機器的自動化探索時。 自動化探索虛擬機器需要 vCenter 帳戶。
- 當您加入機器以進行保護時，如此 Site Recovery 即可以在機器上安裝行動服務。

註冊組態伺服器之後，您可以開啟 **管理帳戶** ] 對話方塊來新增及管理應該用於這些動作的帳戶。 有好幾種方法可執行這項操作：

- 針對設定伺服器 (cspsconfigtool) 安裝程式最後一頁上的對話方塊，開啟您選擇建立的捷徑。
- 開啟完成設定伺服器安裝程式上的對話方塊。

1. 在 **管理帳戶** 按一下 **新增帳戶**。 您也可以修改並刪除現有的帳戶。

    ![管理帳戶](./media/site-recovery-vmware-to-azure/ASRVMWare_ManageAccount.png)

2. 在 **帳戶詳細資料** 指定要使用的帳戶名稱在 Azure 或認證 （網域/使用者名稱）。 

    ![管理帳戶](./media/site-recovery-vmware-to-azure/ASRVMWare_AccountDetails.png)

### 連線到設定伺服器 

有兩種方式可以連線到設定伺服器：

- 透過 VPN 站對站或 ExpressRoute 連線
- 透過網際網路 

請注意：

- 網際網路連線使用虛擬機器的端點搭配伺服器的公用虛擬 IP 位址。
- VPN 連線會使用伺服器的內部 IP 位址與端點私人連接埠。
- 它是一次性的決策，決定是否要從您的內部部署伺服器透過 VPN 連線或網際網路連接 (控制和複寫資料) 到在 Azure 中執行的各種元件伺服器 (設定伺服器、主要目標伺服器)。 您之後無法變更此設定。 如果您這樣做，將需要重新部署案例並重新保護您的機器。  


## 步驟 3：部署主要目標伺服器

1. 在 **準備目標 （azure） 資源**, ，按一下 [ **部署主要目標伺服器**。
2. 指定主要目標伺服器的詳細資料和認證。 伺服器將部署在與您註冊之設定伺服器相同的 Azure 網路。 當您按一下以完成動作時，隨即會建立附帶 Windows 或 Linux 資源庫映像的 Azure 虛擬機器。

    ![目標伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。 指定任何其他可用的 IP 位址。

>[AZURE.NOTE] 設定保護的工作負載需要一致的 IO 高效能和低度延遲，若要裝載 IO 密集工作負載使用時，選取標準 DS4 [高階儲存體帳戶](../storage/storage-premium-storage-preview-portal.md)。


3. 會隨著這些端點建立 Windows 主要目標伺服器虛擬機器 (只有當您的部署類型為 [公用網際網路] 時，才會建立公開端點)：

    - 自訂：處理序伺服器可使用公用連接埠透過網際網路傳送複寫資料。 處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
    - 自訂 1：處理序伺服器可使用公用連接埠透過網際網路傳送控制中繼資料。 處理序伺服器可使用私人連接埠 9080 透過 VPN 傳送控制中繼資料給主要目標伺服器。
    - PowerShell：私人連接埠 5986
    - 遠端桌面：私人連接埠 3389

4. 會隨著這些端點建立 Linux 主要目標伺服器虛擬機器 (只有當您的部署類型為 [公用網際網路] 時，才會建立公開端點)：

    - 自訂：處理序伺服器可使用公用連接埠透過網際網路傳送複寫資料。 處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
    - 自訂 1：處理序伺服器可使用公用連接埠透過網際網路傳送控制中繼資料。 處理序伺服器可使用私人連接埠 9080 透過 VPN 傳送控制資料給主要目標伺服器
    - SSH：私人連接埠 22

    >[AZURE.WARNING] 切勿刪除或變更任何主要目標伺服器部署期間建立之端點的公用或私用通訊埠編號。

5. 在 **虛擬機器** 等待虛擬機器啟動。

    - 如果您已利用 Windows 設定伺服器，請記下遠端桌面詳細資料。
    - 如果您已利用 Linux 設定，並透過 VPN 連線，請注意虛擬機器的內部 IP 位址。 如果您透過網際網路連線，請注意公用 IP 位址。

6.  登入伺服器以完成安裝，並在設定伺服器上註冊該伺服器。 
7.  如果您在執行 Windows：

    1. 啟動與虛擬機器的遠端桌面連線。 第一次登入指令碼會在 PowerShell 視窗中執行。 不要關閉它。 完成時，主機代理程式設定工具會自動開啟註冊伺服器。
    2. 在 **主機代理程式設定** 指定設定伺服器與連接埠 443 的內部 IP 位址。 即使您並非透過 VPN 連線，您還是可以使用內部位址與私人連接埠 443，因為虛擬機器附加於和設定伺服器相同的 Azure 網路。 保留 **使用 HTTPS** 啟用。 輸入您先前所記下的設定伺服器複雜密碼。 按一下 [ **確定** 註冊的伺服器。 請注意，您可以忽略頁面上的 NAT 選項。 它們不會被使用。
    3. 如果您估計的保留磁碟機需要超過 1 TB，您就可以設定使用虛擬磁碟的保留磁碟區 （:） 和 [儲存空間](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Windows 主要目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

8. 如果您在執行 Linux：
    1. 確定您已安裝最新的 Linux Integration Services (LIS)，之後才安裝主要目標伺服器軟體。 您可以找到最新版本的 LIS 以及指示如何安裝 [這裡](https://www.microsoft.com/en-us/download/details.aspx?id=46842)。 LIS 安裝之後重新啟動電腦。
    2. 在 **準備目標 （azure） 資源** 按一下 **下載並安裝其他軟體 （僅適用於 Linux 主要目標伺服器）** 以下載 Linux 主要目標伺服器封裝。 將下載的 tar 檔案複製到使用 sftp 用戶端的虛擬機器。 或者，您便可以登入已部署的 llinux 主要目標伺服器上，並使用 *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* 下載檔案。
    2. 使用安全殼層用戶端登入伺服器。 請注意，如果您已透過 VPN 連線到 Azure 網路，請使用內部 IP 位址。 否則請使用外部 IP 位址與 SSH 公用端點。
    3. 執行從 gzip 安裝程式解壓縮檔案 ︰ **tar – xvzf-microsoft-asr_ua_8.4.0.0_rhel6-64***
    ![Linux 主要目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)
    4. 請確認您在解壓縮 tar 檔案內容的目錄中。
    5. 設定伺服器複雜密碼複製到本機檔案，使用命令 **echo *`<passphrase>`* > passphrase.txt**
    6. 執行命令"**sudo。 /install-t 這兩種-a host-R MasterTarget-d /usr/local/ASR-i *`<Configuration server internal IP address>`* -p 443-s y-c https-P passphrase.txt**」。

    ![註冊目標伺服器](./media/site-recovery-vmware-to-azure/Linux-MT-install.png)

9. 等候幾分鐘 (10-15)，然後在 **伺服器** > **組態伺服器** 上註冊頁面上檢查主要目標伺服器已列為 **伺服器詳細資料** ] 索引標籤。 如果您在執行 Linux 而且並未註冊主應用程式設定工具再次從 /usr/local/ASR/Vx/bin/hostconfigcli 執行。 您必須藉由執行 chmod 做為根使用者，以設定存取權限。

    ![確認目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

>[AZURE.NOTE] 請注意，可能需要 15 分鐘之後註冊已完成設定伺服器下要列出的主要目標伺服器。 若要立即更新，請按一下設定伺服器頁面底部的重新整理按鈕，以重新整理設定伺服器。

## 步驟 4：部署內部部署處理序伺服器

>[AZURE.NOTE] 我們建議您設定處理序伺服器上的靜態 IP 位址，以便能夠持續消失保障。

1. 按一下 [快速入門 > **安裝處理序伺服器內部** > **下載並安裝處理序伺服器**。

    ![安裝處理序伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2.  將下載的 zip 檔案複製到您要安裝處理序伺服器的伺服器。 zip 檔案包含兩個安裝檔案：

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. 解壓縮封存，並將安裝檔案複製到伺服器上的位置。
4. 執行 **Microsoft-microsoft-asr_cx_tp_8.4.0.0_windows*** 安裝檔案，然後遵循指示進行。 這樣可以安裝部署所需第三方廠商元件。
5. 然後執行 **Microsoft-microsoft-asr_cx_8.4.0.0_windows***。
6. 在 **伺服器模式** 頁面上，選取 **處理序伺服器**。

    ![伺服器選取模式](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSelection.png)

7. 在 **環境詳細資料** 頁面執行下列動作 ︰


    - If you want to protect VMware virtual machines click **Yes**
    - If  you only want to protect physical servers and thus don't need VMware vCLI installed on the process server. Click **No** and continue.
        
    ![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerVirtualPhysical.png)

8. 安裝 VMware vCLI 時，請注意下列事項：

    - **僅支援 VMware vSphere CLI 5.5.0 是**。 處理序伺服器無法與 vSphere CLI 的其他版本或更新搭配使用。
    - 下載 vSphere CLI 5.5.0 從 [這裡。](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - 如果您正好在您開始安裝處理序伺服器之前安裝 vSphere CLI，並且安裝程式未偵測到它，請等候最多五分鐘的時間，再重新嘗試安裝。 這可確保 vSphere CLI 偵測需要的所有環境變數已正確初始化。

9.  在 **NIC 選取範圍的處理序伺服器** 選取處理序伺服器應該使用的網路介面卡。

    ![選取配接器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerNICSelection.png)

10. 在 **組態伺服器詳細資料**:

    - 針對 IP 位址和連接埠，如果您透過 VPN 連線，請指定設定伺服器的內部 IP 位址以及連接埠 443。 否則，請指定公用虛擬 IP 位址和對應的公用 HTTP 端點。
    - 輸入設定伺服器的複雜密碼。
    - 清除 **驗證行動服務軟體簽章** 如果您想要使用自動推入安裝服務時停用驗證。 簽章驗證需要處理序伺服器的網際網路連線能力。
    - 按一下 [ **下一步**。

    ![註冊設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerConfigServer.png)


11. 在 **選取安裝磁碟機** 選取快取磁碟機。 處理序伺服器需要至少有 600 GB 可用空間的快取磁碟機。 然後按一下 [安裝]****。 

    ![註冊設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerCacheConfig.png)

12. 請注意，您可能需要重新啟動伺服器才能完成安裝。 在 **組態伺服器** > **伺服器詳細資料** 確認處理序伺服器會出現，並在保存庫中成功註冊。

>[AZURE.NOTE]可能需要 15 分鐘之後註冊已完成設定伺服器下才會列出處理序伺服器。 若要立即更新，請按一下設定伺服器頁面底部的重新整理按鈕，以重新整理設定伺服器
 
![驗證處理序伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

如果您在註冊處理序伺服器的時候未停用簽章驗證，您可以稍後再依下列說明停用簽章：

1. 以系統管理員身分登入處理序伺服器，並開啟 C:\pushinstallsvc\pushinstaller.conf 檔案進行編輯。 下一節 **[PushInstaller.transport]** 加入這一行 ︰ **SignatureVerificationChecks ="0"**。 儲存並關閉檔案。
2. 重新啟動 InMage PushInstall 服務。


## 步驟 5：安裝最新的更新

在繼續之前，請確定您已安裝最新的更新。 請記得依下列順序安裝更新：

1. 設定伺服器
2. 處理序伺服器
3. 主要目標伺服器
4. 容錯回復工具 (vContinuum)

您可以在 Site Recovery **儀表板** 上取得更新。 針對 Linux 安裝，請從 Gzip 安裝程式解壓縮檔案，並執行命令 “sudo ./install” 來安裝更新。

下載最新的更新 **容錯回復 tool(vContinuum)** 從 [這裡](http://go.microsoft.com/fwlink/?LinkID=533813)

如果是執行已安裝行動服務的虛擬機器或實體伺服器，您可以取得服務的更新，如下所示：

- 下載服務的更新，如下所示：
    - [Windows Server (僅限 64 位元)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
    - [CentOS 6.4、6.5、6.6 (僅限 64 位元)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
    - [Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
    - [SUSE Linux Enterprise Server SP3 (僅限 64 位元)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- 或者在更新處理序伺服器之後您可以從處理序伺服器上的 C:\pushinstallsvc\repository 資料夾取得行動服務的更新的版本。
- 如果您已有安裝舊版的行動服務的受保護的電腦，則也可以從管理入口網站自動升級受保護電腦上的行動服務。 若要這樣做，請選取電腦所屬的保護群組，反白顯示受保護的電腦並按一下底部的 [更新行動服務] 按鈕。 只有有較新版本的行動服務時，[更新行動服務] 按鈕才會啟用。 請確定處理序伺服器正在執行最新版本的處理序伺服器軟體，之後才更新行動服務。 受保護的伺服器必須符合所有 [自動推入安裝必要條件](#install-the-mobility-service-automatically) 順序更新行動服務才能運作。

![選取 vCenter 伺服器](./media/site-recovery-vmware-to-azure/ASRVmware_UpdateMobility1.png)

在 [選取帳戶] 中，指定用來更新受保護的伺服器上的行動服務的系統管理員帳戶。 按一下 [確定] 並等待觸發的工作完成。


## 步驟 6：加入 vCenter 伺服器或 ESXi 主機

1. 在 **伺服器** > **組態伺服器** 選取組態伺服器] 索引標籤，然後按一下 [ **加入 VCENTER 伺服器** 加入 vCenter 伺服器或 ESXi 主機。

    ![選取 vCenter 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. 指定 vCenter 伺服器或 ESXi 主機的詳細資料並選取將用來探索詳細資料的處理序伺服器。

    - 如果 vCenter 伺服器不在預設連接埠 443 上執行，請指定 vCenter 伺服器執行所在的連接埠號碼。
    - 處理序伺服器必須與 vCenter 伺服器/ESXi 主機在相同的網路上，並且已安裝 VMware vSphere CLI 5.5.0。

    ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter4.png)


3. 探索完成之後，vCenter 伺服器將會列在設定伺服器詳細資料之下。

    ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)

4. 如果您使用非系統管理員帳戶，若要加入 vCenter 伺服器或 ESXi 主機，請確定帳戶具有下列權限：

    - vCenter 帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、儲存體檢視、虛擬機器和 vSphere 分散式切換權限。
    - ESXi 主機帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、虛擬機器和 vSphere 分散式切換權限



## 步驟 7：建立保護群組

1. 開啟 **受保護項目** > **保護群組** 並且按一下以加入保護群組。

    ![建立保護群組](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. 在 **指定保護群組設定** 頁面指定群組的名稱，然後選取您要建立群組的設定伺服器。

    ![保護群組設定](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. 在 **指定複寫設定** 頁面上，設定將用於群組中的所有機器的複寫設定。

    ![保護群組複寫](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. 設定：
    - **多 VM 一致性**︰ 如果您開啟此功能保護群組中跨機器建立共用的應用程式一致復原點。 當保護群組中的所有機器都執行相同的工作負載時，這項設定就可以發揮最高的重要性。 所有機器都將復原到相同的資料點。 僅適用於 Windows 伺服器。
    - **RPO 臨界值**︰ 當連續資料保護複寫 RPO 超過設定的 RPO 臨界值時，就會產生警示。
    - **復原點保留**︰ 指定保留時段。 受保護的機器可以復原到這個週期內的任意點。
    - **應用程式一致快照的頻率**︰ 指定包含應用程式一致快照的復原點建立的頻率。

您可以監視保護群組，因為它們建立在 **受保護項目** 頁面。

## 步驟 8：設定您想要保護的電腦

您必須在您想要保護的虛擬機器和實體伺服器上安裝行動服務。 您可以使用兩種方式執行此動作：

- 從處理序伺服器自動推入並在每一部機器上安裝服務。
- 手動安裝服務。 

### 自動安裝行動服務

當您將機器加入保護群組時，行動服務會自動由處理序伺服器推入並安裝於每部機器上。 

**自動推入會在 Windows 伺服器上安裝行動服務：** 

1. 安裝最新的處理序伺服器的更新中所述 [步驟 5 ︰ 安裝最新的更新](#step-5-install-latest-updates), ，並確定處理序伺服器可用。 
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 設定 Windows 防火牆允許 **檔案及印表機共用** 和 **Windows Management Instrumentation**。 在 Windows 防火牆設定中，選取 [允許應用程式或功能通過防火牆] 選項，並選取應用程式，如下圖所示。 針對隸屬於網域中的機器，您可以利用群組原則物件設定防火牆原則。

    ![防火牆設定](./media/site-recovery-vmware-to-azure/ASRVMWare_PushInstallFirewall.png)

4. 用來執行推入安裝的帳戶必須在您要保護之機器的系統管理員群組中。 這些認證僅適用於推入行動服務的安裝，並且您會在將電腦加入保護群組時提供它們。
5. 如果提供的帳戶不是網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要執行此動作，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要從 CLI 開啟 cmd 或 powershell 加入登錄項目，然後輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 

**自動推入會在 Linux 伺服器上安裝行動服務：**

1. 安裝最新的處理序伺服器的更新中所述 [步驟 5 ︰ 安裝最新的更新](#step-5-install-latest-updates), ，並確定處理序伺服器可用。
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 確認帳戶是來源 Linux 伺服器上的根使用者。
4. 確保來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有 NIC 相關聯之 IP 位址的項目。
5. 在您想要保護的電腦上安裝最新的 openssh、openssh 伺服器、openssl 套件。
6. 請確定 SSH 已啟用且正在連接埠 22 上執行。 
7. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示： 

    - a) 以 root 的身分登入。
    - b） 在 /etc/ssh/sshd_config 檔案中，尋找開頭的那一行 **PasswordAuthentication**。
    - c) 取消註解該行，並將值從 “no” 變更為 “yes”。

        ![Linux 行動性](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

    - d) 尋找以 Subsystem 開頭的行並取消註解該行。
    
        ![Linux 推播行動性](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility2.png)  

8. 確定支援來源機器 Linux 變異。 
 
### 手動安裝行動服務

用來安裝行動服務使用的軟體套件位於處理序伺服器上的 C:\pushinstallsvc\repository 中。 登入處理序伺服器，並將適當的安裝套件複製到來源電腦，根據下表：

| 來源作業系統                               | 處理序伺服器上的行動服務封裝                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (僅限 64 位元)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4、6.5、6.6 (僅限 64 位元)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**若要在 Windows 伺服器上手動安裝行動服務**, ，執行下列動作 ︰

1. 複製 **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** 到來源機器上表中列出的處理序伺服器目錄路徑的包裝。
2. 在來源電腦上執行可執行檔來安裝行動服務。
3. 遵循安裝程式的指示。
4. 選取 **行動服務** 作為角色，然後按一下 **下一步**。
    
    ![安裝行動服務](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall1.png)

5. 保留為預設安裝路徑的安裝目錄，再按 **安裝**。
6. 在 **主機代理程式設定** 指定 IP 位址和 HTTPS 連接埠的組態伺服器。

    - 如果您是透過網際網路連線，請指定公用虛擬 IP 位址和公用 HTTPS 端點做為連接埠。
    - 如果您是透過 VPN 連接，請指定內部 IP 位址和連接埠 443。 保留 **使用 HTTPS** 檢查。

    ![安裝行動服務](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall2.png)

7. 指定設定伺服器複雜密碼，然後按一下 **確定** 向組態伺服器註冊的行動服務。

**若要從命令列執行：**

1. 將複雜密碼從 CX 複製到伺服器上的 "C:\connection.passphrase" 檔案，並執行此命令。 在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Linux 伺服器上手動安裝行動服務**:

1. 根據上表，將適當的 tar 封存檔從將處理序伺服器複製到來源機器。
2. 開啟殼層程式，並藉由執行 `tar -xvzf Microsoft-ASR_UA_8.2.0.0*` 將壓縮的 tar 封存檔解壓縮到本機路徑
3. 您要解壓縮 tar 封存檔內容輸入本機目錄中建立 passphrase.txt 檔案 *`echo <passphrase> >passphrase.txt`* 從 shell。
4. 輸入安裝行動服務 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*。
5. 指定 IP 位址和連接埠：

    - 如果您是透過網際網路連接到設定伺服器，請在 `<IP address>` 和 `<port>` 中指定組態伺服器的虛擬公用 IP 位址和公用 HTTPS 端點。
    - 如果您要透過 VPN 連線連接，請指定內部 IP 位址和 443。

**若要從命令列執行**:

1. 將複雜密碼從 CX 複製到伺服器上的 "passphrase.txt"檔案，並執行此命令。 在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
若要在目標伺服器上安裝：


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] 當您加入機器到保護群組已在執行適當版本的行動服務然後推入安裝會略過。


## 步驟 9：啟用保護

若要啟用保護，您會將虛擬機器和實體伺服器加入至保護群組。 開始之前，請注意：

- 系統會每隔 15 分鐘探索虛擬機器一次，而且在探索之後，需要最多 15 分鐘的時間，虛擬機器才會會出現在 Azure Site Recovery 中。
- 虛擬機器上的環境變更 (例如 VMware 工具安裝) 也可能需要最多 15 分鐘的時間，才能在 Site Recovery 中更新。
- 您可以檢查上次探索的時間 **上次連絡時間** 欄位上的 vCenter 伺服器 /esxi 主機 **組態伺服器** 頁面。
- 如果您已建立保護群組，並新增 vCenter Server 或 ESXi 主機之後，需要 15 分鐘重新整理 Azure Site Recovery 入口網站和虛擬機器列在 **將機器加入保護群組** ] 對話方塊。
- 如果您想要立即繼續將機器加入保護群組，而不要等候已排程的探索，反白顯示設定伺服器 （不要按它），按一下 **重新整理** ] 按鈕。
- 當您將虛擬機器或實體機器加入保護群組時，處理序伺服器會自動推入行動服務並在來源伺服器上安裝 (如果尚未安裝)。
- 若要讓自動推入機制運作，請確認已將受保護機器如上一個步驟所述進行設定。

如下所示加入機器：

1. **受保護項目** > **保護群組** > **機器** ] 索引標籤。 按一下 [ **將機器加入**。 做為最佳做法，我們建議保護群組應反映您的工作負載，這樣一來您才可以將執行特定應用程式的機器加入相同的群組。
2. 在 **選取虛擬機器** 如果您要保護實體伺服器，在 **加入實體機器** 精靈提供的 IP 位址和易記名稱。 然後選取作業系統系列。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PhysicalProtect.png)

3. 在 **選取虛擬機器** 如果您要保護 VMware 虛擬機器，選取負責管理您的虛擬機器 （或正在執行的 EXSi 主機） 的 vCenter 伺服器，然後選取機器。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)   

4. 在 **指定目標資源** 選取主要目標伺服器和儲存體用於複寫，並選擇是否要設定適用於所有工作負載。 選取 [高階儲存體帳戶](../storage/storage-premium-storage-preview-portal.md) 時設定，若要裝載 IO 密集工作負載需要一致的 IO 高效能和低延遲工作負載的保護。 如果您希望針對您的工作負載磁碟使用進階儲存體帳戶，您必須使用 DS 系列的主要目標。 您無法搭配使用進階儲存體磁碟與非 DS 系列的主要目標。

    ![vCenter 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. 在 **指定帳戶** 選取您想要使用受保護的機器上安裝行動服務的帳戶。 需要帳戶認證，才能自動安裝行動服務。 如果您無法選取帳戶，請確定您如步驟 2 中所述設定一個。 請注意，Azure 無法存取此帳戶。 對於 Windows 伺服器而言，帳戶應該具有來源伺服器上的系統管理員權限。 若為 Linux，帳戶必須是 root。

    ![Linux 認證](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. 按一下核取記號，以完成將機器加入保護群組，並啟動每部機器的初始複寫。 您可以在監視狀態 **工作** 頁面。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs2.png)

7. 此外您可以監視保護狀態，藉以 **受保護項目** > 保護群組名稱 > **虛擬機器** 。 初始複寫完成，且機器在同步處理資料之後仍會顯示 **受保護** 狀態。

    ![虛擬機器作業](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)


### 設定受保護機器屬性

1. 在機器具有之後 **受保護** 狀態，您可以設定其容錯移轉屬性。 在保護群組詳細資料選取機器並開啟 **設定** ] 索引標籤。
2. 在容錯移轉和 Azure 虛擬機器大小調整之後，您可以修改要提供給 Azure 中機器的名稱。 您也可以選取機器要在容錯移轉之後要連結的 Azure 網路。

    ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

請注意：

- Azure 機器的名稱必須符合 Azure 需求。
- 根據預設，Azure 中的複寫虛擬機器不會與 Azure 網路連線。 如果您要讓複寫的虛擬機器進行通訊，請務必為其設定相同的 Azure 網路。
- 如果您調整 VMware 虛擬機器或實體伺服器上磁碟區的大小，它會進入嚴重狀態。 如果您需要修改大小，請執行下列動作：

    - a) 變更大小設定。
    - b） 在 **虛擬機器** 索引標籤，選取虛擬機器，然後按一下 [ **移除**。
    - c） 在 **移除虛擬機器** 選擇 **停用保護 （用於復原切入和調整大小的磁碟區）**。 此選項會停用保護，但會在 Azure 中保留復原點。

        ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure/ASRVMWare_RemoveVM.png)

    - d) 重新啟用虛擬機器的保護。 當您重新啟用保護時，重新調整過大小的磁碟區資料會傳輸至 Azure。

    

## 步驟 10：執行容錯移轉

目前您只能為受保護的 VMware 虛擬機器和實體伺服器執行非計劃的容錯移轉。 請注意：



- 起始容錯移轉之前，請確保設定和主要目標伺服器執行中且狀況良好。 否則容錯移轉將會失敗。
- 來源機器未隨著非計劃的容錯移轉關閉。 執行非計劃的容錯移轉會停止受保護伺服器的資料複寫。 您將必須從保護群組刪除機器並重新加入，然後才能在非計劃的容錯移轉完成之後，再次開始保護機器。
- 如果您想要容錯移轉而不遺失任何資料，請確定主要網站虛擬機器在您起始容錯移轉之前是關閉的。

1. 在 **復原計劃** 頁面上，並加入復原計畫。 指定的計畫，然後選取詳細資料 **Azure** 做為目標。

    ![設定復原計畫](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. 在 **選取虛擬機器** 選取保護群組，然後選取要加入至復原方案群組中的 [機器。 [閱讀更多](site-recovery-create-recovery-plans.md) 復原方案的相關資訊。

    ![新增虛擬機器](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. 若需要，您可以自訂計畫以建立群組並決定機器在復原計劃中容錯移轉的順序。 您也可以加入進行手動動作和指令碼的提示。 要加入指令碼復原到 Azure 時，請使用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md)。

4. 在 **復原計劃** 頁面上，選取計畫並按一下 [ **規劃的容錯移轉**。
5. 在 **確認容錯移轉** 確認容錯移轉方向 (朝向 Azure)，然後選取 [容錯移轉至復原點。
6. 等候容錯移轉工作完成，然後確認容錯移轉如預期般運作，且複寫的虛擬機器在 Azure 中成功啟動。




## 步驟 11：來自 Azure 機器的容錯移轉失敗

[了解詳細](site-recovery-failback-azure-to-vmware.md) 有關如何將失敗的 Azure 中執行的機器還原到您的內部部署環境。


## 管理您的處理序伺服器

處理序伺服器會傳送複寫資料至 Azure 中的主要目標伺服器，並探索加入至 vCenter 伺服器的新 VMware 虛擬機器。 在下列情況下，您可能要變更在您的部署中的處理序伺服器：

- 如果目前的處理序伺服器關閉
- 如果您的復原點目標 (RPO) 上升到您的組織無法接受的程度。

必要時，您可以將內部部署 VMware 虛擬機器和實體伺服器的部分或所有複寫移到另一個處理序伺服器。 例如：

- **失敗**— 如果處理序伺服器失敗或無法使用您可以將受保護的機器複寫移至另一個處理序伺服器。 來源機器和複本機器的中繼資料將會移至新的處理序伺服器，並重新同步處理資料。 新的處理序伺服器將自動連接至 vCenter 伺服器來執行自動探索。 您可以在 Site Recovery 儀表板上監視處理序伺服器的狀態。
- **負載平衡以調整 RPO**— 如改善的負載平衡，您可以在 Site Recovery 入口網站中選取不同的處理序伺服器，並手動進行負載平衡，一個或多個機器的複寫移到它。 在此情況下，選取的來源和複本機器的中繼資料會移至新的處理序伺服器。 原始的處理序伺服器仍然連接至 vCenter 伺服器。 

### 監視處理序伺服器

如果處理序伺服器處於嚴重狀態，Site Recovery 儀表板上將顯示狀態警告。 您可以按一下狀態以開啟 [事件] 索引標籤，然後向下鑽研至 [工作] 索引標籤上的特定工作。 

### 修改用於複寫的處理序伺服器

1. 移至 **組態伺服器** 頁面下 **伺服器**
2. 按一下 [設定伺服器的名稱，並移至 **伺服器詳細資料**。
3. 在 **處理序伺服器** 清單中按一下 **變更處理序伺服器** 您想要修改的伺服器旁。
    ![變更處理序伺服器 1](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS1.png)
4. 在 **變更處理序伺服器** ] 對話方塊中選取新伺服器 **目標處理序伺服器**, ，然後選取您想要複寫到新的伺服器虛擬機器。 按一下以取得其相關資訊，包括可用空間，已用記憶體的伺服器名稱旁的資訊圖示。 將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間就會顯示，以協助您進行負載的決策。
    ![變更處理序伺服器 2](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS2.png)
5. 按一下核取記號以開始複寫到新處理序伺服器。 如果從某個重要的處理序伺服器中移除所有虛擬機器，儀表板中應該不會再顯示重大警告。


## 第三方廠商軟體注意事項和資訊

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

完整的檔案可能位於 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=529428)。 Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


