<properties 
    pageTitle="Site Recovery：常見問題集 | Microsoft Azure" 
    description="本文討論 Azure Site Recovery 的相關熱門問題。" 
    services="site-recovery" 
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags 
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na" 
    ms.workload="storage-backup-recovery"
    ms.date="12/07/2015" 
    ms.author="raynew"/>


# Azure Site Recovery：常見問題集 (FAQ)
## 本文內容

本文包含有關 Site Recovery 的常見問題集。 如果您有疑問閱讀 theh 常見問題集後，將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。


## 支援

###Site Recovery 的功能是什麼？

Site Recovery 可藉由協調及自動執行內部部署虛擬機器與實體伺服器對 Azure 或次要資料中心的複寫，為您的商務持續性與災害復原 (BCDR) 做出貢獻。 [了解詳細](site-recovery-overview.md)。


### Site Recovery 可以保護什麼？

- **HYPER-V 虛擬機器**: Site Recovery 可以保護 HYPER-V 虛擬機器上執行任何工作負載。 
- **實體伺服器**: Site Recovery 可以保護執行 Windows 或 Linux 實體伺服器。
- **VMware 虛擬機器**: Site Recovery 可以保護在 VMware VM 上執行任何工作負載。


###我可以保護哪些 Hyper-V VM？

這取決於部署案例。 

請參閱下列主題中的 Hyper-V 主機伺服器先決條件：

