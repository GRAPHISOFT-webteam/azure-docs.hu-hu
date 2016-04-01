<properties
    pageTitle="監視、診斷與疑難排解儲存體 | Microsoft Azure"
    description="使用儲存體分析、用戶端記錄和其他協力廠商工具等功能，為 Azure 儲存體的相關問題進行識別、診斷與疑難排解。"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="jahogg"/>

# 監視、診斷與疑難排解 Microsoft Azure 儲存體

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## 概觀

與傳統環境相比，託管於雲端環境的分散式應用程式一旦發生問題，無論要為其進行診斷或疑難排解，都更加複雜。 應用程式可以部署在 PaaS 或 IaaS 基礎架構、內部部署環境、行動裝置或是這幾種環境的組合上。 一般來說，您的應用程式網路流量可能會跨越公共與私有網路，而您的應用程式有可能使用多項儲存技術，例如 Microsoft Azure 儲存體資料表、Blob、佇列或是檔案，乃至於關聯式資料庫與文件資料庫之類的其他資料存放區。

若要成功管理這類應用程式，您除了需要主動監視它們之外，還需要了解如何為其各層面與相依技術進行診斷與疑難排解。 身為 Azure 儲存體服務的使用者，您應持續監視應用程式所使用的儲存體服務，以預防發生非預期的行為改變 (例如，回應速度明顯比平時慢)，並使用記錄功能來收集更多的詳細資料，同時深入分析問題的成因。 從監視與記錄手段中取得的診斷資訊，將在應用程式遭遇問題時，協助您判斷根本原因。 接著才能為問題進行疑難排解，並決定該採取哪些合宜的步驟來加以矯正。 Azure 儲存體是 Azure 的核心服務之一，更在客戶部署至 Azure 基礎結構的主要解決方案之中扮演著重要的環節。 Azure 儲存體會在您的雲端架構應用程式裡加入各項功能，從而簡化儲存體問題的監視、診斷與疑難排解程序。

端對端疑難排解 Azure 儲存體應用程式中的實際操作指南，請參閱 [端對端使用 Azure 儲存體度量和記錄、 AzCopy 和 Message Analyzer 來排解疑難](../storage-e2e-troubleshooting/)。

+ [簡介]
    + [本指南的組織架構]
+ [監視您的儲存體服務]
    + [監視服務健康情況]
    + [監視容量]
    + [監視可用性]
    + [監視效能]
+ [診斷儲存體問題]
    + [服務健康情況問題]
    + [效能問題]
    + [診斷錯誤]
    + [儲存體模擬器問題]
    + [儲存體記錄工具]
    + [使用網路記錄工具]
+ [端對端追蹤]
    + [為記錄資料建立相互關聯]
    + [用戶端要求 ID]
    + [伺服器要求 ID]
    + [時間戳記]
+ [疑難排解指引]
    + [度量顯示高 AverageE2ELatency 與低 AverageServerLatency]
    + [度量顯示低 AverageE2ELatency 與低 AverageServerLatency，但用戶端正經歷高延遲]
    + [度量顯示高 AverageServerLatency]
    + [佇列上的訊息在遞送期間出現非預期的延遲]
    + [度量顯示 PercentThrottlingError 增加]
    + [度量顯示 PercentTimeoutError 增加]
    + [度量顯示 PercentNetworkError 增加]
    + [用戶端收到 HTTP 403 (禁止) 訊息]
    + [用戶端收到 HTTP 404 (找不到) 訊息]
    + [用戶端收到 HTTP 409 (衝突) 訊息]
    + [度量顯示低 PercentSuccess，或是分析記錄項目內含具有 ClientOtherErrors 交易狀態的作業項目]
    + [容量度量顯示非預期的儲存體容量使用增加]
    + [附加大量 VHD 的虛擬機器，出現非預期的重新開機情況]
    + [您的問題起因於使用儲存體模擬器進行開發或測試]
    + [安裝 Azure SDK for .NET 時發生問題]
    + [您的儲存體服務出現其他問題]
+ [附錄]
    + [附錄 1：使用 Fiddler 擷取 HTTP 與 HTTPS 流量]
    + [附錄 2：使用 Wireshark 擷取網路流量]
    + [附錄 3：使用 Microsoft Message Analyzer 擷取網路流量]
    + [附錄 4：使用 Excel 檢視度量與記錄資料]
    + [附錄 5：使用 Application Insights for Visual Studio Team Services 監視]

## <a name="introduction"></a>簡介

本指南說明如何使用 Azure 儲存體分析、Azure 儲存體用戶端程式庫內的用戶端記錄，與其他協力廠商工具等功能，來為 Azure 儲存體的相關問題進行識別、診斷與疑難排解。

![][1]

*圖 1 監視、診斷與疑難排解*

本指南主要提供給使用 Azure 儲存體服務的開發人員，以及負責管理此類線上服務之 IT 專業人士閱讀。 本指南宗旨如下：

- 協助您維持 Azure 儲存體帳戶的健康情況與效能。
- 提供您必要的程序與工具，協助您判斷應用程式內的某個問題是否與 Azure 儲存體有關。
- 提供您可行的指引，幫助您解決與 Azure 儲存體相關的問題。

### <a name="how-this-guide-is-organized"></a>本指南架構

一節 「[Monitoring your storage service]」 說明如何監視的健全狀況和效能的 Azure 儲存體服務使用 Azure 儲存體分析度量 (儲存體度量)。

一節 「[Diagnosing storage issues]」 說明如何使用 Azure 儲存體分析記錄 (儲存體記錄) 來診斷問題。 並說明如何透過 Storage Client Library for .NET 或是 Azure SDK for Java 之類的任何一項用戶端程式庫來啟用用戶端記錄。

一節 「[End-to-end tracing]」 將告訴您如何相互關聯各種記錄檔與度量資料中所包含的資訊。

一節 「[Troubleshooting guidance]」 提供可能會遇到的一些常見的儲存體相關問題您疑難排解指引。

「[Appendices]」 包含使用 Wireshark 與 Netmon 等其他工具來分析網路封包資料，使用 Fiddler 來分析 HTTP/HTTPS 訊息，以及 Microsoft Message Analyzer 相互關聯的記錄資料的相關資訊。


## <a name="monitoring-your-storage-service"></a>監視您的儲存體服務

如果您熟悉 Windows 效能監視，可以將儲存體度量當成相當於 Windows 效能監視器計數的 Azure 儲存體。 儲存體度量中您會發現一組完整的度量 (Windows 效能監視器詞彙裡的計數器)，例如服務可用性、 服務，要求的總數或服務要求成功的百分比 (如需可用度量的完整清單，請參閱 <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存體分析度量資料表結構描述</a> 位於 MSDN)。 您可以指定讓儲存體服務每小時或每分鐘收集並彙總度量一次。 如需如何啟用度量並監視儲存體帳戶的詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">啟用儲存體度量</a> (在 MSDN 上)。

