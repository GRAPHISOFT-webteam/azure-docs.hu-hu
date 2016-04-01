<properties
    pageTitle="進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體 | Microsoft Azure"
    description="「進階儲存體」可針對在「Azure 虛擬機器」上執行且需要大量 I/O 的工作負載，提供高效能、低延遲的磁碟支援。 Azure DS 系列和 GS 系列 VM 支援「進階儲存體」。"
    services="storage"
    documentationCenter=""
    authors="ms-prkhad"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="robinsh;selcint"/>


# Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體

## 概觀

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用「進階儲存體」的虛擬機器 (VM) 會將資料儲存在固態硬碟 (SSD) 上。 您可以將應用程式的 VM 磁碟移轉到「Azure 進階儲存體」，以利用這些磁碟的速度和效能。 

Azure VM 支援連接數個「進階儲存體」磁碟，讓您應用程式的每一 VM 最多可擁有 64 TB 的儲存體。 使用「進階儲存體」時，您應用程式的每一 VM 可達到 80,000 IOPS (每秒輸入/輸出作業)，而每一 VM 的每秒磁碟輸送量為 2000 MB，且讀取作業的延遲極低。

>[AZURE.NOTE] 我們建議您移轉高 IOPS Azure 高階儲存體需要您的應用程式的最佳效能的任何虛擬機器磁碟。 如果您的磁碟不需要高 IOPS，您可以在「標準儲存體」中維護它來限制成本，這會將虛擬機器磁碟資料儲存在「硬碟機 (HDD)」上而非 SSD 上。

