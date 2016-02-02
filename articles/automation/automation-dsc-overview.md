<properties 
   pageTitle="Azure 自動化 DSC 概觀 | Microsoft Azure" 
   description="Azure 自動化期望狀態組態 (DSC)、其條款和已知問題的概觀" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="10/09/2015"
   ms.author="coreyp"/>


# Azure 自動化 DSC 概觀

## 什麼是 PowerShell DSC？

期望狀態組態 (DSC) 是 Windows PowerShell 中的新管理平台，可讓您使用宣告式 PowerShell 語法管理實體主機和虛擬機器的組態。

DSC 提供一組 Windows PowerShell 語言延伸模組、新的 Windows PowerShell Cmdlet 和資源，供您用來以宣告方式指定您想要設定您的軟體環境的方式。 它也提供方法來維護和管理現有的組態。

### 實際應用

以下是一些範例案例，其中您可以使用內建的 DSC 資源，以自動化方式設定和管理一組電腦 (也稱為目標節點)：

- 啟用或停用伺服器角色和功能
- 管理登錄設定
- 管理檔案和目錄
- 啟動、停止和管理程序和服務
- 管理群組和使用者帳戶
- 部署新軟體
- 管理環境變數
- 執行 Windows PowerShell 指令碼
- 修正組態從期望狀態消逝的問題
- 探索指定節點上的實際組態狀態

此外，您可以建立自訂的資源，來設定任何應用程式或系統設定的狀態。


如需有關 PowerShell DSC 的詳細資訊，請參閱: [DevOps 世界-Windows PowerShell 期望狀態組態中的組態](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx)

## 什麼是 Azure 自動化 DSC？