您可以選擇您想要顯示在一種小時度量 [Azure 入口網站](portal.azure.com) 並設定以電子郵件通知系統管理員，只要小時度量超出特定閥值的規則 (如需詳細資訊，請參閱頁面 <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">How to: 接收警示通知及管理在 Azure 中的警示規則</a>)。 儲存體服務會盡其所能收集各項度量，但是不一定會記錄每一次的儲存體操作。

在 Azure 入口網站中，您可以檢視可用性、總要求數，與儲存體帳戶平均延遲數等度量。 同時設定了一個通知規則，會在可用性降至特定水準以下時，通知管理員。 從檢視這項資料，其中一個可能的區域，以便調查是資料表服務成功百分比低於 100%(如需詳細資訊，請參閱 「[Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]」)。

您應該藉由以下方式，持續監視您的 Azure 應用程式，確保這些程式如預期地健全並正常運作：

- 建立一些應用程式的基準度量，以便您比較目前的資料並發現 Azure 儲存體與您的應用程式的行為重大改變。 在許多情況中，您的基準度量值將適用特定應用程式，因此當您要測試應用程式效能時，請建立這些基準度量。
- 記錄每分鐘度量並使用這些度量主動監視非預期的錯誤與異常行為，例如錯誤計數或要求率的暴增情況。
- 記錄每小時度量並使用這些度量監視一些平均值，例如平均錯誤計數與要求率。
- 調查潛在的問題，如稍後節所述，使用診斷工具 」[Diagnosing storage issues]。 」

下圖 3 裡的圖表顯示每小時度量平均值會隱藏活動裡的暴增情況。 每小時度量所顯示的要求率極為穩定，而每分鐘度量顯示的才是真正發生的變動起伏情況。

![][3]

本小節剩下部分說明您應該監視的度量項目及這麼做的原因。

### <a name="monitoring-service-health"></a>監視服務健康情況

您可以使用 [Azure 入口網站](portal.azure.com) 來檢視全球各個 Azure 區域中的儲存體服務 (和其他 Azure 服務) 的健全狀況。 藉此可以立即瞭解，不受您控制的問題所影響的區域，是否也涵蓋了您為應用程式使用儲存體服務區域。

 [Azure 入口網站](portal.azure.com) 也可以提供會影響各種 Azure 服務之事件的通知。
注意：這項資訊之前是與歷程資料一起在「Azure 服務儀表板」(網址 <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>) 上提供。

雖然 [Azure 入口網站](portal.azure.com) 收集健全狀況資訊從 (內到外監視)、 Azure 資料中心內您可以考慮採用從外的方式來產生定期存取來自多個位置的 Azure 裝載的 web 應用程式的綜合交易。 <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">Keynote</a>、<a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a> 及 Application Insights for Visual Studio Team Services 所提供的各項服務都是這個由外到內的方式範例之一。 如需有關 Application Insights for Visual Studio Team Services，請參閱 「 附錄 」[Appendix 5: Monitoring with Application Insights for Visual Studio Team Services]。 」

### <a name="monitoring-capacity"></a>監視容量

儲存體度量只會儲存 Blob 服務的容量度量，這是因為 Blob 通常佔已儲存的資料最大宗 (寫入期間無法使用儲存體度量來監視資料表與佇列的容量)。 您可以找到這項資料在 **$MetricsCapacityBlob** 資料表，如果您已啟用 Blob 服務的監視。 儲存體度量記錄此一天，一次資料，您可以使用的值 **RowKey** 來判斷資料列是否包含使用者資料的相關實體 (值 **資料**) 或分析資料 (值 **分析**)。 每個預存的實體包含使用的儲存體數量的相關資訊 (**容量** 以位元組為單位) 和目前容器編號 (**ContainerCount**) 及 blob (**ObjectCount**) 儲存體帳戶中使用。 如需有關儲存的容量度量 **$MetricsCapacityBlob** 資料表，請參閱 <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存體分析度量資料表結構描述</a> MSDN 上。

> [AZURE.NOTE] 您應該監視這些數值，以便在接近儲存體帳戶的容量限制時早期預警。 在 Azure 入口網站中，您可以新增警示規則，以便在彙總儲存體使用量超出或低於指定的閥值時通知您。

如需有關 blob 之類的各種儲存體物件大小估計說明，請參閱部落格文章 <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">了解 Azure 儲存體計費 - 頻寬、交易和容量</a>.

### <a name="monitoring-availability"></a>監視可用性

您也應該監視中的值儲存體帳戶中監視儲存體服務的可用性 **可用性** 每小時或每分鐘度量資料表中的資料行 — **$MetricsHourPrimaryTransactionsBlob**, ，**$MetricsHourPrimaryTransactionsTable**, ，**$MetricsHourPrimaryTransactionsQueue**, ，**$MetricsMinutePrimaryTransactionsBlob**, ，**$MetricsMinutePrimaryTransactionsTable**, ，**$MetricsMinutePrimaryTransactionsQueue**, ，**$MetricsCapacityBlob**。  **可用性** 資料行包含代表服務或資料列所代表的 API 作業的可用性百分比值 ( **RowKey** 是否資料列內含整體服務或特定 API 操作度量會顯示)。

任何小於 100% 的值，皆表示某些儲存體要求已經失敗。 您可以看到檢視度量資料，例如顯示的不同錯誤類型的要求數字中的其他資料欄的失敗原因 **ServerTimeoutError**。 您應該會看到 **可用性** 百分比暫時低於 100%暫時性伺服器逾時，當服務移動資料分割以便提供更佳的負載平衡要求; 在用戶端應用程式的重試邏輯應該會處理此類間歇性狀況。 頁面 <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> 列出交易類型的儲存體度量納入其 **可用性** 計算。

在 [Azure 入口網站](portal.azure.com), ，您可以新增警示規則，以便通知您，如果 **可用性** 服務低於您指定的閥值。

「[Troubleshooting guidance]」 一節，說明一些常見儲存體服務相關問題可用性。

### <a name="monitoring-performance"></a>監視效能

若要監視儲存體服務效能，您可以使用下列來自每小時與每分鐘度量表的度量。

- 中的值 **AverageE2ELatency** 和 **AverageServerLatency** 資料行顯示的平均時間的儲存體服務或是 API 操作類型來處理要求。 **AverageE2ELatency** 是端對端延遲，內含讀取要求並傳送回應的時間來處理要求所花費的時間量值 (因此包括網路延遲一旦要求抵達儲存體服務); **AverageServerLatency** 的處理時間量值，因此會排除任何與用戶端通訊相關的網路延遲。 請參閱一節 「[Metrics show high AverageE2ELatency and low AverageServerLatency]」 原因的討論本指南稍後可能會有顯著的差異，這兩個值之間。
- 中的值 **TotalIngress** 和 **TotalEgress** 資料行會顯示總量的資料，以位元組為單位，寄至和進出儲存體服務或是透過特定 API 操作類型。
- 中的值 **TotalRequests** 欄會顯示儲存體服務 API 操作所收到的要求總數。 **TotalRequests** 是儲存體服務收到的要求總數。

一般來說，您需要監視這些值是否出現非預期的改變，以便察覺是否有需要進一步調查原因的問題。

在 [Azure 入口網站](portal.azure.com), ，您可以新增任何此服務的效能度量低於或超過您指定的閥值時通知您的警示規則。

「[Troubleshooting guidance]」 一節，描述與效能有關的一些常見儲存體服務問題。


## <a name="diagnosing-storage-issues"></a>診斷儲存體問題

您可以藉由許多方式來察覺應用程式裡的問題，包括：

- 導致應用程式當機或是停止運作的重大失敗。
- 上一節中所述，您要監視的度量中基準值的重大變更 「[Monitoring your storage service]。 」
- 您的應用程式使用者回報表示，某些特定操作無法如預期完成，或是某些功能無法正常運作。
- 您的應用程式所產生，並顯示在記錄檔或是透過其他通知方法顯示的錯誤。

一般來說，與 Azure 儲存體服務相關的問題不外乎以下四個廣義類別之一：

- 應用程式出現效能問題 (可能由使用者回報，或是從效能度量中的變化推測得知)。
- Azure 儲存體基礎結構在下列一或多個區域中出現問題。
- 應用程式出現錯誤 (可能由使用者回報，或是因您所監視的錯誤計數度量之一開始增加而加以推測得知)
- 在開發與測試期間，您可能會用到本機儲存體模擬器；您可以會碰到一些特別與儲存體模擬器使用情況相關的問題。

以下章節概述當您對這四個類別分別進行問題診斷與疑難排解時，應該遵循的步驟。 一節 「[Troubleshooting guidance]"本指南稍後提供進一步的詳細資訊，您可能會遇到一些常見的問題。

### <a name="service-health-issues"></a>服務健康情況問題

服務健康情況問題通常是您無法掌控的部分。  [Azure 入口網站](portal.azure.com) 與 Azure 服務，包括儲存體服務提供進行中的任何問題的相關資訊。 若您在建立儲存體帳戶時選擇使用「讀取存取地理區域備援儲存體」，則當主要位置無法提供您的資料時，您的應用程式會暫時切換到次要位置的唯讀副本。 若要這麼做，您的應用程式必須要能切換使用主要與次要儲存位置，並能在降低功能模式下使用唯讀資料。 Azure 儲存體用戶端程式庫可讓您定義重試原則，當無法從主要儲存體讀取資料時，才能嘗試從次要儲存體讀取資料。 您的應用程式還需要了解次要位置的資料最終會與主要位置的資料維持一致。 如需詳細資訊，請參閱部落格文章 <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Azure 儲存體備援選項與讀取存取異地備援儲存體</a>。

### <a name="performance-issues"></a>效能問題

應用程式效能是很主觀的問題，尤其是從使用者觀點來看。 因此，我們需要一套基準度量來協助您識別出現效能問題的位置。 從用戶端應用程式觀點來看，許多因素都會影響 Azure 儲存體服務的效能。 這些因素可能會影響儲存體服務、用戶端或是網路基礎結構，因此我們有必要制訂策略來找出效能問題的源頭。

當您從度量中找出了效能問題可能發生的位置時，就可以使用記錄檔來找出詳細資訊以便稍後進行診斷並疑難排解問題。

一節 「[Troubleshooting guidance]"本指南稍後會提供一些常見效能相關的詳細資訊，發出您可能會發生。

### <a name="diagnosing-errors"></a>診斷錯誤

您的應用程式使用者可能會通知您用戶端應用程式所回報的一些錯誤。 儲存體度量還會記錄來自儲存體服務的不同錯誤類型計數例如 **NetworkError**, ，**ClientTimeoutError**, ，或 **AuthorizationError**。 雖然儲存體度量只會記錄不同的錯誤類型計數，不過您還是可以藉由檢視伺服器端、用戶端與網路記錄來取得個別要求的詳細資料。 一般來說，儲存體服務所傳回的 HTTP 狀態碼會指示要求失敗的原因。

> [AZURE.NOTE] 請記住，您應該會看到一些間歇性錯誤: 例如，因為暫時性網路狀況或是應用程式錯誤。

以下位於 MSDN 的資源有助您了解儲存體相關狀態與錯誤碼：

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">常見的 REST API 錯誤碼</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Blob 服務錯誤碼</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">佇列服務錯誤碼</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">資料表服務錯誤碼</a>

### <a name="storage-emulator-issues"></a>儲存體模擬器問題

Azure SDK 包含一個儲存體模擬器，可供您在開發工作站上執行。 這個模擬器會模擬大部分的 Azure 儲存體服務行為，而且在開發與測試期間非常有用，能讓您在沒有 Azure 訂閱與 Azure 儲存體帳戶的情況下執行使用 Azure 儲存體服務的應用程式。

「[Troubleshooting guidance]」 一節，說明使用儲存體模擬器會碰到的一些常見問題。

### <a name="storage-logging-tools"></a>儲存體記錄工具

儲存體記錄功能可針對您的 Azure 儲存體帳戶，提供伺服器端的儲存體要求記錄服務。 如需如何啟用伺服器端記錄及存取記錄資料的詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">使用伺服器端記錄</a> (在 MSDN 上)。

Storage Client Library for .NET 能讓您針對應用程式所執行的儲存體操作，收集與其相關的用戶端記錄資料。 如需如何啟用用戶端記錄及存取記錄資料的詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">使用儲存體用戶端程式庫的用戶端記錄</a> (在 MSDN 上)。

> [AZURE.NOTE] 在某些情況下 (例如 SAS 授權失敗)，使用者可能會回報的錯誤，您可以找到任何要求資料在伺服器端儲存體記錄中。 您可以使用儲存體用戶端程式庫裡的記錄功能，調查問題是否出現在用戶端，或是使用網路監視工具來調查網路。

### <a name="using-network-logging-tools"></a>使用網路記錄工具

您可以擷取用戶端與伺服器之間的流量，針對用戶端與伺服器在交換的資料，以及在底層運作的網路狀況提供詳細資訊。 有用的網路記錄工具如下：

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) 是免費的 Web 偵錯 Proxy，可讓您檢視 HTTP 與 HTTPS 要求及回應訊息的標頭與裝載資料。 如需詳細資訊，請參閱 「[Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]」。
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) 與 Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) 都是免費的網路通訊協定分析器，可讓您針對廣泛的網路通訊協定檢視詳細封包資訊。 如需有關 Wireshark 的詳細資訊，請參閱 「[Appendix 2: Using Wireshark to capture network traffic]」。
- 來自 Microsoft 的 Microsoft Message Analyzer 工具功能比 Netmon 更強，除了可擷取網路封包資料之外，還能協助您檢視並分析其他工具所擷取的記錄資料。 如需詳細資訊，請參閱 「[Appendix 3: Using Microsoft Message Analyzer to capture network traffic]」。
- 如果您想要執行基本連線測試，以檢查您的用戶端電腦可以透過網路連接到 Azure 儲存體服務，您無法使用標準 **ping** 用戶端上的工具。 不過，您可以使用 **tcping** 工具來檢查連線能力。 **Tcping** 是下載 <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>。