- [將 Hyper-V VM (不使用 VMM) 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md/#before-you-start)
- [將 Hyper-V VM (使用 VMM) 複寫至 Azure](site-recovery-vmm-to-azure.md/#before-you-start)
- [將 Hyper-V VM 複寫至次要資料中心](site-recovery-vmm-to-vmm.md/#before-you-start)

關於客體作業系統：

- 如果您要複寫至次要資料中心，請參閱支援的客體作業系統的清單中的 HYPER-V 主機伺服器上執行的 vm [支援客體作業系統的 HYPER-V Vm](https://technet.microsoft.com/library/mt126277.aspx)。
- 如果您要複寫至 Azure，站台復原支援所有客體作業系統的 [Azure 所支援的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)。

Site Recovery 可以保護在所支援之 VM 上執行的任何工作負載。

### 當 Hyper-V 在用戶端作業系統上執行時，我可以保護 VM 嗎？

否，不支援這麼做。 而是想要 [實體用戶端機器的複寫](site-recovery-vmware-to-azure.md) 到 Azure 或次要資料中心。


### 我可以使用 Site Recovery 來保護哪些工作負載？

您可以使用 Site Recovery 來保護在虛擬機器或實體伺服器上執行的大多數工作負載。 Site Recovery 可以協助您部署應用程式感知 DR。 它整合了 Microsoft 應用程式，包括 SharePoint、 Exchange、 Dynamics、 SQL Server 和 Active Directory，並仔細搭配領導廠商包括 Oracle、 SAP、 IBM 和 Red Hat 您可以自訂每個特定的應用程式的嚴重損壞修復解決方案。 [了解詳細](site-recovery-workload.md) 有關工作負載的保護。


### 一定需要 System Center VMM 伺服器才能保護 Hyper-V 虛擬機器嗎？ 

編號 如果您要複寫至 Azure，您可以複寫 HYPER-V 主機伺服器上都是/不是位於 VMM 雲端中的 HYPER-V Vm。 如果是複寫至次要資料中心，就必須在 VMM 雲端中管理 Hyper-V 主機伺服器。 [閱讀更多資訊](site-recovery-hyper-v-site-to-azure.md)

### 如果我只有一部 VMM 伺服器，可以部署 Site Recovery 搭配 VMM 嗎？ 

是。 您可以任一個複寫至 Azure，在 VMM 伺服器上定域機組中的 HYPER-V Vm，或者您可以在相同伺服器上的 VMM 雲端之間進行複寫。 請注意，若要在內部部署的單位間進行複寫，建議您在主要與次要站台中具有 VMM 伺服器。  [閱讀更多資訊](site-recovery-single-vmm.md)

### 我可以保護哪些實體伺服器？

您可以將執行 Windows 或 Linux 的實體伺服器複寫至 Azure 或次要站台來進行保護。 如需作業系統需求讀取 [我需要什麼？](site-recovery-vmware-to-azure.md/#what-do-i-need)。 不論是將實體伺服器複寫至 Azure 還是次要站台，都適用相同的限制。

請注意，如果您的內部部署伺服器當機，實體伺服器將會在 Azure 中以 VM 的身分執行。 容錯回復目前並不能以內部部署的實體伺服器作為目標，但您仍可將 Hyper-V 或 VMware 上所執行的虛擬機器作為目的地。

### 我可以保護哪些 VMware VM？

針對此案例，您將需要一部 VMware vCenter 伺服器、一個 vSphere Hypervisor 以及幾個執行 VMware 工具的虛擬機器。 如實際需求，請參閱 [我需要什麼？](site-recovery-vmware-to-azure.md/#what-do-i-need)。 不論是將實體伺服器複寫至 Azure 還是次要站台，都適用相同的限制。

### 將虛擬機器複寫至 Azure 有任何先決條件嗎？

您想要複寫至 Azure 虛擬機器都應該遵守 [Azure 需求](site-recovery-best-practices.md/#virtual-machines)。 

### 我可以將 Hyper-V 第 2 代虛擬機器複寫至 Azure 嗎？

是。 Site Recovery 會在容錯移轉時從第 2 代轉換成第 1 代。 在容錯回復時，機器會轉換回第 2 代。 [閱讀更多](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。

### 如果複寫至 Azure，我要如何支付 Azure VM 費用？ 

在一般複寫期間，就會將資料複寫至異地備援的 Azure 儲存體，您不需要支付任何 Azure IaaS 虛擬機器費用 (這是一項重要的優點)。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器，之後就會針對您在 Azure 中取用的運算資源進行計費。

### 我可以使用 Site Recovery 來管理分公司的災害復原嗎？

是。 當您使用 Site Recovery 來協調分公司中的複寫與容錯移轉時，會為您集中提供所有分公司工作負載的整合協調與檢視。 您可以輕鬆執行容錯移轉和管理的所有分支的災害復原，從總公司，而沒有瀏覽分支。 

### 有可以用來將 Site Recovery 工作流程自動化的 SDK 嗎？

是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。 深入了解 [部署 Site Recovery 使用 PowerShell](site-recovery-deploy-with-powershell.md)。


## 安全性與法規遵循

### 我的資料會傳送到 Site Recovery 服務嗎？

否，Site Recovery 不會攔截您的應用程式資料，也不會擁有您虛擬機器或實體伺服器上執行哪些項目的任何相關資訊。

複寫資料會在 Hyper-V 主機、VMware Hypervisor 或您主要和次要資料中心內的實體伺服器之間進行交換，或是在您資料中心與 Azure 儲存體之間進行交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。 

Site Recovery 已經過 ISO 27001:2005 認證，並且正在完成其 HIPAA、DPA 及 FedRAMP JAB 評定程序。

### 為了遵循法規，我們甚至連內部部署環境中的中繼資料也必須保留在相同的地理區域內。 Site Recovery 可以幫助我們嗎？

是。 當您在某個區域中建立 Site Recovery 保存庫時，我們會確保我們啟用及協調複寫與容錯移轉時所需的一切中繼資料都會保留在該區域地理界限內。

### Site Recovery 會將複寫加密嗎？
在內部部署站台之間複寫虛擬機器和實體伺服器時，支援傳輸中加密。 在內部部署站台與 Azure 之間複寫虛擬機器和實體伺服器時，同時支援傳輸中加密和在 Azure 中加密。 




## 複寫及容錯移轉

### 如果要複寫至 Azure，我需要哪一種儲存體帳戶？

您將需要的儲存體帳戶 [標準的地理區域備援儲存體](../storage/storage-redundancy.md/#geo-redundant-storage)。 A [高階儲存體帳戶](../storage/storage-premium-storage-preview-portal/) 時，才支援 VMware 虛擬機器或 Windows/Linux 實體伺服器複寫至 Azure。   

支援標準的本機備援儲存體是在待處理項目中，傳送有關這項功能的意見反應 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support)。

### 我可以多久複寫一次資料？
- **Hyper-v:** HYPER-V Vm 可以複寫每隔 30 秒，5 分鐘或 15 分鐘。 如果您已設定 SAN 複寫，則複寫將會是同步的。
- **VMware 和實體伺服器 ︰** 此處不相關的複寫頻率。 複寫將會是連續的。 

### 我可以將複寫從現有的復原網站延伸到另一個第三網站嗎？
不支援延伸的或鏈結的複寫。 傳送有關這項功能的意見反應 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)。


### 我可以在第一次複寫至 Azure 時進行離線複寫嗎？ 

不支援此做法。 傳送有關這項功能的意見反應 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)。


### 我可以從複寫中排除特定的磁碟嗎？

不支援此做法。 傳送有關這項功能的意見反應 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication)。

### 與動態磁碟的虛擬機器可以複寫嗎？

複寫 Hyper-V 虛擬機器時，支援使用動態磁碟。 複寫 VMware 虛擬機器或實體伺服器時，則不支援。 傳送有關這項功能的意見反應 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery)。

### 如果容錯移轉到 Azure，在容錯移轉之後，我要如何存取存取 Azure 虛擬機器？ 

您可以透過安全的網際網路連線，或是如果您有的話，也可以透過站台對站台 VPN (或 Azure ExpressRoute)，存取 Azure VM。 透過 VPN 連線的通訊會通向 VM 所在的 Azure 網路上的內部連接埠。 透過網際網路的通訊會對應到 Azure 雲端服務上 VM 的公用端點。 [閱讀更多資訊](site-recovery-network-design.md/#connectivity-after-failover)

### 如果我容錯移轉到 Azure，Azure 如何確定我的資料具有復原能力？

Azure 是針對服務復原能力而設計的。 Site Recovery 已經設計成可在需要時，容錯移轉到符合 Azure SLA 的次要 Azure 資料中心。 當此情況發生時，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。 

### 如果我在兩個資料中心之間進行複寫，當我的主要資料中心發生意外中斷時，會發生什麼情況？

您可以從次要站台觸發非計劃性容錯移轉。 Site Recovery 不需要來自主要站台的連線即可執行容錯移轉。


### 容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 起始容錯移轉，只要按在網站中，或者您可以使用 [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) 觸發容錯移轉。 容錯回復在 Site Recovery 中也是一個簡單的動作。 若要進行自動化，您可以使用內部部署 Orchestrator 或 Operations Manager 來偵測虛擬機器失敗，然後使用 SDK 來觸發容錯移轉。

### 如果我複寫 Hyper-V VM，可以調節為 Hyper-V 複寫流量配置的頻寬嗎？
- 如果您是在兩個內部部署站台上的 Hyper-V VM 之間進行複寫，則您可以使用 Windows QoS。 以下是一個簡單的指令碼： 

        New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
        gpupdate.exe /force

- 如果您是將 Hyper-V VM 複寫至 Azure，您可以使用以下的範例 PowerShell Cmdlet 來設定節流：

        Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## 服務提供者部署

### Site Recovery 適用於專用或共用的基礎結構模型嗎？
是，Site Recovery 同時支援專用與共用的基礎結構模型。

### 我租用戶的身分識別會與 Site Recovery 服務共用嗎？
編號 事實上，您的租用戶不需要站台復原入口網站存取權。 只有服務提供者系統管理員會在入口網站中執行動作。


### 我租用戶的應用程式資料會傳送到 Azure 嗎？
在服務提供者擁有的站台之間進行複寫時，永遠不會將應用程式資料傳送到 Azure。 資料會在傳輸中加密，並且會在服務提供者站台之間直接進行複寫。

如果您是複寫至 Azure，應用程式資料就會傳送到 Azure 儲存體而不是 Site Recovery 服務。 資料會在傳輸中加密並在 Azure 中繼續維持加密狀態。 

### 我的租用戶會收到來自 Azure 服務的帳單嗎？

編號 Azure 的計費關係是直接與服務提供者。 服務提供者負責為其租用戶產生特定的帳單。

### 如果我複寫至 Azure，我們需要在 Azure 中隨時執行虛擬機器嗎？

否，資料會複寫至您訂用帳戶中的異地備援 Azure 儲存體帳戶。 當您執行測試容錯移轉 (DR 演練) 或實際容錯移轉時，Site Recovery 會在您的訂用帳戶中自動建立虛擬機器。

### 當我複寫至 Azure 時，您會確保提供租用戶層級的隔離嗎？

是。

### 目前支援哪些平台？

我們支援「Azure 套件」、「雲端平台系統」及 System Center 架構 (2012 及更新版本) 的部署。 深入了解 Azure Pack 和站台復原中整合 [設定虛擬機器的保護](https://technet.microsoft.com/library/dn850370.aspx)。

### 您支援單一 Azure 套件與單一 VMM 伺服器部署嗎？

是，您可以複寫 Hyper-V 虛擬機器與 Azure，或是在服務提供者站台之間進行複寫。  請注意，如果您是在服務提供者站台之間進行複寫，將無法使用 Azure Runbook 整合。


## 後續步驟

- 讀取 [站台復原概觀](site-recovery-overview.md)
- 深入了解 [站台復原架構](site-recovery-components.md)  

 


