<properties
    pageTitle="透過 Azure Site Recovery 設定使用 SAN 之內部部署 VMM 站台之間的保護 | Microsoft Azure"
    description="Azure Site Recovery 可使用 SAN 複寫，以協調內部部署站台之間的 Hyper-V 虛擬機器複寫、容錯移轉及復原作業。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2015"
    ms.author="raynew"/>

# 透過 Azure Site Recovery 設定使用 SAN 之內部部署 VMM 站台之間的保護

Azure Site Recovery 可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原做出貢獻。 了解可能的部署案例中 [Azure Site Recovery 概觀](site-recovery-overview.md)。

此逐步解說說明如何使用存放裝置陣列型 (SAN) 複寫來部署 Site Recovery，將針對在某個內部部署站台中受 System Center VMM 管理之內部部署 Hyper-V 伺服器上執行的虛擬機器工作負載提供的保護，透過協調及自動化移轉至另一個 VMM 內部部署站台。

商務優點包括：

- 提供 Site Recovery 自動化的企業可擴充式複寫解決方案。
- 利用企業儲存體合作夥伴在光纖通道及 iSCSI 存放裝置之間提供的 SAN 複寫功能。 請參閱我們 [SAN 儲存體合作夥伴](http://go.microsoft.com/fwlink/?LinkId=518669)。
- 運用現有的 SAN 基礎結構保護在 Hyper-V 叢集中部署的關鍵任務應用程式。
- 為客體叢集提供支援。
- 藉由低 RTO 與 RPO 的同步複寫，以及高彈性的非同步複寫 (視存放裝置陣列功能而定) 確保不同層應用程式的複寫一致性。  
- 與 VMM 整合時，可在 VMM 主控台中提供 SAN 管理功能，且 VMM 中的 SMI-S 會探索現有的存放裝置。  


## 本文內容

本文包含概觀和部署的必要條件。 它將為您逐步解說如何在 VMM 和 Site Recovery 保存庫中設定並啟用複寫。 您將探索 VMM 中的 SAN 存放裝置並加以分類、佈建 LUN，以及將存放裝置配置給 Hyper-V 叢集。 最後它會測試容錯移轉，藉此確定一切如預期般運作。

如果您遇到的問題張貼至您 [Azure 復原服務論壇](http://go.microsoft.com/fwlink/?LinkId=313628)。

## 概觀
此案例可透過使用 SAN 複寫，將 Hyper-V 虛擬機器從一個內部部署 VMM 站台備份至另一個內部部署 VMM 站台來保護您的工作負載。

![SAN 架構](./media/site-recovery-vmm-san/ASRSAN_Arch.png)

### 案例元件

- **內部部署上的虛擬機器**— 在 VMM 私人雲端中管理您內部部署 HYPER-V 伺服器包含您想要保護的虛擬機器。
- **內部部署 VMM 伺服器**— 您可以執行您想要保護的主要站台和次要站台上的一個或多個 VMM 伺服器。
- **SAN 存放裝置**— 主要站台和次要站台上的各一個 SAN 陣列
-  **Azure Site Recovery 保存庫**— 在保存庫會協調並協調資料複寫、 容錯移轉及內部部署站台之間的復原。
- **Azure Site Recovery Provider**— 每個 VMM 伺服器上安裝提供者。

## 開始之前

### Azure 必要條件

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。 您可以開始 [免費試用版](http://aka.ms/try-azure)。 取得定價資訊位於 [Azure Site Recovery 管理員定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=378268)。

### VMM 先決條件

- 您必須至少一個 VMM 伺服器中每個內部部署站台，部署為實體或虛擬獨立伺服器，或與 System Center 2012 R2 上執行的虛擬叢集 [VMM 更新彙總套件 5.0](http://go.microsoft.com/fwlink/?LinkId=517707)。
- 在您想要保護的主要 VMM 伺服器上，以及在次要 VMM 伺服器上，應該都至少要有一個雲端。 要保護的主要雲端必須包含下列項目：
    - 一或多個 VMM 主機群組
    - 每個主機群組中的一或多個 Hyper-V 叢集。
    - 位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。

### Hyper-V 需求

- 您將需要一個部署在主要和次要站台中的 Hyper-V 主機叢集，且其應至少執行含有最新更新的 Windows Server 2012。

### SAN 必要條件

- 您可以使用 SAN 複寫，藉由 iSCSI 或光纖通道存放裝置，或藉由使用共用虛擬硬碟 (vhdx)，複寫客體叢集化虛擬機器。 SAN 的必要條件如下：
    - 您將需要設定兩個 SAN 陣列，一個在主要站台中，一個在次要站台中。
    - 應該在陣列之間設定網路基礎結構。 應該設定對等互連和複寫。 應該根據存放裝置陣列需求設定複寫授權。
    - 應該在 Hyper-V 主機伺服器與存放裝置陣列之間設定網路功能，讓主機可以使用 ISCSI 或光纖通道與存放裝置 LUN 進行通訊。
    - 看到一份 [支援的存放裝置陣列](http://go.microsoft.com/fwlink/?LinkId=518669)。
    - 應該安裝存放裝置陣列製造商所提供的 SMI-S 提供者，並且應該由此提供者管理 SAN 陣列。 根據提供者的文件說明，設定提供者。
    - 確定陣列的 SMI-S 提供者所在的伺服器，是 VMM 伺服器可透過網路以 IP 位址或 FQDN 存取的伺服器。
    - 每個 SAN 陣列都應該要有一或多個存放集區可供在此部署中使用。主要站台上的 VMM 伺服器將需要管理主要陣列，而次要 VMM 伺服器將管理次要陣列。
    - 主要站台上的 VMM 伺服器應該管理主要陣列，而次要 VMM 伺服器應該管理次要陣列。

### 網路必要條件

您可以視需要設定網路對應，以確保在容錯移轉後，會以最佳方式將複本虛擬機器置於 Hyper-V 主機伺服器上，而且這些虛擬機器可以連線到適當的 VM 網路。 請注意：

- 已啟用網路對應時，位於主要位置的虛擬機器將會連線至網路，且其位於目標位置的複本將會連線到其對應的網路。
- 如果您沒有設定網路對應，虛擬機器將不會在容錯移轉後連線到 VM 網路。
- VM 網路必須在 VMM 中設定。 如需詳細資訊，請參閱「在 VMM 中設定 VM 網路和閘道」
- 來源 VMM 伺服器上的虛擬機器應該連線到 VM 網路。 來源 VM 網路應該連結到與雲端相關聯的邏輯網路。


## 步驟 1：準備 VMM 基礎結構

為準備您的 VMM 基礎結構，您需要：

1. 確定已設定 VMM 雲端
2. 將 VMM 中的 SAN 存放裝置整合並分類。
3. 建立 LUN 並配置存放裝置
4. 建立複寫群組
5. 設定 VM 網路

### 確定已設定 VMM 雲端

Site Recovery 可協調對 VMM 雲端中 Hyper-V 主機伺服器上之虛擬機器的保護。 您必須確定在開始 Site Recovery 部署之前，已妥善設定這些雲端。 其中一些不錯的資料來源包括：

- [什麼是私用雲端的新功能](http://go.microsoft.com/fwlink/?LinkId=324952)
- [VMM 2012 和雲端](http://go.microsoft.com/fwlink/?LinkId=324956) Gunter Danzeisen 部落格上。
- [設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [在 VMM 中建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324953)
- [逐步解說 ︰ 建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324954) Keith Mayer 部落格中。

### 將 VMM 中的 SAN 存放裝置整合並分類。


在 VMM 主控台中新增 SAN 分類：

1. 在 **光纖** 工作區中按一下 [ **儲存體**。 按一下 [ **首頁** > **新增資源** > **存放裝置** 啟動 [新增存放裝置精靈。
2. 在 **選取存放裝置提供者類型** 頁面上，選取 **探索 SAN 和 NAS 裝置，並由 SMI-S 提供者管理**。

    ![提供者類型](./media/site-recovery-vmm-san/SRSAN_Providertype.png)

3. 在 **指定的通訊協定與位址的儲存體 SMI-S 提供者** 頁面上，選取 **SMI-S CIMXML** 並連接到提供者指定的設定。
4. 在 **提供者 IP 位址或 FQDN** 和 **TCP/IP 通訊埠**, ，指定用來連接到提供者設定。 您只能針對 SMI-S CIMXML 使用 SSL 連線。

    ![提供者連接](./media/site-recovery-vmm-san/SRSAN_ConnectSettings.png)

5. 在 **執行身分帳戶** 指定 VMM 執行身分帳戶可以存取提供者，或建立新的帳戶。
6. 在 **收集資訊** ] 頁面上，VMM 會自動嘗試探索並匯入存放裝置資訊。 若要重試探索，請按一下 [ **掃描提供者**。 如果探索程序成功，頁面上就會列出探索到的存放裝置陣列、存放集區、製造商、型號及容量。

    ![探索儲存體](./media/site-recovery-vmm-san/SRSAN_Discover.png)

7. 在 **選取存放裝置集區列入管理，並指派分類**, ，選取存放集區，VMM 管理，並為它們指派分類。 LUN 資訊將會從存放集區匯入。 請根據您需要保護的應用程式、其容量需求，以及您對於哪些項目要一起複寫的需求，建立 LUN。

    ![分類儲存體](./media/site-recovery-vmm-san/SRSAN_Classify.png)

### 建立 LUN 並配置存放裝置

1. 將 SAN 存放裝置整合到 VMM 之後，您將建立 (佈建) 邏輯單元 (LUN)：

    - [如何選取要在 VMM 中建立邏輯單元的方法](http://go.microsoft.com/fwlink/?LinkId=518490)
    - [如何在 VMM 中佈建存放裝置邏輯單元](http://go.microsoft.com/fwlink/?LinkId=518491)

2. 接著，配置儲存容量給 Hyper-V 主機叢集，讓 VMM 可以將虛擬機器資料部署到已佈建的存放裝置上：

    - 將存放裝置配置給叢集之前，您必須先將其配置給叢集所在的 VMM 主機群組。 請參閱 [如何將存放裝置邏輯單元配置到主機群組](http://go.microsoft.com/fwlink/?LinkId=518493) 和 [如何將存放集區配置到主機群組](http://go.microsoft.com/fwlink/?LinkId=518492)。</a>。
    - 中所述，然後配置到叢集的儲存容量 [如何在 VMM 中的 HYPER-V 主機叢集上設定存放裝置](http://go.microsoft.com/fwlink/?LinkId=513017)。</a>。

### 建立複寫群組

建立複寫群組，其中包含所有需要一起進行複寫的 LUN。

1. 在 VMM 主控台中開啟 **複寫群組** ] 索引標籤的存放裝置陣列內容，然後按一下 **新增**。
2. 接著，建立複寫群組。

    ![SAN 複寫群組](./media/site-recovery-vmm-san/SRSAN_RepGroup.png)

### 設定網路

如果您想要設定網路對應，請執行下列操作：

1. 閱讀有關 [網路對應](https://msdn.microsoft.com/library/azure/dn801052.aspx)。
2. 在 VMM 中準備 VM 網路：

    - 深入了解 [設定邏輯網路](http://go.microsoft.com/fwlink/?LinkId=386307)。設定邏輯網路 — 讀取設定邏輯網路 VMM 概觀中。
    - [設定 VM 網路](http://go.microsoft.com/fwlink/?LinkId=386308)。

## 步驟 2：建立保存庫


1. 登入 [管理入口網站](https://portal.azure.com)。


2. 展開 **Data Services** > **復原服務** 按一下 **Site Recovery 保存庫**。


3. 按一下 [ **新建** > **快速建立**。

4. 在 **名稱**, ，輸入易記名稱來識別保存庫。

5. 在 **區域**, ，選取保存庫的地理區域。 若要檢查支援的地區，請參閱在上市 [Azure Site Recovery 定價詳細資料](href="http://go.microsoft.com/fwlink/?LinkId=389880)

6. 按一下 [ **建立保存庫**。

    ![新增保存庫](./media/site-recovery-vmm-san/SRSAN_HvVault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫會被列為 **Active** 在主要 **復原服務** 頁面。


### 註冊 VMM 伺服器

1. 在 [復原服務]<b></b> 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。 您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示](./media/site-recovery-vmm-san/SRSAN_QuickStartIcon.png)

2. 在下拉式清單中，選取 **之間 HYPER-V 內部部署站台使用陣列複寫**。

    ![註冊金鑰](./media/site-recovery-vmm-san/SRSAN_QuickStartRegKey.png)

4. 在 *快速入門* 頁面上，於 **準備 VMM 伺服器**, ，按一下 [ *下載 Microsoft Azure Site Recovery Provider 的 VMM 伺服器上安裝* 以取得最新版的提供者安裝檔案。

2. 在來源 VMM 伺服器上執行此檔案。 如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。 然後在其他節點上安裝提供者。 請注意，如果您要升級提供者，您必須在所有節點上升級，因為它們都應該執行相同的提供者版本。


3. 安裝程式沒有少數 **必要條件檢查** ，並要求停止 VMM 服務以開始安裝 「 提供者的權限。 當安裝完成之後，VMM 服務將會自動重新啟動。 如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。

4. 在 **Microsoft Update** 您可以選擇進行更新。 啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

    ![Microsoft Update](./media/site-recovery-vmm-san/VMMASRInstallMUScreen.png)


1.  安裝位置設為 **<SystemDrive>files\microsoft System Center 2012 R2\Virtual Machine Manager\bin**。 按一下 [安裝] 按鈕，開始安裝提供者。
    ![InstallLocation](./media/site-recovery-vmm-san/VMMASRInstallLocationScreen.png)



1. 提供者安裝之後，請按一下 [註冊] 按鈕，在保存庫中註冊伺服器。
    ![InstallComplete](./media/site-recovery-vmm-san/VMMASRInstallComplete.png)

5. 在 **網際網路連線** 指定 VMM 伺服器上執行的提供者連線到網際網路的方式。 選取 *使用預設系統 proxy 設定* 使用在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定](./media/site-recovery-vmm-san/VMMASRRegisterProxyDetailsScreen.png)
    - 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。 當您進行自訂 proxy 設定時，會執行一項測試以檢查 proxy 連線。
    - 如果您使用自訂 proxy，或者您的預設 proxy 需要驗證，您必須輸入 proxy 詳細資料，包含 proxy 位址和連接埠。
    - 下列 URL 應可從 VMM 伺服器和 Hyper-V 主機存取
        - *.hypervrecoverymanager.windowsazure.com
        - *.accesscontrol.windows.net
        - *.backup.windowsazure.com
        - *.blob.core.windows.net
        - *.store.core.windows.net
    - 允許中所述的 IP 位址 [Azure Datacenter IP 範圍](http://go.microsoft.com/fwlink/?LinkId=511094) 和 HTTPS (443) 通訊協定。 您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

    - 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。 若要這樣做，請開啟 [設定] 工作區、展開 [安全性]、按一下 [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。 您必須重新啟動 VMM 服務，這項設定才會生效。

6. 在 **註冊金鑰**, ，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器。
7. 在 **保存庫名稱**, ，確認保存庫中註冊伺服器的名稱。 按一下 [ *下一步*。


    ![Server registration](./media/site-recovery-vmm-san/VMMASRRegisterVaultCreds.png)

9. 這項設定只適用於 Azure 案例的 VMM，如果您是 VMM 對 VMM 唯一使用者，則可以忽略這個畫面。

    ![伺服器註冊](./media/site-recovery-vmm-san/VMMASRRegisterEncryptionScreen.png)

8. 在 **伺服器名稱**, ，指定用來識別保存庫中的 VMM 伺服器的易記名稱。 在叢集設定中，指定 VMM 叢集角色名稱。

8. 在 **初始雲端中繼資料** 同步選取您是否想要在 VMM 伺服器上的所有雲端的中繼資料與保存庫同步。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。
    ![伺服器註冊](./media/site-recovery-vmm-san/VMMASRRegisterFriendlyName.png)


8. 按一下 [ *下一步* 完成程序。 註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。 伺服器會顯示在  *VMM 伺服器* 索引標籤上 **伺服器** 保存庫中的頁面。

>[AZURE.NOTE] Azure Site Recovery 提供者也可以使用下列命令列來安裝。 這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者
>
>1. 將提供者安裝檔案和註冊金鑰下載至資料夾，例如 C:\ASR
>2. 停止 System Center Virtual Machine Manager 服務
>3. 擷取提供者安裝程式，藉由執行下列命令，從命令提示字元中使用 **管理員** 權限
>
        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
>4. 執行下列命令來安裝提供者
>
        C:\ASR> setupdr.exe /i
>5. 執行下列命令來登錄提供者
>
        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         
 ####命令列安裝參數清單####
>
 - **/ 認證** ︰ 指定註冊金鑰檔所在的位置的必要參數  
 - **/Friendlyname** ︰ 必要參數，會出現在 Azure Site Recovery 入口網站中的 HYPER-V 主機伺服器的名稱。
 - **/ EncryptionEnabled** ︰ 選擇性的參數，您需要使用只能在 VMM 至 Azure 案例中，如果您需要的虛擬機器在 Azure 中的靜止的加密。 請確認您提供的檔案名稱具有 **.pfx** 延伸模組。
 - **/proxyAddress** ︰ 選擇性參數，指定 proxy 伺服器的位址。
 - **/proxyport** ︰ 指定的連接埠的 proxy 伺服器的選擇性參數。
 - **/proxyUsername** ︰ 指定的 Proxy 使用者名稱 （如果 proxy 需要驗證） 的選擇性參數。
 - **/proxyPassword** ︰ 指定的密碼來驗證 proxy 伺服器 （如果 proxy 需要驗證） 的選擇性參數。


## 步驟 4：對應存放裝置陣列和集區

對應陣列，以指定哪一個次要存放集區要從主要集區接收複寫資料。 您應該在設定雲端保護之前先對應存放裝置，因為在您為複寫群組啟用保護時，會使用此對應資訊。

在開始之前，請先確定雲端有出現在保存庫中。 選取同步處理所有雲端，當您安裝提供者，或選擇都同步某個特定的雲端上偵測到雲端 **一般** VMM 主控台中雲端內容] 索引標籤。 然後，依下列方式對應存放裝置陣列：

1. 按一下 [ **資源** > **伺服器儲存體** > **對應來源和目標陣列**。
    ![伺服器註冊](./media/site-recovery-vmm-san/SRSAN_StorageMap.png)
2. 選取主要站台上的存放裝置陣列，然後將它們對應至次要站台上的存放裝置陣列。
3.  對應陣列內的來源和目標存放集區。 若要這樣做，請在 **存放集區** 選取來源和目標存放集區對應。

    ![伺服器註冊](./media/site-recovery-vmm-san/SRSAN_StorageMapPool.png)

## 步驟 5：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。 您已啟用選項 **雲端資料與保存庫同步** 當您安裝提供者 VMM 伺服器上的所有雲端都會出現在 <b>受保護項目</b> 保存庫中的索引標籤。

![發佈的雲端](./media/site-recovery-vmm-san/SRSAN_CloudsList.png)

1. 在 [快速入門] 頁面中，按一下 [ **設定 VMM 雲端的保護**。
2. 在 **受保護項目** 索引標籤上，選取您想要設定，並移至雲端 **組態** ] 索引標籤。 請注意：
3. 在 [目標]<b></b> 中，選取 [VMM]<b></b>。
4. 在 [目標位置]<b></b> 中，選取網站上負責對您要用於復原之雲端進行管理的 VMM 伺服器。
5. 在 [目標雲端]<b></b> 中，選取要在來源雲端中用於虛擬機器容錯移轉的目標雲端。 請注意：
    - 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
    - 雲端只能當成單一雲端組中的主要雲端或目標雲端。
6. Azure Site Recovery 會確認雲端能夠存取具備 SAN 複寫功能的存放裝置，並確認存放裝置陣列已對等互連。 將會顯示參與的陣列對等項。
7. 如果驗證成功，請在 **複寫類型**, ，請選取 **SAN**。

<p>儲存設定之後，即會建立一個工作，並且可在 [工作]<b></b> 索引標籤上進行監視。 上可修改雲端設定 <b>設定</b> ] 索引標籤。 如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。</p>

## 步驟 6：啟用網路對應

1. 在 [快速入門] 頁面中，按一下 [ **對應網路**。
2. 選取您想要從其中對應網路的來源 VMM 伺服器，然後選取對應網路的目標 VMM 伺服器。 隨即會顯示來源網路的清單和與其相關聯的目標網路。 目前尚未對應的網路會顯示空白值。 按一下來源和目標網路名稱旁邊的資訊圖示，以檢視每個網路的子網路。
3. 中選取網路 **來源上的網路**, ，然後按一下 [ **對應**。 服務會偵測目標伺服器上的 VM 網路並加以顯示。

    ![SAN 架構](./media/site-recovery-vmm-san/ASRSAN_NetworkMap1.png)

4. 在顯示的對話方塊中，從目標 VMM 伺服器選取其中一個 VM 網路。

    ![SAN 架構](./media/site-recovery-vmm-san/ASRSAN_NetworkMap2.png)

5. 當您選曲目標網路時，隨即會顯示使用來源網路的受保護雲端。 同時也會顯示與用於保護之雲端相關聯的可用目標網路。 建議您選取所有用於保護之雲端都可用的目標網路。
6.  按一下打勾記號完成對應程序。 隨即有個工作啟動來追蹤對應程序。 您可以檢視上 **工作** ] 索引標籤。


## 步驟 7 ︰ 為複寫群組啟用複寫</h3>

您必須先為存放裝置複寫群組啟用複寫，才能為虛擬機器啟用保護。

1. 在 Azure Site Recovery 入口網站之主要雲端的 [內容] 頁面中開啟 **虛擬機器** ] 索引標籤。 按一下 [ **新增複寫群組**。
2. 選取一個或多個與雲端關聯的 VMM 複寫群組、確認來源和目標陣列，然後指定複寫頻率。

當此操作完成時，Azure Site Recovery 會與 VMM 和 SMI-S 提供者一起佈建目標站台存放裝置 LUN，並啟用存放裝置複寫。 如果複寫群組已經被複寫，Azure Site Recovery 便會重複使用現有的複寫關係，然後更新 Azure Site Recovery 中的資訊。

## 對虛擬機器啟用保護

在存放裝置群組正在進行複寫之後，請在 VMM 主控台中，使用下列其中一個方法為虛擬機器啟用保護：

- **新的虛擬機器**— 在 VMM 主控台中，當您建立新的虛擬機器啟用 Azure Site Recovery 保護，並將虛擬機器與複寫群組產生關聯。
使用此選項時，VMM 會使用智慧型放置，以最佳方式將虛擬機器存放裝置放置在複寫群組的 LUN 上。 Azure Site Recovery 會在次要網站上協調建立影子虛擬機器並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。
- **現有的虛擬機器**— 如果 VMM 中已經部署虛擬機器，您可以啟用 Azure Site Recovery 保護，並執行存放裝置移轉至複寫群組。 完成之後，VMM 和 Azure Site Recovery 會偵測新的虛擬機器，並開始在 Azure Site Recovery 中管理它以進行保護。 建立影子虛擬機器時，會在次要站台上建立並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。


    ![Enable protection](./media/site-recovery-vmm-san/SRSAN_EnableProtection.png)


<P>為虛擬機器啟用保護之後，它們就會出現在 Azure Site Recovery 主控台中。 您可以檢視虛擬機器內容、追蹤狀態，以及讓包含多個虛擬機器的複寫群組進行容錯移轉。 請注意，在 SAN 複寫中，與複寫群組關聯的所有虛擬機器必須都一起進行容錯移轉。 這是因為容錯移轉會先發生在儲存層。 請務必正確地組成您的複寫群組，只將相關聯的虛擬機器放在一起。</P>

追蹤在 「 啟用保護動作的進度 **工作** 索引標籤上，包括初始複寫。 執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。
    ![虛擬機器保護工作](./media/site-recovery-vmm-san/SRSAN_JobPropertiesTab.png)

## 步驟 8：測試部署</h3>

測試您的部署，以確定虛擬機器和資料容錯移轉如預期般運作。 若要這樣做，您將必須選取複寫群組來建立復原方案。然後在方案上執行測試容錯移轉。

1. 在 **復原計劃** 索引標籤上，按一下 [ **建立復原計畫**。
2. 指定復原方案的名稱，以及來源和目標 VMM 伺服器。 來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。 選取 **SAN** ，檢視為 SAN 複寫設定的雲端。
3.
    ![建立復原計畫](./media/site-recovery-vmm-san/SRSAN_RPlan.png)

4. 在 **選取虛擬機器**, ，選取複寫群組。 所有與複寫群組關聯的虛擬機器，將會被選取並新增至復原方案。 這些虛擬機器將新增到復原計畫的預設群組—群組 1。 您可以視需要新增更多的群組。 請注意，複寫之後，虛擬機器將會根據復原方案群組的順序來啟動。

    ![新增虛擬機器](./media/site-recovery-vmm-san/SRSAN_RPlanVM.png)
5. 建立復原計畫之後，它會出現在清單上 **復原計劃** ] 索引標籤。
6. 在 **復原計劃** ] 索引標籤上，選取計畫，然後按一下 [ **測試容錯移轉**。
7. 在 **確認測試容錯移轉** 頁面上，選取 **無**。 請注意，啟用此選項時，容錯移轉複本虛擬機器將不會連線到任何網路。 這將會測試虛擬機器是否依照預期執行容錯移轉，但是不會測試您的複寫網路環境。 看看如何 [執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover) 如需有關如何使用不同的網路功能選項的詳細資訊。


    ![Select test network](./media/site-recovery-vmm-san/SRSAN_TestFailover1.png)


8. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。 它並不會新增至複本虛擬機器所在的雲端。
9. 複製之後，複本虛擬機器的 IP 位址會不同於主要虛擬機器的 IP 位址。 如果您是從 DHCP 發出位址，便會自動更新位址。 如果您不使用 DHCP，而且您想要確定位址是相同的您必須執行數個指令碼。
10. 執行此範例指令碼以抓取 IP 位址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

11. 執行此範例指令碼來更新 DNS (使用上一個範例指令碼抓取到的 IP 位址)。

        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 監視活動

您可以使用 **工作** ] 索引標籤和 **儀表板** 來檢視和監視的主要工作由 Azure Site Recovery 保存庫，包括設定雲端、 啟用和停用虛擬機器的保護、 執行容錯移轉 （計劃性、 非計劃性或測試），以及認可非計劃性容錯移轉保護。

從 **工作** 索引標籤檢視工作、 向下鑽研工作詳細資料和錯誤、 執行工作查詢以擷取符合特定準則，工作將工作匯出至 Excel，以及重新啟動失敗的工作。

從 **儀表板** 可以下載最新版的提供者和代理程式安裝檔案、 取得保存庫的組態資訊、 查看由保存庫的保護、 查看最近的工作、 管理保存庫憑證中，虛擬機器的數目和重新同步虛擬機器。

如需有關與工作和儀表板互動的詳細資訊，請參閱 [作業和監視](http://go.microsoft.com/fwlink/?LinkId=398534)。