在許多案例中，來自儲存體記錄與儲存體用戶端程式庫的記錄資料，用來診斷問題都已綽綽有餘，但是在某些情況中，您可能需要比這些網路記錄工具所能提供的資訊還要詳盡的資料才行。 舉例來說，透過 Fiddler 來檢視 HTTP 與 HTTPS 訊息可以讓您檢視在儲存體服務之間來回傳送的標頭與裝載資料，進一步幫助您確認用戶端應用程式如何重試儲存體操作。 諸如 Wireshark 之類的通訊協定分析器可在封包層級運作，方便您檢視 TCP 資料，從而為遺失的封包與連線問題進行疑難排解。 Message Analyzer 可同時在 HTTP 與 TCP 網路層運作。

## <a name="end-to-end-tracing"></a>端對端追蹤

使用各種記錄檔案進行的端對端追蹤方式，是調查潛在問題的有效方法。 您可以使用度量資料裡的日期/時間資訊當作參考，來判斷該從記錄檔的哪個區段開始檢視，以獲得問題的詳細資訊，並協助您進行疑難排解。

### <a name="correlating-log-data"></a>為記錄資料建立相互關聯

檢視來自用戶端應用程式、網路追蹤與伺服器端儲存體記錄的記錄時，您必須要能為各記錄檔的要求建立相互關聯。 這些記錄檔包含一些可當成相互關聯識別碼來使用的不同欄位。 需為不同記錄中的項目建立相互關聯時，用戶端要求 ID 是最有用處的欄位。 然而，有時使用伺服器要求 ID 或是時間戳記也是可以的。 以下章節針對這些選項提供詳盡的說明。

### <a name="client-request-id"></a>用戶端要求 ID

儲存體用戶端程式庫會自動為每一項要求產生唯一的用戶端要求 ID。

- 在用戶端記錄檔儲存體用戶端程式庫所建立，用戶端要求 id 會出現在 **用戶端要求 ID** 欄位與要求相關的每個記錄項目。
- Fiddler 所擷取網路追蹤時，用戶端要求 id 會顯示在要求訊息中，以 **x-用戶端要求-ms-request-id** HTTP 標頭值。
- 在伺服器端的儲存體記錄中，用戶端要求 ID 會顯示在 [用戶端要求 ID] 資料欄。

> [AZURE.NOTE] 很多個要求可以共用相同的用戶端要求 id，因為用戶端可以指派此值可能 (雖然儲存體用戶端程式庫會指派
> 新值自動)。 如果重試來自用戶端，則所有嘗試都共用相同的用戶端要求 ID。 如果批次是從用戶端傳送，則該批次具有單一用戶端要求 ID。


### <a name="server-request-id"></a>伺服器要求 ID

儲存體服務會自動產生伺服器要求 ID。

- 在伺服器端儲存體記錄中，伺服器要求 id 會顯示 **Request ID header** 資料行。
- Fiddler 所擷取網路追蹤時，伺服器要求 id 會出現在回應訊息中，以 **x ms-要求 id** HTTP 標頭值。
- 在用戶端記錄檔儲存體用戶端程式庫所建立，伺服器要求 id 會出現在 **作業文字** 內含伺服器回應的詳細資料之記錄檔項目的資料行。

> [AZURE.NOTE] 儲存體服務一律會指派唯一的伺服器要求 id 對每個要求收到，因此從用戶端每次重試和批次中每個作業都有唯一的伺服器要求 id。

如果儲存體用戶端程式庫會擲回 **StorageException** 在用戶端， **RequestInformation** 屬性包含 **RequestResult** 物件，其中包含 **ServiceRequestID** 屬性。 您也可以存取 **RequestResult** 物件從 **OperationContext** 執行個體。

下列程式碼範例示範如何設定自訂 **ClientRequestId** 值附加 **OperationContext** 物件要求至儲存體服務。 它也會顯示如何擷取 **ServerRequestId** 回應訊息中的值。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>時間戳記

您也可以使用時間戳記找到相關的記錄項目，但是要注意任何可能存在用戶端與伺服器之間的時鐘誤差。 您應該依據用戶端上的時間戳記，搜尋前後誤差 15 分鐘之內相符的伺服器端記錄項目。 請記住，針對內含度量的 Blob 所產生的 Blob 中繼資料，代表儲存在 Blob 中的度量時間範圍，當您在同一分鐘或小時內擁有太多度量 Blob 時，這項資料相當有用。

## <a name="troubleshooting-guidance"></a>疑難排解指引

本小節將在您使用 Azure 儲存體服務時，協助您針對應用程式可能會碰到的一些常見問題進行診斷與疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

**疑難排解決策樹**

----------

您的問題是否與其中一項儲存體服務效能相關？

- [度量顯示高 AverageE2ELatency 與低 AverageServerLatency]
- [度量顯示低 AverageE2ELatency 與低 AverageServerLatency，但用戶端正經歷高延遲]
- [度量顯示高 AverageServerLatency]
- [佇列上的訊息在遞送期間出現非預期的延遲]

----------

您的問題是否與其中一項儲存體服務可用性相關？

- [度量顯示 PercentThrottlingError 增加]
- [度量顯示 PercentTimeoutError 增加]
- [度量顯示 PercentNetworkError 增加]

----------

您的用戶端應用程式是否收到來自儲存體服務的 HTTP 4XX (例如 404) 回應？

- [用戶端收到 HTTP 403 (禁止) 訊息]
- [用戶端收到 HTTP 404 (找不到) 訊息]
- [用戶端收到 HTTP 409 (衝突) 訊息]

----------

[度量顯示低 PercentSuccess，或是分析記錄項目內含具有 ClientOtherErrors 交易狀態的作業項目]

----------

[容量度量顯示非預期的儲存體容量使用增加]

----------

[附加大量 VHD 的虛擬機器，出現非預期的重新開機情況]

----------

[您的問題起因於使用儲存體模擬器進行開發或測試]

----------

[安裝 Azure SDK for .NET 時發生問題]

----------

[您的儲存體服務出現其他問題]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>度量顯示高 AverageE2ELatency 與低 AverageServerLatency

下的圖，從 [Azure 入口網站](portal.azure.com) 監視工具] 舉例說明其中 **AverageE2ELatency** 遠遠高 **AverageServerLatency**。

![][4]

請注意，儲存體服務只會計算度量 **AverageE2ELatency** 成功的要求，而且不同於 **AverageServerLatency**, ，包含用戶端來傳送資料與接收儲存體服務認可所需的時間。 因此，之間的差異 **AverageE2ELatency** 和 **AverageServerLatency** 可能是因為用戶端應用程式的速度太慢回應，或網路狀況所引起。

> [AZURE.NOTE] 您也可以檢視 **E2ELatency** 和 **ServerLatency** 中儲存體記錄的個別儲存體作業記錄資料。

#### 調查用戶端效能問題

用戶端回應速度較慢的可能原因包括可用的連線或執行緒數量有限或資源 (例如 CPU、記憶體或網路頻寬) 不足。 您可以試著將用戶端程式碼修改得更有效率 (例如對儲存體服務使用非同步呼叫) 或是使用較大型的虛擬機器 (核心數量增加，記憶體容量加大) 來解決這個問題。