若要開始使用 Azure 高階儲存體，請造訪 [免費開始](http://azure.microsoft.com/pricing/free-trial/) 頁面。 如需將現有的虛擬機器移轉到高階儲存體，請參閱 [移轉到 Azure Premium 儲存體](storage-migration-to-premium-storage.md)。

## Premium 儲存體重要須知

下列是使用 Premium 儲存體之前或使用 Premium 儲存體時需考量的重要事項清單：

- 若要使用 Premium 儲存體，您必須有 Premium 儲存體帳戶。 若要了解如何建立高階儲存體帳戶，請參閱 [建立和使用 Premium 儲存體帳戶磁碟](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)。

- 高階儲存體位於 [Azure 入口網站](portal.azure.com) 且可存取透過下列 SDK 程式庫 ︰ [儲存體 REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) 2014年-02-14 版或更新版本; [服務管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 2014年-10-01 版或更新版本，和 [PowerShell](../install-configure-powershell.md) 0.8.10 或更新版本。

- 如需目前支援高階儲存體的區域，請參閱 [依地區的 Azure 服務](http://azure.microsoft.com/regions/#services)。

- Premium 儲存體僅支援 Azure 分頁 Blob，用於保存適用於 Azure 虛擬機器 (VM) 的耐久性磁碟。 如需 Azure 分頁 blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。 Premium 儲存體不支援 Azure 區塊 Blob、Azure 檔案、Azure 表格或 Azure 佇列。

- Premium 儲存體帳戶是本機備援 (LRS) 帳戶，在單一區域內會保留三份資料。  如需使用 Premium 儲存體時關於地理複寫的考量，請參閱 [快照與複製 Blob 時使用高階儲存體](#snapshots-and-copy-blob-when-using-premium-storage) 〉 一節。

- 如果您希望您的 VM 磁碟使用進階儲存體帳戶，您必須使用 DS 系列或 GS 系列的 VM。 DS 系列或 GS 系列的 VM 可同時使用 Standard 和 Premium 儲存體磁碟。 非 DS 系列或非 GS 系列的 VM 則無法使用進階儲存體磁碟。 如需可用 Azure VM 磁碟類型和大小資訊，請參閱 [虛擬機器和雲端服務大小](../virtual-machines/virtual-machines-size-specs.md)。

- 設定 VM 之 Premium 儲存體磁碟的程序和標準儲存體磁碟的類似。 您必須為您的 Azure 磁碟和 VM 選擇最適合的 Premium 儲存體選項。 依 Premium 解決方案的效能特性而定，VM 大小應適合您的工作負載。 如需詳細資訊，請參閱 [延展性和效能目標時使用高階儲存體](#scalability-and-performance-targets-when-using-premium-storage)。

- Premium 儲存體帳戶無法對應到自訂網域名稱。

- Premium 儲存體目前不支援儲存體分析。 若要分析使用 Premium 儲存體帳戶磁碟的 Vm 效能度量，請使用作業系統工具，例如 [Windows 效能監視器](https://technet.microsoft.com/library/cc749249.aspx) Windows vm 和 [IOSTAT](http://linux.die.net/man/1/iostat) 對於 Linux Vm。 您也可以在 Azure Preview 入口網站啟用 Azure VM 診斷。 請參閱 [Microsoft Azure Azure 診斷擴充功能與監視的虛擬機器](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) 如需詳細資訊。

## 使用 Premium 磁碟儲存體
有兩種方式可使用 Premium 磁碟儲存體：

- 首先建立新的 Premium 儲存體帳戶，然後在建立 VM 時使用。
- 建立新的 DS 系列或 GS 系列 VM。 建立 VM 時，您可以選取先前建立的 Premium 儲存體帳戶、建立新的 Premium 儲存體帳戶，或讓 Azure 入口網站建立預設的 Premium 帳戶。

Azure 使用儲存體帳戶做為作業系統 (OS) 和資料磁碟的容器。 換句話說，如果您建立 Azure DS 系列或 GS 系列的 VM 並選取 Azure 進階儲存體帳戶，您的作業系統和資料磁碟會儲存在該儲存體帳戶中。

如需將現有的虛擬機器移轉到高階儲存體，請參閱 [移轉到 Azure Premium 儲存體](storage-migration-to-premium-storage.md)。

若要利用高階儲存體的優點，建立 Premium 儲存體帳戶使用的帳戶類型 *Premium_LRS* 第一次。 若要這樣做，您可以使用 [Azure 入口網站](portal.azure.com), ，[PowerShell](../install-configure-powershell.md), ，或 [服務管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)。 如需逐步指示，請參閱 [建立和使用 Premium 儲存體帳戶磁碟](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)。

### 重要事項：

- 如需容量和頻寬限制的高階儲存體帳戶的詳細資訊，請參閱 [延展性和效能目標時使用高階儲存體](#scalability-and-performance-targets-when-using-premium-storage) 一節。 如果您的應用程式需求超出單一儲存體帳戶的延展性目標，請建置使用多個儲存體帳戶的應用程式，並將資料分散到那些儲存體帳戶中。 例如，如果要將 51 TB 的磁碟連結到多個 VM，請將它們分散到兩個儲存體帳戶，因為單一 Premium 儲存體帳戶的限制是 35 TB。 請務必確認單一 Premium 儲存體帳戶的佈建磁碟不要超過 35 TB。
- 根據預設，所有 Premium 資料磁碟的磁碟快取原則都是「唯讀」，而連接至 VM 的 Premium 作業系統磁碟的磁碟快取原則則是「讀寫」。 為使應用程式的 I/O 達到最佳效能，建議使用此組態設定。 對於頻繁寫入或唯寫的資料磁碟 (例如 SQL Server 記錄檔)，停用磁碟快取可獲得更佳的應用程式效能。
- 確定 VM 上有足夠的磁碟流量頻寬。 例如，STANDARD_DS1 VM 每秒專用頻寬有 32 MB 的 Premium 儲存體磁碟流量。 這表示，連接到此 VM 的 P10 Premium 儲存體磁碟最多只能到每秒 32 MB，而無法到達 P10 磁碟可提供的每秒 100 MB。 同樣地，STANDARD_DS13 VM 在所有磁碟最多可以到達每秒 256 MB。 目前在 DS 系列上最大的 VM 是 STANDARD_DS14，它在所有磁碟最多可以提供每秒 512 MB。 GS 系列上最大的 VM 是 STANDARD_GS5，它在所有磁碟最多可以提供每秒 2000 MB。

    請注意，這些限制僅適用於磁碟流量，不包含快取命中數和網路流量。 VM 網路流量有不同的頻寬，與 Premium 儲存體磁碟專用的頻寬不同。 
    
    最大 IOPS 和輸送量 （頻寬） 在 DS 系列和 GS 系列 Vm 的最新資訊，請參閱 [虛擬機器和雲端服務大小](../virtual-machines/virtual-machines-size-specs.md)。 若要深入了解 Premium 儲存體磁碟及其 IOPs 與輸送量限制，請參閱中的資料表 [延展性和效能目標時使用高階儲存體](#scalability-and-performance-targets-when-using-premium-storage) 〉 一節。

> [AZURE.NOTE] 快取命中數不會受到配置的磁碟 IOPS/輸送量。 也就是說，當您在 DS 系列 VM 或 GS 系列 VM 上使用具 ReadOnly 快取設定的資料磁碟時，來自快取的讀取數不會受到進階儲存體磁碟的限制。 因此，如果工作負載以讀取為主，可以從磁碟獲得極高的輸送量。 請注意，快取會根據 VM 大小，受到 VM 層級個別 IOPS / 輸送量的限制。 DS 系列 VM 大約有 4000 IOPS，快取與本機 SSD IO 是每個核心 33 MB/秒。

- 您可在 DS 系列 VM 或 GS 系列 VM 中同時使用 Premium 和 Standard 儲存體磁碟。
- 使用 Premium 儲存體，您可以佈建 DS 系列 VM 並將幾個永久性的資料磁碟連接到 VM。 如有需要，您可以跨磁碟等量磁碟區以增加磁碟區的容量和效能。 如果您使用的高階儲存體資料磁碟等量磁碟區 [儲存空間](http://technet.microsoft.com/library/hh831739.aspx), ，您應該以每個磁碟所使用的一個資料行設定。 否則，等量磁碟區的整體效能可能會因為磁碟流量分配不平均而比預期的效能還低。 根據預設，伺服器管理員使用者介面 (UI) 可讓您設定最多 8 個磁碟的資料行。 但是，如果您有 8 個以上的磁碟，您就必須使用 PowerShell 來建立磁碟區，並且手動指定資料行數目。 否則，即使您擁有更多磁碟，伺服器管理員 UI 還是會繼續使用 8 個資料行。 例如，如果您在單一等量磁碟區組有 32 個磁碟，您應該指定 32 個資料行。 您可以使用 *NumberOfColumns* 參數 [New-virtualdisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell 指令程式來指定虛擬磁碟所使用的資料行數目。 如需詳細資訊，請參閱 [儲存空間概觀](http://technet.microsoft.com/library/jj822938.aspx) 和 [儲存體空間常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)。
- 請避免將 DS 系列 VM 加入包括非 DS 系列 VM 的現有雲端服務。 可能的解決方法是將現有的 VHD 移轉到僅執行 DS 系列 VM 的新雲端服務。  如果您想要保留相同的虛擬 IP 位址 (VIP)，新的雲端服務裝載 DS 系列 Vm，請使用 [保留的 IP 位址](virtual-networks-configure-vnet-to-vnet-connection.md) 功能。 可將 GS 系列 VM 加入至僅執行 G 系列 VM 的現有雲端服務。
- Azure 虛擬機器的 DS 系列可以設定為使用裝載在標準儲存體帳戶或 Premium 儲存體帳戶上的作業系統 (OS) 磁碟。 如果您使用的作業系統磁碟僅供開機，您可以考慮使用標準儲存體的作業系統磁碟。 在開機之後，它提供類似於 Premium 儲存體的成本效益和效能結果。 如果您在作業系統磁碟上執行除了開機以外的任何其他工作，請使用 Premium 儲存體，因為它提供更好的效能結果。 例如，如果您的應用程式從作業系統磁碟讀取或寫入至作業系統磁碟，使用 Premium 儲存體的作業系統磁碟可為您的 VM 提供更佳的效能。
- 您可以使用 [Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md) 與高階儲存體。 若要使用 Azure CLI 變更其中一個磁碟上的快取原則，請執行下列命令：

    `$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

    請注意，快取原則選項可以是 ReadOnly、None 或 ReadWrite。 如需其他選項，請執行下列命令參閱說明：

    `azure vm disk attach --help`


## 使用 Premium 儲存體時的延展性和效能目標

當您為某個 Premium 儲存體帳戶佈建磁碟時，其每秒的輸入/輸出作業 (IOPS) 和輸送量 (頻寬) 取決於磁碟大小。 目前有三種類型的 Premium 儲存體磁碟：P10、P20 及 P30。 每種各有特定的 IOPS 和輸送量限制，如下表所示：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
    <td><strong>進階儲存體磁碟類型</strong></td>
    <td><strong>P10</strong></td>
    <td><strong>P20</strong></td>
    <td><strong>P30</strong></td>
</tr>
<tr>
    <td><strong>磁碟大小</strong></td>
    <td>128 GB</td>
    <td>512 GB</td>
    <td>1024 GB (1 TB)</td>
</tr>
<tr>
    <td><strong>每一磁碟的 IOPS</strong></td>
    <td>500</td>
    <td>2300</td>
    <td>5000</td>
</tr>
<tr>
    <td><strong>每一磁碟的輸送量</strong></td>
    <td>每秒 100 MB *</td>
    <td>每秒 150 MB *</td>
    <td>每秒 200 MB *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] 請確定有足夠的頻寬可用的磁碟機的磁碟流量中所述的 VM 上 [磁碟使用 Premium 儲存體](#using-premium-storage-for-disks) 稍早在本文中的區段。 否則，您的磁碟輸送量和 IOPS 將會根據 VM 限制而不是上表所述的磁碟限制而受限於較低的值。  

Azure 會將磁碟大小對應 (無條件進位) 至表格中指定之最接近的 Premium 儲存體磁碟選項。 例如，大小為 100 GB 的磁碟會分類為 P10 選項，每秒最多可執行 500 個 IO 單位，每秒輸送量可達 100 MB。 同樣地，大小為 400 GB 的磁碟會分類為 P20 選項，每秒最多可執行 2300 個 IO 單位，每秒輸送量可達 150 MB。

輸入/輸出 (I/O) 單位大小為 256 KB。 如果要傳送的資料少於 256 KB，會視為單一 I/O 單位。 較大的 I/O 大小則會視為大小是 256 KB 的多個 I/O。 例如，1100 KB 的 I/O 會視為五個 I/O 單位。

輸送量限制包含寫入至磁碟，以及從磁碟而不是從快取的讀取。 例如，P10 磁碟的非快取讀取和寫入總和應小於或等於每秒 100 MB。  同樣地，單一 P10 磁碟可有每秒 100 MB 的非快取讀取或每秒 100 MB 的寫入，或是每秒 60 MB 的讀取和每秒 40 MB 的寫入。

當您在 Azure 建立磁碟後，請依據您應用程式的容量、效能、延展性和尖峰負載需求來選取最適合的 Premium 儲存體磁碟解決方案。

> [AZURE.NOTE] 您可以輕鬆增加現有磁碟的大小。 例如，如果您想要將 30 GB 大小的磁碟增加到 128 GB 或 1 TB。 或者，如果您想要將 P20 磁碟轉換為 P30 磁碟，因為您需要更多容量或更多的 IOPS 和輸送量。 您可以使用 "Update-AzureDisk" PowerShell 命令搭配 "-ResizedSizeInGB" 屬性來擴充磁碟。 若要執行這個動作，必須先從 VM 卸離磁碟或停止 VM。  

下表描述 Premium 儲存體帳戶的延展性目標：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
    <td><strong>總帳戶容量</strong></td>
    <td><strong>本機備援儲存體帳戶總頻寬</strong></td>
</tr>
<tr>
    <td>
    <ul>
       <li type=round>磁碟容量：35 TB</li>
       <li type=round>快照容量：10 TB</li>
    </ul>
    </td>
    <td>每秒最多 50 Gbps (輸入 + 輸出)</td>
</tr>
</tbody>
</table>

- 輸入是指傳送至某個儲存體帳戶的所有資料 (要求)。
- 輸出是指從某個儲存體帳戶接收的所有資料 (回應)。

如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)。

## 使用 Premium 儲存體時的節流
如果您的應用程式的 IOPS 或輸送量超過 Premium 儲存體磁碟的配置限制，或如果您在 VM 上所有磁碟的磁碟總流量超過 VM 可用的磁碟頻寬限制，您可能會看到節流。 若要避免節流，建議您根據佈建之磁碟的延展性和效能目標，以及根據 VM 可用的磁碟頻寬，來限制磁碟的擱置 I/O 要求數。  

當您的應用程式設計為避免節流情況時，可以達到最低延遲。 但是，如果磁碟的擱置 I/O 要求數過小，您的應用程式便無法達到磁碟最大的 IOPS 和輸送量層級。

下列範例示範如何計算節流層級：

### 範例 1：
在 P10 磁碟上，您的應用程式一秒內有 495 個 16 KB 磁碟大小的 I/O (等於是 495 個 I/O 單位)。 如果您在該秒內嘗試 2 MB 的 I/O，I/O 單位的總數會等於 495 + 8。 這是因為當 I/O 單位大小是 256 KB 時，2 MB 的 I/O 會產生 2048 KB / 256 KB = 8 個 I/O 單位。 因為 495 + 8 的總和超出磁碟 500 個 IOPS 的限制，因此會發生節流情況。

### 注意：
所有計算都是基於 256 KB 的 I/O 單位大小。

### 範例 2：
在 P10 磁碟上，您的應用程式有 400 個 256 KB 磁碟大小的 I/O。 使用的總頻寬為 (400 * 256) / 1024 = 100 MB/秒。 請注意，每秒 100 MB 是 P10 磁碟的輸送量限制。 如果您的應用程式嘗試在該秒內執行更多 I/O 便會發生節流情況，因為會超出配置的限制。

### 範例 3：
DS4 VM 連接了兩個 P30 磁碟。 每個 P30 磁碟有每秒 200 MB 的輸送量。 不過，DS4 VM 有每秒 256 MB 的磁碟總頻寬容量。 因此，此 DS4 VM 上連接的磁碟無法同時達到最大的輸送量。 若要解決這個問題，您可以在一個磁碟上維持每秒 200 MB 的流量，在另一個磁碟上維持每秒 56 MB 的流量。 如果您的磁碟流量總和超過每秒 256 MB，磁碟流量就會發生節流。

### 注意：
如果磁碟流量大多包含小型 I/O，您的應用程式在達到輸送量限制前很可能會先達到 IOPS 限制。 但是，如果磁碟流量大多包含大型 I/O，您的應用程式可能會達到輸送量限制，而非 IOPS 限制。 您可以使用最佳的 I/O 大小和限制磁碟的擱置 I/O 要求數，以最大化應用程式的 IOPS 和輸送量容量。

## 使用 Premium 儲存體時的快照與複製 Blob
您可以使用 Standard 儲存體時建立快照的方式來為 Premium 儲存體建立快照。 由於 Premium 儲存體是本地備援儲存體，建議您建立快照，並將那些快照複製到異地備援的 Standard 儲存體帳戶。 如需詳細資訊，請參閱 [Azure 儲存體備援選項](http://msdn.microsoft.com/library/azure/dn727290.aspx)。

如果磁碟已連結至 VM，在備份磁碟的分頁 Blob 上不允許某些 API 作業。 例如，您無法執行 [複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) ，只要磁碟連接至 VM 的 blob 作業。 相反地，使用第一次建立該 blob 的快照集 [快照集 Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API 方法，然後執行 [複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 的快照集複製連接的磁碟。 或者，您可以中斷連結磁碟，然後在基礎 Blob 上執行任何必要的作業。

### 重要事項：

- 單一 Blob 的快照數限制為 100。 每 10 分鐘最多可取得一個快照。
- 每個進階儲存體帳戶的快照集最大容量為 10 TB。 請注意，快照集容量只是指快照集中的資料總數，不包含基底 blob 中的資料。
- 若要維護快照集的異地備援副本，您可以使用 AzCopy 或「複製 Blob」，將進階儲存體帳戶中的快照複製到異地備援的標準儲存體帳戶。 如需詳細資訊，請參閱 [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](storage-use-azcopy.md) 和 [複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx)。
- 高階儲存體帳戶中執行對分頁 blob 的 REST 作業的詳細資訊，請參閱 [與 Azure 高階儲存體使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969) MSDN library 中。

## 在 Premium 儲存體使用 Linux VM
請參閱下方重要的指示了解如何在 Premium 儲存體上設定 Linux VM：

- 對於快取設定為 "ReadOnly" 或 "None" 的所有 Premium 儲存體磁碟，您在掛接檔案系統時必須停用 "barrier" (阻礙)，才能達到 Premium 儲存體的延展性目標。 此案例中您不需要阻礙，因為這些快取設定的 Premium 儲存體磁碟寫入都是持久的。 寫入要求成功完成時，資料就已寫入永久性的存放區。 請根據您的檔案系統，使用下列方法停用 "barrier" (阻礙)：
    - 如果您使用 **reiserFS**, 、 停用阻礙使用掛接選項"屏障 = 無 」 (啟用阻礙，請使用 「 屏障 = flush")
    - 如果您使用 **ext3/ext4**, 、 停用阻礙使用掛接選項"屏障 = 0 」 (若要啟用阻礙，請使用 「 屏障 = 1")
    - 如果您使用 **XFS**, 、 使用掛接選項"nobarrier"停用阻礙 （若要啟用阻礙，使用"barrier"選項）

- 對於快取設定為 "ReadWrite" 的 Premium 儲存體磁碟，則應該啟用阻礙以持續寫入。
- 對於要在 VM 重新開機後保存的磁碟機標籤，您必須以參考磁碟的 UUID 更新 /etc/fstab。 也請參閱 [如何將資料磁碟連接至 Linux 虛擬機器](../virtual-machines/virtual-machines-linux-how-to-attach-disk)

以下是我們驗證能使用 Premium 儲存體的 Linux 散發套件。 我們建議您升級 VM 到至少其中一個版本 (或更新版本)，以便獲得 Premium 儲存體較佳的效能和穩定性。 此外，部分版本需要最新的 LIS (適用於 Microsoft Azure 的 Linux Integration Services v4.0)。 請依照下面提供的連結進行下載及安裝。 當我們完成其他驗證後，將繼續在清單中新增更多映像。 請注意，我們的驗證顯示效能依映像而有所不同，而且也取決於工作負載特性和映像上的設定。 不同的映像已針對不同種類的工作負載進行調整。
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
    <td><strong>配送映像</strong></td>
    <td><strong>版本</strong></td>
    <td><strong>支援的核心</strong></td>
    <td><strong>支援的映像</strong></td>
</tr>
<tr>
    <td rowspan="4"><strong>Ubuntu</strong></td>
    <td>12.04</td>
    <td>3.2.0-75.110</td>
    <td>Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB</td>
</tr>
<tr>
    <td>14.04</td>
    <td>3.13.0-44.73</td>
    <td>Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB</td>
</tr>
<tr>
    <td>14.10</td>
    <td>3.16.0-29.39</td>
    <td>Ubuntu-14_10-amd64-server-20150202-en-us-30GB</td>
</tr>
<tr>
    <td>15.04</td>
    <td>3.19.0-15</td>
    <td>Ubuntu-15_04-amd64-server-20150422-en-us-30GB</td>
</tr>
<tr>
    <td><strong>SUSE</strong></td>
    <td>SLES 12</td>
    <td>3.12.36-38.1</td>
    <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
    <td><strong>CoreOS</strong></td>
    <td>584.0.0</td>
    <td>3.18.4</td>
    <td>CoreOS 584.0.0</td>
</tr>
<tr>
    <td rowspan="2"><strong>CentOS</strong></td>
    <td>6.5、6.6、6.7、7.0</td>
    <td></td>
    <td>
        <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> 需要 LIS 4.0 </a> </br>
        *See note below
    </td>
</tr>
<tr>
    <td>7.1</td>
    <td>3.10.0-229.1.2.el7</td>
    <td>
        <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 建議 </a> <br/>
        *See note below
    </td>
</tr>

<tr>
    <td rowspan="2"><strong>Oracle</strong></td>
    <td>6.4</td>
    <td></td>
    <td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> 需要 LIS 4.0 </a></td>
</tr>
<tr>
    <td>7.0</td>
    <td></td>
    <td>請連絡支援了解詳細資訊</td>
</tr>
</tbody>
</table>


### Openlogic CentOS 的 LIS 驅動程式

執行 OpenLogic CentOS VM 的客戶應該執行下列命令以安裝最新的驅動程式：

    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v

您需要重新開機才能啟動驅動程式。



## 使用 Premium 儲存體時的定價和計費資訊
使用 Premium 儲存體時，需考量下列計費資訊：

- Premium 儲存體磁碟的計費依據是磁碟的佈建大小。 Azure 會對應的表格中指定的磁碟大小 （無條件進位） 至最接近的 Premium 儲存體磁碟選項 [延展性和效能目標時使用高階儲存體](#scalability-and-performance-targets-when-using-premium-storage) 一節。 任何已佈建的磁碟都是按每月的 Premium 儲存體優惠價格以每小時的方式計費。 例如，如果您在佈建完 P10 磁碟的 20 小時後刪除它，則會以 20 小時計算 P10 解決方案的費用。 這與寫入磁碟的實際資料量或使用的 IOPS/輸送量無關。
- Premium 儲存體上的快照會因為使用的額外容量而產生費用。 快照集的資訊，請參閱 [建立 Blob 的快照集](http://msdn.microsoft.com/library/azure/hh488361.aspx)。
- [輸出資料傳輸](http://azure.microsoft.com/pricing/details/data-transfers/) （Azure 資料中心送出的資料） 頻寬使用量費用。

高階儲存體、 DS 系列 Vm 與 GS 系列 Vm 之定價的詳細資訊，請參閱 ︰

- [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)
- [虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)

## 為虛擬機器資料磁碟建立和使用 Premium 儲存體帳戶

本節示範如何使用 Azure Preview 入口網站、Azure PowerShell 和 Azure 命令列介面 (Azure CLI) 來建立 Premium 儲存體帳戶。 另外亦示範 Premium 儲存體帳戶的範例使用案例：在使用 Premium 儲存體帳戶時建立虛擬機器，並將資料磁碟連結至虛擬機器。

### 透過 Azure Preview 入口網站使用 Premium 儲存體建立 Azure 虛擬機器

本節說明如何使用 Azure Preview 入口網站建立 Premium 儲存體帳戶。

1.  登入 [Azure 入口網站](portal.azure.com)。 簽出 [免費試用版](http://azure.microsoft.com/pricing/free-trial/) 提供如果您沒有訂閱。

2.  在 [中樞] 功能表中，按一下 [ **新增**。

3.  在 **新增**, ，按一下 [ **一切**。 選取 **Storage，cache，+ backup**。 從該處按一下 **儲存體** 然後按一下 [ **建立**。

4.  在 [儲存體帳戶] 刀鋒視窗中，輸入儲存體帳戶名稱。 按一下 [ **定價層**。 在 **建議的定價層** 刀鋒視窗中，按一下 [ **瀏覽所有定價層**。 在 **選擇價格層** 刀鋒視窗中，選擇 [ **Premium 本地備援**。 按一下 [ **選取**。 請注意， **儲存體帳戶** ] 刀鋒視窗顯示 **STANDARD-GRS** 為 **定價層** 預設。 按一下之後 **選取**, 、 **定價層** 就會顯示為 **Premium LRS**。

    ![定價層][Image1]


5.  在 **儲存體帳戶** 刀鋒視窗中，保留預設值為 **資源群組**, ，**訂閱**, ，**位置**, ，和 **診斷**。 按一下 [ **建立**。

在 Azure 環境的完整逐步解說，請參閱 [在 Azure 預覽入口網站中建立執行 Windows 的虛擬機器](../virtual-machines-windows-tutorial-azure-preview.md)。

### 透過 Azure PwerShell 使用 Premium 儲存體建立 Azure 虛擬機器
這個 PowerShell 範例示範如何建立新的 Premium 儲存體帳戶並將使用該帳戶的資料磁碟連接至新的 Azure 虛擬機器。

1. 提供的步驟設定您的 PowerShell 環境 [如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。
2. 啟動 PowerShell 主控台，連接至您的訂用帳戶，並在主控台視窗中執行下列 PowerShell Cmdlet。 如此 PowerShell 陳述式所示，您必須指定 **類型** 參數做為 **Premium_LRS** 當您建立 premium 儲存體帳戶。

        New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. 接著建立新的 DS 系列 VM，並在主控台視窗中執行下列 PowerShell Cmdlet 以指定您要使用進階儲存體。 您可以使用相同的步驟建立 GS 系列 VM。 在命令中指定適當的 VM 大小。 例如針對 Standard_GS2︰

        $storageAccount = "yourpremiumaccount"
        $adminName = "youradmin"
        $adminPassword = "yourpassword"
        $vmName ="yourVM"
        $location = "West US"
        $imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
        $vmSize ="Standard_DS2"
        $OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
        $vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
        Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
        New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. 如果您希望 VM 有更多磁碟空間，在虛擬機器建立後於主控台視窗中執行下列 PowerShell Cmdlet，以將新的資料磁碟連結至現有的 DS 系列 VM 或 GS 系列 VM。

        $storageAccount = "yourpremiumaccount"
        $vmName ="yourVM"
        $vm = Get-AzureVM -ServiceName $vmName -Name $vmName
        $LunNo = 1
        $path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
        $label = "Disk " + $LunNo
        Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### 透過 Azure 命令列介面使用 Premium 儲存體建立 Azure 虛擬機器

 [Azure 指令行介面](../xplat-cli-install.md)(Azure CLI) 提供提供一組開放原始碼的跨平台命令集合，供您運用在 Azure 平台。 下列範例示範如何使用 Azure CLI (版本 0.8.14 和更新版本) 建立 Premium 儲存體帳戶、新的虛擬機器，並從 Premium 儲存體帳戶連接新的資料磁碟。

#### 建立 Premium 儲存體帳戶

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### 建立 DS 系列的虛擬機器

    azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
        "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-en-us-30GB" -u "myusername" -p "passwd@123"

#### 顯示虛擬機器的相關資訊

    azure vm show premium-test-vm

#### 連接新的資料磁碟

    azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### 顯示新資料磁碟的相關資訊

    azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## 後續步驟

- [在 Azure 儲存體帳戶使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969)
- [移轉至 Azure 儲存體帳戶](storage-migration-to-premium-storage.md)。
- [建立執行 Windows 的虛擬機器](../virtual-machines-windows-tutorial-azure-preview.md)
- [Azure 的虛擬機器和雲端服務大小](../virtual-machines/virtual-machines-size-specs.md)
- [儲存體文件](http://azure.microsoft.com/documentation/services/storage/)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
 