Azure 自動化 DSC 建置在 PowerShell DSC 中推出的基礎上，提供您更輕鬆的組態管理經驗。Azure 自動化 DSC 為相同的管理層 [PowerShell 期望狀態組態](https://technet.microsoft.com/library/dn249912.aspx) <link> Azure 自動化提供的 PowerShell 指令碼目前。

Azure 自動化 DSC 可讓您 [編寫和管理 PowerShell 期望狀態組態](https://technet.microsoft.com/library/dn249918.aspx), ，匯入 [DSC 資源](https://technet.microsoft.com/library/dn282125.aspx), ，並產生 DSC 節點組態 (MOF 文件)，全都在雲端。 這些 DSC 項目將會放在 Azure 自動化 [DSC 提取伺服器](https://technet.microsoft.com/library/dn249913.aspx) 以便在雲端或內部部署的目標節點 (例如實體和虛擬機器) 可以加以提取，自動符合它們指定，並回報其符合性到 Azure 自動化所需的狀態所需的狀態。

寧可觀賞也不要閱讀？ 請觀賞以下在 2015 年 5 月發佈的影片，此為首次發佈 Azure 自動化 DSC 的時間。 **附註：**儘管這段影片中所討論的概念和週期都是正確的，但是 Azure 自動化 DSC 自從這段影片錄製之後已經進步許多。 它現在可公開預覽、在 Azure 入口網站中具有更豐富的 UI，並支援額外的功能。

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]

## Azure 自動化 DSC 詞彙

### 組態

PowerShell DSC 引進了稱為組態的新概念。 組態可讓您透過 PowerShell 語法定義您的環境所需的狀態。 若要使用 DSC 來設定您的環境，請先使用組態關鍵字定義 Windows PowerShell 指令碼區塊，接著使用識別項，然後以大括弧 ({}) 來分隔區塊。

![替代文字](./media/automation-dsc-overview/AADSC_1.png)

在組態區塊內，您可以定義節點組態區塊，指定一組環境中應該完全相同的節點 (電腦) 的期望組態。 如此一來，節點組態可表示要設想的一或多個節點的「角色」。 節點組態區塊開頭為 node 關鍵字。 在此關鍵字之後加上角色名稱，它可以是變數或運算式。 在角色名稱之後，使用大括弧 {} 來分隔節點組態區塊。

![替代文字](./media/automation-dsc-overview/AADSC_2.png)

在節點組態區塊內，您可以定義資源區塊來設定特定 DSC 資源。 資源區塊的開頭是資源的名稱，後面接著要為該區塊指定的識別碼，然後是大括弧 {} 用來分隔區塊。

![替代文字](./media/automation-dsc-overview/AADSC_3.png)

如需詳細的組態關鍵字的相關資訊，請參閱: [了解期望狀態組態中的組態關鍵字](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "了解期望狀態組態中的組態關鍵字")

執行 (編譯) DSC 組態將會產生一或多個 DSC 節點組態 (MOF 文件)，這是 DSC 節點會套用以符合所需狀態的組態。

Azure 自動化 DSC 可讓您在 Azure 自動化中匯入、編寫並編譯 DSC 組態，類似於匯入、編寫並在 Azure 自動化中開始使用 Runbook 的方式。
>[AZURE.IMPORTANT] 在 Azure 自動化 DSC 中，組態應該僅包含一個組態區塊，具有與組態相同的名稱。 


### 節點組態

編譯了 DSC 組態之後，根據組態中的節點區塊，會產生一或多個節點組態。 節點組態與「MOF」或「組態文件」是一樣的 (如果您熟悉這些 PS DSC 詞彙)，而代表「角色」，例如 webserver 或worker，應該設想一或多個節點的期望狀態。 在 Azure 自動化 DSC 節點組態名稱的形式的 「 設定
Name.NodeConfigurationBlockName 」。

可察覺節點組態的 PS DSC 節點，應該透過 DSC 發送或提取方法來執行。 Azure 自動化 DSC 依賴 DSC 提取方法，其中要求節點組態的節點應該從 Azure 自動化 DSC 提取伺服器套用。 由於節點會對 Azure 自動化 DSC 提出要求，因此這些節點可以在防火牆之後、將所有輸入連接埠關閉等等。他們只需要對網際網路的輸出存取權。


### 節點

DSC 節點是由 DSC 所管理的任何一部機器的組態。 這可能是 Azure VM 或內部部署 VM/實體主機。 節點執行節點組態以成為並保持符合它們定義的期望狀態，而且也可以回報給報告伺服器其相關的組態狀態和符合性。

Azure 自動化 DSC 使將節點上架以由 Azure 自動化 DSC 進行管理更為輕鬆，並允許變更指派給每個節點伺服器端的節點組態，因此下一次節點檢查伺服器的指示時，會設想不同的角色並變更它的設定方式來符合組態。 節點也會向 Azure 自動化 DSC 報告其狀態和組態符合性。


### 資源

DSC 資源是可用來定義 Windows PowerShell 期望狀態組態 (DSC) 組態的建置組塊。 DSC 隨附一組內建功能，可用於設定資源，例如檔案和資料夾、伺服器功能和角色、登錄設定、環境變數，以及服務和程序。 若要深入了解內建 DSC 資源，以及如何使用它們的完整清單，請參閱 [內建 Windows PowerShell 期望狀態組態資源](https://technet.microsoft.com/library/dn249921.aspx)。

DSC 資源也可匯入成為 PowerShell 模組的一部分，以擴充內建的 DSC 資源集。 如果應執行節點的節點組態包含這些資源的參考，非預設的資源會由 DSC 節點從 DSC 提取伺服器撤下。 若要了解如何建立自訂資源，請參閱 [建置自訂 Windows PowerShell 期望狀態組態資源](https://technet.microsoft.com/library/dn249927.aspx)。

Azure 自動化 DSC 隨附與 PS DSC 相同的所有內建 DSC 資源。 透過匯入包含資源的 PowerShell 模組到 Azure 自動化，即可加入其他資源至 Azure 自動化 DSC。


### 編譯工作

Azure 自動化 DSC 中的編譯工作是組態編譯的執行個體，以建立一或多個節點組態。 它們類似於 Azure 自動化 Runbook 工作，除了它們未實際執行除了建立節點組態以外的任何工作。 編譯工作所建立的任何節點組態都會自動放在 Azure 自動化 DSC 提取伺服器上，並覆寫舊版節點組態 (如果此組態具有舊版節點組態)。 編譯工作產生之節點組態的名稱會採用 ConfigurationName.NodeConfigurationBlockName 的形式。 例如，編譯下列組態會產生稱為 "MyConfiguration.webserver" 的單一節點組態


![替代文字](./media/automation-dsc-overview/AADSC_5.png)

>[AZURE.NOTE] 就像 Runbook，一樣可以發行組態。 這與將 DSC 項目放入 Azure 自動化 DSC 提取伺服器無關。 編譯工作造成 DSC 項目放在 Azure 自動化 DSC 提取伺服器上。 如需有關在 Azure 自動化中的 「 發行 」 的詳細資訊，請參閱 [發佈 Runbook](https://msdn.microsoft.com/library/dn903765.aspx)。

Azure 自動化 DSC 目前提供下列 cmdlet 在 [Azure 資源管理員 PowerShell 模組](https://msdn.microsoft.com/library/mt244122.aspx) 用於編譯工作的管理:

-   `Get AzureRmAutomationDscCompilationJob`
-   `Get AzureRmAutomationDscCompilationJobOutput`
-   `開始 AzureRmAutomationDscCompilationJob`

## Azure 自動化 DSC 週期

從空的自動化帳戶到一組受管理的正確設定節點，會包含一組用來定義組態、將這些組態變為節點組態，並將節點上架至 Azure 自動化 DSC 及節點組態的程序。 下圖說明 Azure 自動化 DSC 週期：

![替代文字](./media/automation-dsc-overview/DSCLifecycle.png)


## 錯誤/已知問題：

- Azure 自動化 DSC 目前不支援進行部分或複合 DSC 組態。

- 必須安裝 WMF 5 的最新版本，Windows 適用的 PowerShell DSC 代理程式才能與 Azure 自動化通訊。 必須安裝 Linux 適用之 PowerShell DSC 代理程式的最新版本，Linux 才能與 Azure 自動化通訊。

- Azure 自動化不支援並存使用 PowerShell 模組。 這表示在自動化帳戶內的所有組態，必須與匯入到該自動化帳戶的最後一個版本 PowerShell 模組，以及該模組包含的組態所使用的任何 PowerShell DSC 資源搭配使用。

- 傳統的 PowerShell DSC 提取伺服器預期模組 Zip 會以格式 **ModuleName_Version.zip** 放置在提取伺服器上。 Azure 自動化預期 PowerShell 模組會以 **ModuleName.zip** 名稱格式匯入。 請參閱 [此部落格文章](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) 整合模組格式的詳細資訊所需的模組匯入至 Azure 自動化。

- 指定 DSC 資源在模組內並存且使用「每個資料夾的版本」格式的 PowerShell 模組，目前將無法在 Azure 自動化中運作。

- 匯入到 Azure 自動化的 PowerShell 模組不能包含 .doc 或 .docx 檔案。 包含 DSC 資源的某些 PowerShell 模組包含這些檔案，供說明之用。 匯入到 Azure 自動化之前，應該先從模組移除這些檔案。

- 若已先向 Azure 自動化帳戶註冊節點，或已將該節點變更為對應至不同的節點組態伺服器端，則它的狀態將會是「相容」，即使節點的狀態實際上與現在對應的節點組態不相容亦然。 當節點執行它的首次提取並傳送它的第一份報告之後，在註冊或節點組態對應變更之後，均可信任節點狀態。

- 當使用 Azure 自動化 DSC 管理 Azure VM 上架 `註冊 AzureAutomationDscNode`, ，`Set-azurevmextension`, ，或 Azure 預覽入口網站中，如果註冊失敗，並在 Azure 自動化 DSC VM 延伸模組 **未指定電腦名稱，並且組態目錄沒有任何組態檔**, ，這是誤報而 VM 註冊實際上是成功。 您可以使用驗證成功註冊 `Get AzureAutomationDscNode` 指令程式。

- 當使用 Azure 自動化 DSC 管理 Azure VM 上架 `註冊 AzureAutomationDscNode`, ，`Set-azurevmextension`, ，或在 Azure 預覽入口網站中的 Azure 自動化 DSC VM 延伸模組，它最多可能需要一小時顯示為在 Azure 自動化 DSC 節點 VM。 這是因為 VM 上憑藉著 Azure VM DSC 延伸模組的 Windows Management Framework 5.0 安裝，需要它才能將 VM 上架到 Azure 自動化 DSC。

- 在註冊之後，每個節點會自動交涉唯一的驗證憑證，該憑證於一年之後到期。 此時，當憑證即將過期時，PowerShell DSC 註冊通訊協定便無法自動更新憑證，因此您必須在一年之後重新註冊這些節點。 在重新登錄之前，請確定每個節點都正在執行 Windows Management Framework 5.0 RTM。 如果節點的驗證憑證過期，而且該節點尚未註冊，則該節點將無法與 Azure 自動化通訊，並將標示為「未回應」。 註冊執行方式與您一開始註冊節點時相同。 與憑證到期時間相距 90 天或更短時間內執行的註冊，或是憑證到期時間之後任何時間點執行的註冊，將會產生新的憑證並予以使用。

## 相關文章

- [Azure 自動化 DSC 指令程式]() https://msdn.microsoft.com/library/mt244122.aspx
- [Azure 自動化 DSC 定價]() http://azure.microsoft.com/pricing/details/automation/
- [連續部署至 IaaS Vm 使用 Azure 自動化 DSC 和 Chocolatey](自動化-dsc-cd-chocolatey.md)