對於資料表和佇列服務，Nagle 演算法也可能造成高 **AverageE2ELatency** 相較之下 **AverageServerLatency**: 如需詳細資訊，請參閱下列文章 <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">Nagle 的演算法並不適合用於小型要求</a> Microsoft Azure 儲存體團隊部落格上。 您可以使用停用程式碼中的 Nagle 演算法 **ServicePointManager** 類別 **System.Net** 命名空間。 由於這麼做會影響已經開啟的連線，因此在對應用程式裡的資料表或佇列服務進行任何呼叫之前，請先完成這個動作。 以下範例來自 **Application_Start** 背景工作角色中的方法。

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

您應該檢查用戶端記錄檔，以查看多少個要求送出您的用戶端應用程式，以及檢查一般的.NET 相關的效能瓶頸，您的用戶端，例如 CPU、.NET 記憶體回收、 網路使用率或記憶體中 (做為疑難排解.NET 用戶端應用程式的起始點，請參閱 <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">偵錯、 追蹤和程式碼剖析</a> 位於 MSDN)。

#### 調查網路延遲問題

網路所引起的端對端高延遲通常是暫時性狀況。 您可以使用 Wireshark 或 Microsoft Message Analyzer 之類的工具調查暫時性與永久性網路問題，例如遭到捨棄的封包。

如需有關使用 Wireshark 來為網路問題進行疑難排解的詳細資訊，請參閱 「[Appendix 2: Using Wireshark to capture network traffic]。 」

如需有關使用 Microsoft Message Analyzer 對網路問題進行疑難排解的詳細資訊，請參閱 「[Appendix 3: Using Microsoft Message Analyzer to capture network traffic]。 」

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>度量顯示低 AverageE2ELatency 與低 AverageServerLatency，但用戶端正經歷高延遲

在此案例中，最可能的原因就是通往儲存體服務的儲存體要求發生延遲。 您應該調查來自用戶端的要求為何無法抵達 Blob 服務。

用戶端延遲傳送要求的可能原因之一，是可用的連線或執行緒數量有限。 

您應該同時檢查用戶端是否執行多次的重試，並調查是否真是如此。 若要判斷用戶端是否正在執行多次的重試，您可以：

- 檢查 Storage Analytics 記錄檔。 如果發生多次重試，您會看到多個作業使用相同的用戶端要求 ID，但使用不同的伺服器要求 ID。
- 檢查用戶端記錄檔。 詳細資訊記錄會指出已發生的重試。
- 偵錯程式碼，並檢查內容的 **OperationContext** 與要求相關聯的物件。 如果有重試此作業， **RequestResults** 屬性會包含多個唯一的伺服器要求 Id。 您也可以檢查每個要求的開始和結束時間。 如需詳細資訊，請參閱下一節中的程式碼範例 [Server request ID]。 

如果用戶端裡沒有任何問題，請調查封包遺失之類的潛在網路問題。 您可以使用諸如 Wireshark 或 Microsoft Message Analyzer 之類的工具來調查網路問題。

如需有關使用 Wireshark 來為網路問題進行疑難排解的詳細資訊，請參閱 「[Appendix 2: Using Wireshark to capture network traffic]。 」

如需有關使用 Microsoft Message Analyzer 對網路問題進行疑難排解的詳細資訊，請參閱 「[Appendix 3: Using Microsoft Message Analyzer to capture network traffic]。 」

### <a name="metrics-show-high-AverageServerLatency"></a>度量顯示高 AverageServerLatency

高 **AverageServerLatency** blob 下載要求，您應該使用儲存體記錄，看看是否有重複出現的要求相同的 blob (或是 blob 集合)。 在 Blob 上傳要求中，您應該調查用戶端所使用的區塊大小 (例如，小於 64K 的區塊大小，可能導致額外的負荷，除非讀取的區塊流量也小於 64K)，以及是否有多個用戶端同時間上傳區塊給同一個 Blob。 您也應該檢查每分鐘度量是否超出要求數目突然暴增的每秒延展性目標: 另請參閱 「[Metrics show an increase in PercentTimeoutError]。 」

如果出現高 **AverageServerLatency** blob 下載要求時重複要求相同的 blob 或一組 blob，則您應考慮快取處理這些 blob，使用 Azure 快取或是 Azure 內容傳遞網路 (CDN)。 關於上傳要求，您可以使用較大的區塊大小來改善輸送量。 關於資料表查詢，您也可以對執行相同查詢操作，且資料不會經常變動的用戶端，實作用戶端快取處理。

高 **AverageServerLatency** 值也可以設計不良的資料表或查詢導致掃描作業，或是遵循附加/附加反向模式的徵兆。 請參閱 「[Metrics show an increase in PercentThrottlingError]「 如需詳細資訊。

> [AZURE.NOTE] 您可以找到完整的檢查清單包括要注意的其他問題: [Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)。

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>佇列上的訊息在遞送期間出現非預期的延遲

如果從應用程式新增訊息至佇列，到開始可從佇列讀取訊息之間的這段時間出現延遲，請採取下列步驟來診斷這個問題：

- 確認應用程式成功新增下列訊息至佇列。 應用程式不會重試的核取 **AddMessage** 數次成功之前的方法。 儲存體用戶端程式庫記錄會顯示儲存體作業的重複嘗試情況。
- 請確認負責將訊息新增至佇列的背景工作角色，以及負責從佇列讀取訊息的背景工作角色之間，沒有出現會讓人覺得處理出現延遲的時鐘誤差。
- 檢查負責從佇列讀取訊息的背景工作角色是否失敗。 如果佇列用戶端呼叫 **GetMessage** 方法，來回應通知訊息仍會隱藏在佇列中，直到 **invisibilityTimeout** 期限到期。 此時，訊息才會再次可供處理。
- 檢查佇列長度在一段時間之後是否又增加了。 當您沒有足夠的背景工作來處理其他背景工作放在佇列上的所有訊息時，就會出現這個情況。 您應該同時檢查度量，查看刪除要求是否失敗，以及訊息上是否有解除佇列計數，後者可能代表刪除訊息的嘗試一再失敗。
- 檢查儲存體記錄超出預期有任何佇列作業 **E2ELatency** 和 **ServerLatency** 一段較長的時間比平常的值。


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>度量顯示 PercentThrottlingError 增加

當超出儲存體服務的延展性目標時，會出現節流錯誤。 儲存體服務這麼做是為了確保沒有任何用戶端或是租用戶可犧牲其他服務來使用這項服務。 如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure 儲存體延展性和效能目標</a> 如需有關儲存體帳戶延展性目標，以及儲存體帳戶內的資料分割的效能目標。

如果 **PercentThrottlingError** 度量顯示失敗且出現節流錯誤的要求百分比增加，您應該調查下列兩種情況的其中一個:

- [PercentThrottlingError 中的暫時性增加]
- [PercentThrottlingError 錯誤中的永久性增加]

增加 **PercentThrottlingError** 通常發生於相同的儲存體要求數量增加，或是當您一開始進行負載測試您的應用程式。 當儲存體作業出現「503 伺服器忙碌」或是「500 作業逾時」狀態訊息時，用戶端也會明顯出現這個情況。

#### <a name="transient-increase-in-PercentThrottlingError"></a>PercentThrottlingError 中的暫時性增加

如果您看到的值突然暴增 **PercentThrottlingError** 與應用程式的大量活動期間同時發生，您應該在您的用戶端實作指數型 (非線性) 撤退策略進行重試: 這會減少磁碟分割上的即時負載，協助應用程式緩和突然增加的流量。 如需有關如何使用「儲存體用戶端程式庫」實作重試原則的詳細資訊，請參閱 MSDN 上的 <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間</a>。

> [AZURE.NOTE] 您也可能會看到的值突然暴增 **PercentThrottlingError** 的應用程式的大量活動期間不一致: 最可能的原因是儲存體服務移動資料分割以改善負載平衡。

#### <a name="permanent-increase-in-PercentThrottlingError"></a>PercentThrottlingError 錯誤中的永久性增加

如果您看到值持續偏高 **PercentThrottlingError** 攀升交易量，或執行您的初始載入時測試您的應用程式，則您需要評估您的應用程式使用儲存體資料分割的方式，及其是否接近儲存體帳戶的延展性目標。 舉例來說，當您發現某個佇列 (作為單一資料分割納入計算) 出現節流錯誤，那麼您應該考慮採用額外的佇列，將所有交易分散到多個分割。 如果您發現某個資料表出現節流錯誤，您需要考慮採用不同的資料分割結構描述，並使用範圍較大的資料分割索引鍵，將所有交易分散到多個資料分割。 此問題的常見原因出在結尾附加/開頭附加的反模式，在此模式下您可以選取日期作為資料分割索引鍵，然後特定日期的所有資料就會寫入單一資料分割中：在負載情況下，這項作業會導致寫入瓶頸。 您應該考慮採用不同的資料分割設計，或是評估使用 Blob 儲存體是否會更好。 您應該同時檢查節流錯誤是否因為大量湧入流量而引起，並調查如何緩和要求模式。

如果您將所有交易分散到多個資料分割，您必須同時注意儲存體帳戶所設定的延展性限制。 舉例來說，當您使用 10 個佇列，而每個佇列每秒鐘最多可處理 2,000 個 1KB 大小的訊息時，儲存體帳戶的總體限制將為每秒鐘 20,000 則訊息。 當您每秒鐘需要處理超過 20,000 個實體時，請考慮使用多個儲存體帳戶。 請同時注意，您的要求與實體大小會對儲存體服務何時節流您的用戶端產生影響：如果您有較大型的要求與實體，則會較優先進行節流。

當查詢設計不敷使用時，也會導致資料表分割到達延展性限制。 舉例來說，當查詢中的篩選器只會選取資料分割中實體的 1%，但卻會掃描資料分割中所有實體時，需要存取每個實體。 每個實體讀取動作都會記入該資料分割的總交易數，因此，您可以輕鬆地達到延展性目標。

> [AZURE.NOTE] 效能測試作業應該會顯示您的應用程式中任何沒有效率的查詢設計。

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>度量顯示 PercentTimeoutError 增加

您的度量顯示增加 **PercentTimeoutError** 其中一個儲存體服務。 同時，用戶端從儲存體作業中收到大量的「500 作業逾時」HTTP 狀態訊息。

> [AZURE.NOTE] 您可能會看到逾時錯誤暫時儲存體服務藉由將資料分割移至新的伺服器負載平衡。

 **PercentTimeoutError** 度量是下列度量的彙總: **ClientTimeoutError**, ，**AnonymousClientTimeoutError**, ，**SASClientTimeoutError**, ，**ServerTimeoutError**, ，**AnonymousServerTimeoutError**, ，和 **SASServerTimeoutError**。

伺服器逾時是因為伺服器上的錯誤引起。 用戶端逾時是因為伺服器上的某項作業超出用戶端; 指定的逾時例如，使用儲存體用戶端程式庫的用戶端可以透過設定作業逾時 **Queuerequestoptions** 屬性 **QueueRequestOptions** 類別。

伺服器逾時情況代表儲存體服務發生問題，需要進一步調查原因。 您可以使用度量，查看是否到達服務的延展性限制，並找出可能引起此問題的任何流量暴增情況。 如果此問題是間歇性發生，可能是因為服務中的負載平衡活動所引起。 如果此問題持續發生，而且不是因為應用程式到達服務的延展性限制引起，則請提出支援問題。 有關用戶端逾時，您必須判斷用戶端裡是否為逾時設定了適當的值，然後變更用戶端裡設定的逾時值或是調查如何改善儲存體服務裡的作業效能，例如，您可以最佳化資料表查詢或是縮減訊息大小。

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>度量顯示 PercentNetworkError 增加

您的度量顯示增加 **PercentNetworkError** 其中一個儲存體服務。  **PercentNetworkError** 度量是下列度量的彙總: **NetworkError**, ，**AnonymousNetworkError**, ，和 **SASNetworkError**。 當儲存體服務在用戶端進行儲存體要求時偵測到網路錯誤，就會發生這類情況。

此錯誤最常見的原因，就是用戶端在儲存體服務逾時之前就中斷連線。 您應該調查用戶端裡的程式碼，了解用戶端何時及為何與儲存體服務中斷連線。 您還可以使用 Wireshark、Microsoft Message Analyzer 或是 Tcping 調查來自用戶端的網路連線問題。 這些工具所述 [Appendices]。

### <a name="the-client-is-receiving-403-messages"></a>用戶端收到 HTTP 403 (禁止) 訊息

如果您的用戶端應用程式擲回 HTTP 403 (禁止) 錯誤，則可能是因為用戶端在傳送儲存體要求時使用過期的共用存取簽章 (SAS) (但也可能是時鐘誤差、無效的金鑰與空白標頭引起)。 如果原因出在 SAS 金鑰過期，那麼您將無法在伺服器端的儲存體記錄資料中看到任何項目。 下列資料表以儲存體用戶端程式庫所產生的用戶端記錄為例，說明此問題的原因：

來源|詳細程度|詳細程度|用戶端要求 ID|作業內容
---|---|---|---|---
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-…|從主要位置開始作業 (依據位置模式 PrimaryOnly)。
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab -…|Starting synchronous request to https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr=c&amp;si=mypolicy&amp;sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14.
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab -…|等候回應。
Microsoft.WindowsAzure.Storage|警告|2|85d077ab -…|等候回應時擲回例外狀況：遠端伺服器傳回錯誤：(403) 禁止...
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab -…|收到回應。 狀態碼 = 403，要求 ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d，Content-MD5 =，ETag = .
Microsoft.WindowsAzure.Storage|警告|2|85d077ab -…|作業期間擲回例外狀況：遠端伺服器傳回錯誤：(403) 禁止...
Microsoft.WindowsAzure.Storage|資訊|3 |85d077ab -…|檢查是否應該重試作業。 重試計數 = 0，HTTP 狀態碼 = 403，例外狀況 = 遠端伺服器傳回錯誤：(403) 禁止..
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab -…|以下位置已經設為「主要」(依據位置模式)。
Microsoft.WindowsAzure.Storage|錯誤|1|85d077ab -…|重試原則不允許重試。 失敗時遠端伺服器傳回錯誤：(403) 禁止。

在此案例中，您應該調查 SAS 權杖為何在用戶端將權杖傳送給伺服器之前到期：

- 一般來說，當您為用戶端建立要使用的 SAS，不應該立即設定開始時間。 如果使用目前時間來產生 SAS 的主機，以及儲存體服務之間出現些微的時鐘誤差，則儲存體服務有可能收到尚未生效的 SAS。
- 您不應該設定極短暫的 SAS 到期時間。 再說一次，產生 SAS 的主機，以及儲存體服務之間出現些微的時鐘誤差時，會導致 SAS 明顯比預期時間早到期。
- SAS 金鑰中的版本參數 (例如 **sv = 2015年-04-05**) 符合您所使用的儲存體用戶端程式庫版本? 我們建議您一律使用最新版的 [儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/)。 如需有關 SAS 權杖版本設定及用戶端程式庫版本相依性的詳細資訊，請參閱 [Microsoft Azure 儲存體的新功能](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx)。
- 如果您重新產生儲存體存取金鑰，會讓任何現有的 SAS 權杖失效。 如果您產生的 SAS 權杖，內含很長的到期時間以便用戶端應用程式快取處理，則可能會出現問題。

如果您是使用儲存體用戶端程式庫來產生 SAS 權杖，則您可以輕易地建立有效的權杖。 不過，如果您使用儲存體 REST API，並手動建構 SAS 權杖您應該仔細閱讀本主題 <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">使用共用存取簽章來委派存取權</a> (在 MSDN 上)。

### <a name="the-client-is-receiving-404-messages"></a>用戶端收到 HTTP 404 (找不到) 訊息
當用戶端應用程式接收來自伺服器的 HTTP 404 (找不到) 訊息時，表示用戶端嘗試使用的物件 (例如，實體、資料表、Blob、容器或佇列) 並不存在儲存體服務中。 這種情況有數種可能的原因，例如：

- [用戶端或其他程序先前刪除了該物件]
- [共用存取簽章 (SAS) 授權問題]
- [用戶端 JavaScript 程式碼沒有存取該物件的權限]
- [網路失敗]

#### <a name="client-previously-deleted-the-object"></a>用戶端或其他程序先前刪除了該物件
在用戶端嘗試讀取、更新或是刪除儲存體服務裡的資料時，通常很容易在伺服器端記錄中識別先前是哪項作業從儲存體服務中刪除了有問題的物件。 記錄資料通常會顯示另一位使用者或是程序刪除了該物件。 在伺服器端的儲存體記錄中，operation-type 物件與 requested-object-key 資料欄會顯示用戶端刪除物件的時間。

在用戶端嘗試插入物件的案例中，由於用戶端正在建立新的物件，因此可能不容易馬上了解為何這個情況會導致 HTTP 404 (找不到) 回應。 不過，如果用戶端正在建立 Blob，那麼它必定能夠找到 Blob 容器；如果用戶端正在建立訊息，則它必定能夠找到佇列；而且如果用戶端正在新增資料列，則它必定能夠找到資料表。

您可以使用儲存體用戶端程式庫裡的用戶端記錄，更深入地了解用戶端何時將特定要求傳送至儲存體服務。

下列由儲存體用戶端程式庫所產生的用戶端記錄，說明了當用戶端找不到 Blob 所建立的容器時的問題。 此記錄內含下列儲存體作業的詳細資料：

要求 ID|作業
---|---
07b26a5d-...|**DeleteIfExists** 方法用於刪除 blob 容器。 請注意，這項作業包括 **HEAD** 要求，以檢查容器是否存在。
e2d06d78…|**CreateIfNotExists** 方法來建立 blob 容器。 請注意，這項作業包括 **HEAD** 要求，可檢查容器是否存在。  **HEAD** 傳回 404 訊息，但是仍會繼續。
de8b1c3c-...|**UploadFromStream** 方法來建立 blob。  **放** 要求失敗，並顯示 404 訊息

記錄項目：

要求 ID |  作業內容
---|---
07b26a5d-...|開始將要求同步至 https://domemaildist.blob.core.windows.net/azuremmblobcontainer。
07b26a5d-...|StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|等候回應。
07b26a5d-... | 收到回應。 狀態碼 = 200，要求 ID = eeead849...Content-md5 =，ETag = & q u o t; 0x8D14D2DC63D059B & q u o t;。
07b26a5d-... | 回應標頭已成功處理完畢，並繼續剩下的作業。
07b26a5d-... | 正在下載回應內文。
07b26a5d-... | 作業順利完成。
07b26a5d-... | 開始將要求同步至 https://domemaildist.blob.core.windows.net/azuremmblobcontainer。
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue，03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container。
07b26a5d-... | 等候回應。
07b26a5d-... | 收到回應。 狀態碼 = 202，要求 ID = 6ab2a4cf-...，Content-MD5 = ，ETag = .
07b26a5d-... | 回應標頭已成功處理完畢，並繼續剩下的作業。
07b26a5d-... | 正在下載回應內文。
07b26a5d-... | 作業順利完成。
e2d06d78-... | 開始將要求同步至 https://domemaildist.blob.core.windows.net/azuremmblobcontainer。</td>
e2d06d78-... | StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| 等候回應。
de8b1c3c-... | 開始將要求同步至 https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt。
de8b1c3c-... |  StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | 正在準備寫入要求資料。
e2d06d78-... | 等候回應時擲回例外狀況：遠端伺服器傳回錯誤：(404) 找不到..
e2d06d78-... | 收到回應。 狀態碼 = 404，要求 ID = 353ae3bc-...，Content-MD5 = ，ETag = .
e2d06d78-... | 回應標頭已成功處理完畢，並繼續剩下的作業。
e2d06d78-... | 正在下載回應內文。
e2d06d78-... | 作業順利完成。
e2d06d78-... | 開始將要求同步至 https://domemaildist.blob.core.windows.net/azuremmblobcontainer。
e2d06d78-...|StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | 等候回應。
de8b1c3c-... | 正在寫入要求資料。
de8b1c3c-... | 等候回應。
e2d06d78-... | 等候回應時擲回例外狀況：遠端伺服器傳回錯誤：(409) 衝突..
e2d06d78-... | 收到回應。 狀態碼 = 409，要求 ID = c27da20e-...，Content-MD5 = ，ETag = .
e2d06d78-... | 正在下載錯誤回應內文。
de8b1c3c-... | 等候回應時擲回例外狀況：遠端伺服器傳回錯誤：(404) 找不到..
de8b1c3c-... | 收到回應。 狀態碼 = 404，回應 ID = 0eaeab3e-...，Content-MD5 = ，ETag = .
de8b1c3c-...| 作業期間擲回例外狀況：遠端伺服器傳回錯誤：(404) 找不到..
de8b1c3c-... | 重試原則不允許重試。 失敗時遠端伺服器傳回錯誤：(404) 找不到..
e2d06d78-... | 重試原則不允許重試。 失敗時遠端伺服器傳回錯誤：(409) 衝突..

在此範例中，記錄顯示用戶端會要求從 **CreateIfNotExists** 來自要求的方法 (...要求 id e2d06d78) **UploadFromStream** 方法 (de8b1c3c-...)，因為用戶端應用程式會叫用這些方法以非同步的方式，是否發生此情形。 您應該修改用戶端裡的非同步程式碼，確保該程式碼在嘗試將任何資料上傳至容器的 Blob 之前，先建立該容器。 理想的情況是，您應該事先建立所有容器。

#### <a name="SAS-authorization-issue"></a>共用存取簽章 (SAS) 授權問題

如果用戶端應用程式嘗試使用的 SAS 金鑰並未包含作業的必要權限，則儲存體服務會將 HTTP 404 (找不到) 訊息傳回給用戶端。 在此同時，也會看到非零值 **SASAuthorizationError** 度量中。

下列資料表顯示來自儲存體記錄檔案的伺服器端記錄訊息範例：

<table>
  <tr>
    <td>要求開始時間</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>作業類型</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>要求狀態</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>HTTP 狀態碼</td>
    <td>404</td>
  </tr>
  <tr>
    <td>驗證類型</td>
    <td>Sas</td>
  </tr>
  <tr>
    <td>服務類型</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>要求 URL</td>
    <td>
    https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp;sr=c&amp;amp;si=mypolicy&amp;amp;sig=XXXXX&amp;amp;api-version=2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>要求 ID 標頭</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>用戶端要求 ID</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

您應該調查為何您的用戶端應用程式會嘗試執行無執行權限的作業。

#### <a name="JavaScript-code-does-not-have-permission"></a>用戶端 JavaScript 程式碼沒有存取該物件的權限

如果您使用的是 JavaScript 用戶端，而儲存體服務傳回 HTTP 404 訊息，則請在瀏覽器中檢查下列 JavaScript 錯誤：

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] 您可以使用 Internet Explorer 中的 F12 開發人員工具，來追蹤用戶端 JavaScript 問題進行疑難排解時，瀏覽器與儲存體服務之間交換的訊息。

由於網頁瀏覽器中執行，就會發生這些錯誤 <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">相同原始原則</a> 來自卻防止網頁呼叫不同網域的網域] 頁面 API 的安全性限制。

若要解決這個 JavaScript 問題，您可以針對用戶端存取的儲存體服務，設定跨原始來源資源分享 (CORS)。 如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">跨原始來源資源分享 (CORS) 支援 Azure 儲存體服務</a> (在 MSDN 上)。

下例程式碼範例顯示如何設定您的 Blob 服務，以讓 JavaScript 在 Contoso 網域中執行，進而存取位於您的 Blob 儲存體服務中的 Blob：

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>網路失敗

在某些情況中，遺失網路封包可能導致儲存體服務將 HTTP 404 訊息傳回給用戶端。 舉例來說，當您的用戶端應用程式正在刪除資料表服務中的某個實體，您看到該用戶端正從資料表服務擲回一個回報「HTTP 404 (找不到)」狀態訊息的儲存體例外。 當您調查資料表儲存體服務中的資料表時，會看到該服務已依要求刪除該實體。

用戶端的例外狀況詳細資料包括資料表服務要求所指派的要求 id (7e84f12d...): 您可以使用這項資訊至伺服器端儲存體記錄中尋找要求詳細資料中搜尋 **要求 id 標頭** 記錄檔中的資料行。 您也可以使用度量，判定此類失敗情況何時發生，然後依據度量記錄此錯誤的時間搜尋記錄檔。 此記錄項目顯示刪除作業失敗，並顯示「HTTP (404) 用戶端其他錯誤」的狀態訊息。 相同的記錄項目也包含產生的用戶端的要求 id **用戶端要求 id** 資料行 (813ea74f …)。

伺服器端記錄檔也包含具有相同的另一個項目 **用戶端要求 id** 值 (813ea74f …) 順利完成刪除作業，在相同的實體與同一個用戶端。 此順利完成的刪除作業會在刪除要求失敗之前很快地發生。

此情況最有可能的原因出在，用戶端將要刪除實體的要求傳送給資料表服務而且刪除成功，但卻為收到來自伺服器的認可 (可能是因為暫時的網路問題)。 用戶端又自動重試作業 (使用相同 **用戶端要求 id**)，及這次重試失敗，因為該實體已經刪除。

如果這個問題經常發生，您應該調查為何用戶端無法收到來自資料表服務的認可。 如果此問題是間歇性發生，您應該捕捉「HTTP (404) 找不到」錯誤並記錄在用戶端裡，但同時允許用戶端繼續作業。

### <a name="the-client-is-receiving-409-messages"></a>用戶端收到 HTTP 409 (衝突) 訊息

下表顯示來自兩個用戶端作業的伺服器端記錄檔: **DeleteIfExists** 隨後緊接 **CreateIfNotExists** 使用相同的 blob 容器名稱。 請注意，每個用戶端作業會產生兩個要求傳送至伺服器時，會先 **GetContainerProperties** 要求，以檢查容器是否存在，後面接著 **DeleteContainer** 或 **CreateContainer** 要求。

Timestamp|作業|結果|容器名稱|用戶端要求 ID
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-…
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-…
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-…
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-…

用戶端應用程式中的程式碼會刪除，然後立即重新建立使用相同名稱的 blob 容器: **CreateIfNotExists** 方法 (用戶端要求 ID bc881924-…) 最終會失敗，HTTP 409 (衝突) 錯誤。 當用戶端刪除 Blob 容器、資料表或佇列時，需要等候簡短時間，才能使該名稱再度生效。

每當用戶端應用程式建立新的容器時，應該使用唯一的容器名稱 (如果經常出現「刪除」重新建立作業模式的話)。

### <a name="metrics-show-low-percent-success"></a>度量顯示低 PercentSuccess，或是分析記錄項目內含具有 ClientOtherErrors 交易狀態的作業項目

 **PercentSuccess** 度量會擷取的成功其 HTTP 狀態碼為基礎的作業百分比。 作業的狀態碼的 2XX 會計為成功，而帶有 3XX、 4XX 與 5XX 範圍中的狀態碼的作業會計算為失敗並會降低 **PercentSucess** 公制值。 在伺服器端儲存體記錄檔中，這些作業會記錄交易狀態為 **ClientOtherErrors**。

請務必注意，這些作業已經成功完成，因此不會影響到例如可用性的其他度量。 以下作業範例顯示作業已成功執行，但卻出現不成功的 HTTP 狀態碼：
- **ResourceNotFound** (找不到 404)，例如來自 GET 要求到不存在的 blob。
- **ResouceAlreadyExists** (衝突 409)，例如從 **CreateIfNotExist** 資源已經存在的作業。
- **ConditionNotMet** (未修改 304)，例如從條件式的作業，例如當用戶端傳送 **ETag** 值和 HTTP **If 無-比對** 要求影像，只有當它上次作業之後已更新的標頭。

您可以找到一份在頁面上的儲存體服務傳回的常見 REST API 錯誤代碼 <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">常見的 REST API 錯誤碼</a>.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>容量度量顯示非預期的儲存體容量使用增加


如果您發現儲存體帳戶裡突然出現非預期的容量改變，可以先察看可用性度量來調查其原因；舉例來說，刪除要求的失敗次數一旦增加，可能會造成您所使用的 Blob 儲存體數量增加，這是因為您認為應該可以騰出一些空間的特定應用程式清除作業沒有如預期發揮作用所致 (例如，因為用於騰出空間的 SAS 權杖已經過期)。

### <a name="you-are-experiencing-unexpected-reboots"></a>您遇到未預期的重新開機的 Azure 虛擬機器具有大量附加 Vhd

當 Azure 虛擬機器 (VM) 裡有大量的附加 VHD 位於同一個儲存體帳戶內時，您可能會超出個別儲存體帳戶的延展性目標，導致 VM 作業失敗。 您應該檢查儲存帳戶的分鐘數的度量資訊 (**TotalRequests**/**TotalIngress**/**TotalEgress**) 給超過儲存帳戶的延展性目標的特殊圖文集。 請參閱一節 「[Metrics show an increase in PercentThrottlingError]"適用於協助您判斷是否節流已發生您儲存的帳戶。

一般情況下，每個個別的輸入或輸出作業上 VHD，以從虛擬機器會轉譯為 **取得頁面上** 或 **放入頁面** 上基礎頁面 blob 的作業。 因此，您可以在環境中使用預估的 IOPS，依據應用程式的特定行為調節單一儲存體帳戶中應該具備的 VHD 數量。 我們不建議單一儲存體帳戶內具備超過 40 的磁碟。 如需儲存體帳戶目前延展性目標的詳細資訊，尤其是您所使用之儲存體帳戶類型的要求率總計和頻寬總計，請參閱 <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure 儲存體延展性和效能目標</a>。
如果您超過儲存體帳戶的延展性目標，則應該將 VHD 放在多個不同儲存體帳戶，以減少每個個別帳戶中的活動。

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>您的問題起因於使用儲存體模擬器進行開發或測試

通常您會在開發與測試期間使用儲存體模擬器，來迴避 Azure 儲存體帳戶的要求。 以下列出您在使用儲存體模擬器時，常見的問題：

- [儲存體模擬器裡的功能 "X" 未能發揮作用]
- [使用儲存體模擬器時，發生「其中一個 HTTP 標頭的值格式不正確」錯誤]
- [執行儲存體模擬器需要系統管理員權限]

#### <a name="feature-X-is-not-working"></a>儲存體模擬器裡的功能 "X" 未能發揮作用

儲存體模擬器並未支援所有的 Azure 儲存體服務，例如檔案服務。 如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">儲存體模擬器和 Azure 儲存體服務之間的差異</a> (在 MSDN 上)。

如需了解儲存體模擬器不支援哪些功能，請使用雲端裡的 Azure 儲存體服務。

#### <a name="error-HTTP-header-not-correct-format"></a>使用儲存體模擬器時，發生「其中一個 HTTP 標頭的值格式不正確」錯誤

您正在測試您的應用程式使用儲存用戶端程式庫，對本機存放裝置模擬器] 和 [方法呼叫，例如 **CreateIfNotExists** 失敗，錯誤訊息"的其中一個 HTTP 標頭的值不正確的格式 」。 這表示您所使用的儲存體模擬器版本，並不支援您所使用的儲存體用戶端程式庫版本。 儲存用戶端程式庫加入標頭 **x ms 版本** 到它所進行的所有要求。 如果儲存模擬器無法辨識中的值 **x ms 版本** 標頭，它會拒絕要求。

可用於儲存文件庫的用戶端記錄的值，請參閱 **x ms 版本標頭** 傳送。 您也可以查看的值 **x ms 版本標頭** 如果您使用 Fiddler 來追蹤從用戶端應用程式的要求。

此情況通常在您安裝並使用最新版的儲存體用戶端程式庫，但卻沒有一併更新儲存體模擬器時發生。 您應該安裝最新版的儲存體模擬器，或是使用雲端儲存體而非模擬器進行開發與測試。

#### <a name="storage-emulator-requires-administrative-privileges"></a>執行儲存體模擬器需要系統管理員權限

當您執行儲存體模擬器時，系統會提示您輸入系統管理員認證。 這種情況只會在您首次初始化儲存體模擬器時才會發生。 一旦儲存體模擬器初始化完畢後，您就不需要具備系統管理員權限才能再次執行。

如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">使用命令列工具初始化儲存體模擬器</a> (您也可以初始化在 Visual Studio 中，也會需要系統管理權限的存放裝置模擬器) 的 MSDN。

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>安裝 Azure SDK for .NET 時發生問題

當您嘗試安裝 SDK 時，系統在您的本機電腦上安裝儲存體模擬器時失敗。 安裝記錄內含下列其中一則訊息：

- CAQuietExec: 錯誤: 無法存取 SQL 執行個體
- CAQuietExec: 錯誤: 無法建立資料庫

此問題原因出在現有的 LocalDB 安裝。 根據預設，儲存體模擬器在模擬 Azure 儲存體服務時，使用 LocalDB 來永久儲存資料。 您可以在嘗試安裝 SDK 之前，先於命令提示字元中執行下列命令以重設您的 LocalDB 執行個體。

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

 **刪除** 指令會移除先前安裝的存放裝置模擬器中的任何舊的資料庫檔案。

### <a name="you-have-a-different-issue-with-a-storage-service"></a>您的儲存體服務出現其他問題

如果上述的疑難排解章節沒有提到您所碰到的儲存體服務相關問題，請採用下列方法來診斷您的問題，並進行疑難排解。

- 檢查您的度量，了解其中是否出現不同於預期的基準行為。 您可以從度量中，判斷該問題是暫時性還是永久性，以及該問題影響了哪些儲存體作業。
- 您可以使用度量資訊，協助您搜尋伺服器端記錄資料以了解所發生的任何錯誤之詳細資訊。 此項資訊可協助您進行疑難排解並解決問題。
- 如果伺服器端記錄裡的資訊不足以為問題順利進行疑難排解，您可以使用儲存體用戶端程式庫端的記錄來調查用戶端應用程式行為，並使用像是 Fiddler、Wireshark 與 Microsoft Message Analyzer 之類的工具來調查您的網路。

如需有關如何使用 Fiddler 的詳細資訊，請參閱 「[Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]。"

如需有關如何使用 Wireshark 的詳細資訊，請參閱 「[Appendix 2: Using Wireshark to capture network traffic]。"

如需有關如何使用 Microsoft 訊息分析器的詳細資訊，請參閱 「[Appendix 3: Using Microsoft Message Analyzer to capture network traffic]。"

## <a name="appendices"></a>附錄

以下附錄說明當您為 Azure 儲存體 (與其他服務) 的相關問題進行診斷與疑難排解時，可能會有幫助的多項工具。 這些工具並非 Azure 儲存體內建的工具，有些則是協力廠商的產品。 因此，以下附錄所討論的工具，並未包含在您就 Microsoft Azure 或 Azure 儲存體所簽訂的任何支援合約內容裡，因此在您評估期間，請檢視這些工具的供應商所提供的授權與支援選項。

### <a name="appendix-1"></a>附錄 1：使用 Fiddler 擷取 HTTP 與 HTTPS 流量

在您分析用戶端應用程式與您所使用的 Azure 儲存體服務之間的 HTTP 與 HTTPS 流量時，Fiddler 便能派上用場。 您可以下載從 Fiddler <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [AZURE.NOTE] Fiddler 可以解碼 HTTPS 流量;您應該仔細瞭解其中的原理，並瞭解的安全性含意的 Fiddler 文件]。

本附錄簡略地概述如何設定 Fiddler 來擷取安裝了 Fiddler 的本機電腦以及 Azure 儲存體服務之間的流量。

Fiddler 一經啟動後，就會開始擷取本機電腦上的 HTTP 與 HTTPS 流量。 以下提供您一些有用的命令，方便您控制 Fiddler：

- 停止與開始擷取流量。 在主功能表中，移至 **檔案** ，然後按一下 [ **擷取流量** 切換擷取開啟和關閉。
- 儲存擷取的流量資料。 在主功能表中，移至 **檔案**, ，按一下 [ **儲存**, ，然後按一下 [ **所有工作階段**: 這可讓您儲存工作階段的存檔中的流量。 您稍後可以重新載入工作階段封存以供分析，或是因應要求傳送給 Microsoft 支援服務中心。

若要限制的 Fiddler 所擷取的流量，您可以使用您在中設定的篩選器 **篩選條件** ] 索引標籤。 下圖顯示 [擷取傳送至的流量的篩選器 **contosoemaildist.table.core.windows.net** 儲存端點:

![][5]

### <a name="appendix-2"></a>附錄 2：使用 Wireshark 擷取網路流量

Wireshark 是一項網路通訊協定工具，能幫助您針對各式各樣的網路通訊協定檢視詳細的封包資訊。 您可以下載從 Wireshark <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

以下程序說明如何從安裝 Wireshark 的本機電腦，將詳細的流量封包資訊擷取到您的 Azure 儲存體帳戶裡的資料表服務中。

1.  在本機電腦上啟動 Wireshark。
2.  在 **開始** 區段中，選取 [區域網路介面] 或 [連線到網際網路的介面。
3.  按一下 [ **擷取選項**。
4.  新增篩選器來 **擷取篩選器** 文字方塊。 例如， **裝載 contosoemaildist.table.core.windows.net** 將會設定要擷取只攝影機或從資料表服務端點，在傳送的封包的 Wireshark **contosoemaildist** 存放的帳戶。 如需「擷取篩選器」的完整清單，請參閱 <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>。

    ![][6]

5.  按一下 [ **開始**。 現在當您於本機電腦上使用用戶端應用程式時，Wireshark 會開始擷取流入/流出資料表服務端點的所有封包。
6.  當您完成時，在主功能表，按一下 **擷取** ，然後 **停止**。
7.  若要將所擷取的資料儲存到 Wireshark，擷取的檔案，在主功能表上按一下 **檔案** ，然後 **儲存**。

WireShark 會反白顯示存在於任何錯誤 **packetlist** 視窗。 您也可以使用 **專家資訊** 視窗 (按一下 [ **分析**, ，然後 **專家資訊**) 以檢視錯誤和警告的摘要。

![][7]

您也可以選擇檢視 TCP 資料，如應用程式層所看到的是它的 TCP 資料上按一下滑鼠右鍵，然後選取 **遵循 TCP 資料流**。 當您不使用擷取篩選器而擷取到傾印時，這個方法會特別有用。 如需詳細資訊，請參閱<a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">這裡</a>。

![][8]

> [AZURE.NOTE] 如需有關如何使用 Wireshark 的詳細資訊，請參閱 <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Wireshark 使用者指南</a>。

### <a name="appendix-3"></a>附錄 3：使用 Microsoft Message Analyzer 擷取網路流量

您可以使用 Microsoft Message Analyzer，仿照 Fiddler 的方式擷取 HTTP 與 HTTPS 流量，並仿照 Wireshark 的方式擷取網路流量。

#### 使用 Microsoft Message Analyzer 設定 Web 追蹤工作階段

若要設定網路追蹤工作階段使用 Microsoft 訊息分析器中，執行 Microsoft 訊息分析器應用程式的 HTTP 和 HTTPS 流量，然後在 **檔案** ] 功能表中，按一下 [ **追蹤擷取/**。 在 [可用的追蹤案例清單中選取 **網頁 Proxy**。 然後在 **追蹤案例設定** 面板，在 **HostnameFilter** ] 文字方塊中，新增存放端點的名稱 (您可以查詢這些名稱在 [Azure 入口網站](portal.azure.com))。 例如，如果您的 Azure 存放帳戶的名稱是 **contosodata**, ，您應該加入下列項目來 **HostnameFilter** 文字方塊:

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] 空格字元區隔的主機名稱。

當您準備開始收集追蹤資料，請按一下 [ **啟動與** 按鈕。

如需有關 Microsoft 的訊息分析器 **網頁 Proxy** 追蹤，請參閱 <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF WebProxy 提供者</a> TechNet 上。

內建 **網頁 Proxy** Microsoft 訊息分析器中的追蹤以 Fiddler 為基礎; 它可以擷取用戶端 HTTPS 流量，並顯示未加密的 HTTPS 訊息。  **網頁 Proxy** 追蹤藉由設定讓它存取未加密的郵件的所有 HTTP 和 HTTPS 流量的本機 proxy 的運作方式。

#### 使用 Microsoft Message Analyzer 診斷網路問題

除了使用 Microsoft 訊息分析器 」 **網頁 Proxy** 追蹤以擷取用戶端應用程式與存放服務之間的 HTTP/HTTPs 流量的詳細資料，您也可以使用內建 **本機的連結層** 來擷取網路封包資訊的追蹤。 這麼做可讓您擷取到類似於使用 Wireshark 所擷取的資料，並診斷捨棄的封包之類的網路問題。

下列螢幕擷取畫面顯示範例 **本機的連結層** 追蹤一些 **資訊** 訊息在 **DiagnosisTypes** 資料行。 按一下圖示，在 **DiagnosisTypes** 欄會顯示訊息的詳細資料。 在以下範例中，由於伺服器並未收到來自用戶端的認可，因此重新傳送訊息 #305：

![][9]

當您在 Microsoft Message Analyzer 中建立追蹤工作階段時，可以指定篩選器來減少追蹤所產生的雜訊。 在 **擷取 / 追蹤** 可讓您定義追蹤，頁面上按一下 [ **設定** 連結旁邊 **Microsoft Windows NDIS-PacketCapture**。 下列螢幕擷取畫面顯示針對三個儲存體服務的 IP 位址進行 TCP 流量篩選的組態：

![][10]

如需有關 Microsoft 訊息分析器本機連結層追蹤的詳細資訊，請參閱 <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture 提供者</a> technet。

### <a name="appendix-4"></a>附錄 4：使用 Excel 檢視度量與記錄資料

許多工具都可讓您從 Azure 資料表儲存體中下載使用分隔格式的儲存體度量資料，方便您將資料載入 Excel 以供檢視及分析。 來自 Azure Blob 儲存體的儲存體記錄資料已經使用分隔格式，方便您直接載入 Excel。 不過，您必須新增適當的欄名，以在資訊 <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">儲存體分析記錄檔格式</a> 和 <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存體分析度量資料表結構描述</a>.

若要將您從 Blob 儲存體下載的儲存體記錄資料匯入 Excel：

- 在 **資料** ] 功能表中，按一下 [ **從文字**。
- 瀏覽至您想要檢視並按一下 [記錄檔 **匯入**。
- 在步驟 1 **匯入字串精靈**, ，請選取 **分隔符號**。

在步驟 1 **匯入字串精靈**, ，請選取 **分號** 做為唯一的分隔符號，然後選擇 [雙括號做為 **文字辨識符號**。 然後按一下 [ **完成** ，然後選擇 [將資料放在您的活頁簿中的位置。

### <a name="appendix-5"></a>附錄 5：使用 Application Insights for Visual Studio Team Services 監視

您也可以在效能與可用性監視作業中，使用 Visual Studio Online 的「Application Insights」功能。 這項工具可以：

- 確保您的 Web 服務可用且迅速回應。 無論您的應用程式是網站或是使用 Web 服務的裝置應用程式，此工具都可以每幾分鐘從全球各地測試您的 URL，然後讓您知道是否有問題。
- 快速診斷 Web 服務中的任何效能問題或例外。 了解 CPU 或其他資源是否過度使用，從例外中取得堆疊追蹤資料，並且輕鬆地搜尋記錄追蹤項目。 當應用程式的效能低於可接受的範圍，我們可以傳送一封電子郵件給您。 我們可以同時監視 .NET 與 Java Web 服務。

本文撰寫期間，Application Insights 已經進入預覽階段。 您可以找到更多的資訊，在 <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">應用程式洞悉 MSDN 上的 Visual Studio 小組服務</a>.


<!--Anchors-->
[Introduction]: #introduction
[How this guide is organized]: #how-this-guide-is-organized

[Monitoring your storage service]: #monitoring-your-storage-service
[Monitoring service health]: #monitoring-service-health
[Monitoring capacity]: #monitoring-capacity
[Monitoring availability]: #monitoring-availability
[Monitoring performance]: #monitoring-performance

[Diagnosing storage issues]: #diagnosing-storage-issues
[Service health issues]: #service-health-issues
[Performance issues]: #performance-issues
[Diagnosing errors]: #diagnosing-errors
[Storage emulator issues]: #storage-emulator-issues
[Storage logging tools]: #storage-logging-tools
[Using network logging tools]: #using-network-logging-tools

[End-to-end tracing]: #end-to-end-tracing
[Correlating log data]: #correlating-log-data
[Client request ID]: #client-request-id
[Server request ID]: #server-request-id
[Timestamps]: #timestamps

[Troubleshooting guidance]: #troubleshooting-guidance
[Metrics show high AverageE2ELatency and low AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrics show low AverageE2ELatency and low AverageServerLatency but the client is experiencing high latency]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrics show high AverageServerLatency]: #metrics-show-high-AverageServerLatency
[You are experiencing unexpected delays in message delivery on a queue]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrics show an increase in PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Transient increase in PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent increase in PercentThrottlingError error]: #permanent-increase-in-PercentThrottlingError
[Metrics show an increase in PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrics show an increase in PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[The client is receiving HTTP 403 (Forbidden) messages]: #the-client-is-receiving-403-messages
[The client is receiving HTTP 404 (Not found) messages]: #the-client-is-receiving-404-messages
[The client or another process previously deleted the object]: #client-previously-deleted-the-object
[A Shared Access Signature (SAS) authorization issue]: #SAS-authorization-issue
[Client-side JavaScript code does not have permission to access the object]: #JavaScript-code-does-not-have-permission
[Network failure]: #network-failure
[The client is receiving HTTP 409 (Conflict) messages]: #the-client-is-receiving-409-messages

[Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]: #metrics-show-low-percent-success
[Capacity metrics show an unexpected increase in storage capacity usage]: #capacity-metrics-show-an-unexpected-increase
[You are experiencing unexpected reboots of Virtual Machines that have a large number of attached VHDs]: #you-are-experiencing-unexpected-reboots
[Your issue arises from using the storage emulator for development or test]: #your-issue-arises-from-using-the-storage-emulator
[Feature "X" is not working in the storage emulator]: #feature-X-is-not-working
[Error "The value for one of the HTTP headers is not in the correct format" when using the storage emulator]: #error-HTTP-header-not-correct-format
[Running the storage emulator requires administrative privileges]: #storage-emulator-requires-administrative-privileges
[You are encountering problems installing the Azure SDK for .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[You have a different issue with a storage service]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]: #appendix-1
[Appendix 2: Using Wireshark to capture network traffic]: #appendix-2
[Appendix 3: Using Microsoft Message Analyzer to capture network traffic]: #appendix-3
[Appendix 4: Using Excel to view metrics and log data]: #appendix-4
[Appendix 5: Monitoring with Application Insights for Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png

