<properties 
    pageTitle="Application Insights 中的資料保留和儲存" 
    description="保留和隱私權原則聲明" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/18/2015" 
    ms.author="awills"/>

# Application Insights 中的資料收集、保留和儲存 

*Application Insights 目前僅供預覽。*

## 概觀

這篇文章回答有關所收集的資料問題  [Visual Studio Application Insights][start] 以及如何處理和儲存它。

Application Insights 是目前僅供預覽的 Azure 服務。 在預覽中，我們正在努力邁向保護您的資料中所述的原則時 [Azure 安全性、 隱私權和法務遵循白皮書](http://go.microsoft.com/fwlink/?linkid=392408)。


## 集合

#### Application Insights 如何收集資料？

Application Insights SDK 以及與應用程式合併的代理程式會將資料傳送至 Application Insights 服務。  服務會處理資料來提供報告、警示和其他功能。  這可能包括您選擇使用 API 所擷取的資料 (例如在屬性和自訂事件中)。

#### 可以擷取多少資料？ 

**每秒**︰ 每秒每個檢測金鑰最多 500 個資料點 (也就是每個應用程式)。 針對免費 [定價層][pricing], ，限制為 100 dp/s。

有三個個別計數的貯體：

* [TrackTrace 呼叫](app-insights-api-custom-events-metrics.md#track-trace) 和 [擷取記錄檔](app-insights-asp-net-trace-logs.md)
* [例外狀況](app-insights-api-custom-events-metrics.md#track-exception), ，受限於較低的限制為 50/s。
* 所有其他遙測 (頁面檢視、要求、相依性、度量、自訂事件以及 Web 測試結果)。

**每月**︰ 之間 5 和 15 萬個資料點，每個日曆月份中的取決於您 [定價方案](http://azure.microsoft.com/pricing/details/application-insights/)。 除了免費 [定價層][pricing], ，您可以購買額外的容量，如果達到最大限制。


A *資料點* 的遙測資料傳送至 Azure 入口網站應用程式相關的項目。 它可透過下列方式傳送：

* [SDK 模組](app-insights-configuration-with-applicationinsights-config.md) 會自動收集資料，例如報告要求或損毀，或測量的效能。
* [API](app-insights-api-custom-events-metrics.md) `Track...` 呼叫您已撰寫，例如 `TrackEvent` 或 `trackPageView`。
* [可用性 web 測試](app-insights-monitor-web-app-availability.md) 已設定。

遙測資料包括：

* 在每個資料列 [診斷搜尋](app-insights-diagnostic-search.md)
* 從哪一個圖表中的原始資料 [計量瀏覽器](app-insights-metrics-explorer.md) 彙總。 度量資料 (例如效能計數器資料) 通常不會顯示為度量瀏覽器中的個別點。
* 在每個 web 測試結果 [可用性](app-insights-monitor-web-app-availability.md) 報表。

使用者與工作階段計數不會併入定價用途的配額。

*如果應用程式超過每秒速率，會發生什麼事？*

* 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 接著，部分版本的 SDK 會嘗試每數分鐘重新傳送突波；其他像是 JavaScript SDK 則會放棄遭到拒絕的資料。

*如何知道應用程式正在傳送多少資料點？*

* 開啟 [設定/配額與定價] 查看 [資料數量] 圖表。
* 或在計量瀏覽器中加入新圖表並選取 **資料點數量** 做為其計量。 切換群組，並依 **資料型別**。

*如何減少我的應用程式傳送的資料量？*

* 使用 [取樣](app-insights-sampling.md)。 這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。 自 ASP.NET SDK 2.0.0-beta3 起，系統預設會啟用自適性取樣。
* [關閉遙測收集器](app-insights-configuration-with-applicationinsights-config.md) 您不需要。


#### 資料保留多久？ 

這取決於您 [定價方案](http://azure.microsoft.com/pricing/details/application-insights/)。

未處理的資料點 (即您可以在 [診斷搜尋] 中檢查的項目)：7 至 30 天。

彙總的資料 (即您在計量瀏覽器中看到的計數、平均和其他統計資料) 在 1 分鐘的資料粒度中保存 30 天，而 1 小時或 1 天 (視類型而定) 則保存至少 13 個月。

#### 不同類型的資料有哪些限制？

1.  您的應用程式具有最多 200 個唯一計量名稱和 200 個唯一屬性名稱。 度量包括透過 TrackMetric，以及其他資料類型，例如事件上的度量單位傳送的資料。  [計量和屬性名稱][api] 全域每個檢測金鑰不只限於資料類型。
2.  [屬性][apiproperties] 可以用於篩選和分組方式，只有在具有少於 100 個唯一值，每個屬性時。 唯一值超過 100 之後，屬性仍可用於搜尋與篩選，但無法用於篩選器。
3.  標準屬性，例如要求名稱和網頁 URL 會限制為每週 1000 個唯一值。 超過 1000 個唯一值之後，額外值都會標示為「其他值」。 原始值仍然可以用於全文檢索搜尋和篩選。


## Access

#### 誰可以查看資料？

您和您的小組成員 (如果您有組織帳戶) 可以看到資料。 

它可以由您和您的小組成員匯出，也可以複製到其他位置，並傳遞給其他人員。

#### Microsoft 如何處理我的應用程式傳送至 Application Insights 的資訊？

Microsoft 只會使用這項資料，以將服務提供給您。


## 位置

#### 資料存放在哪裡？ 

* 在美國。 

#### 可以儲存在其他某個地方 (例如歐洲) 嗎？ 

* 尚未提供。 

## 安全性 

#### 我的資料有多安全？ 

資料會儲存在 Microsoft Azure 伺服器中。 Azure 入口網站中的帳戶，帳戶限制中所述 [Azure 安全性、 隱私權和法務遵循文件](http://go.microsoft.com/fwlink/?linkid=392408)。 在 Visual Studio Team Services 入口網站中，帳戶 [Visual Studio Team Services 資料保護](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf) 套用文件。 

Microsoft 人員對您的資料存取會受到限制。 我們只有在獲得您的許可及為了支援您使用 Application Insights 而有必要時，才會存取您的資料。 

跨所有客戶應用程式 (例如資料速率和平均追蹤大小) 的彙總資料用於改善 Application Insights。

#### 其他人的遙測是否會干擾我的 Application Insights 資料？

他們可以使用檢測金鑰，將額外的遙測傳送至您的帳戶，而檢測金鑰位於您網頁的程式碼中。 有足夠的額外資料，您的計量將不會正確地代表您應用程式的效能和使用方式。

如果您與其他專案共用程式碼，請務必移除您的檢測金鑰。

## 加密

#### 資料在 Application Insights 伺服器中會加密嗎？ 

目前不在伺服器內。

所有資料在資料中心之間移動時會予以加密。

#### 將資料從我的應用程式傳輸到 Application Insights 伺服器時是否進行加密？

是，我們使用 https，將資料從幾乎所有 SDK (包括網頁伺服器、裝置和 HTTPS 網頁) 傳送至入口網站。 唯一的例外是從純 HTTP 網頁傳送的資料。

## 個人識別資訊

#### 個人識別資訊 (PII) 會傳送到 Application Insights 嗎？ 

是。 

一般指引為：

* 大部分的標準遙測 (即，在未撰寫任何程式碼的情況下所傳送的遙測) 都不包括明確的 PII。 不過，從事件集合推斷，即可識別個人。
* 例外狀況和追蹤訊息可能包含 PII
* 自訂遙測 (即，使用 API 以程式碼撰寫的呼叫 (例如 TrackEvent) 或記錄檔追蹤) 可以包含您選擇的任何資料。


這份文件結尾的資料表包含所收集資料的更詳細描述。



#### 我是否要負責遵守有關 PII 的法規？

是。 您必須負責確保資料的收集與使用符合法規和 Microsoft Online Services 條款。

您應該適當地通知您的客戶有關您應用程式所收集的資料和資料使用方式。

#### 我的使用者是否可以關閉 Application Insights？

無法直接進行。 我們不提供您的使用者可以操作來關閉 Application Insights 的參數。

不過，您可以在應用程式中實作這類功能。 所有 SDK 都包括關閉遙測收集的 API 設定。 

#### 我的應用程式不小心收集到機密資訊。 Application Insights 是否可以刪除此資料，不予保留？

Application Insights 不會篩選或刪除資料。 您應該適當地管理資料，並避免將這類資料傳送至 Application Insights。



## Application Insights 所傳送的資料

不同的平台會有不同的 SDK，而且有數個可安裝的元件  (請參閱 [Application Insights-開始][start]。)每個元件都會傳送不同的資料。

#### 不同情況下所傳送資料的類別

您的動作  | 收集的資料類別 (請參閱下一個資料表)
---|---
[將 Application Insights SDK 新增至 .NET Web 專案][greenbrown] | ServerContext<br/>推斷<br/>效能計數器<br/>要求<br/>**例外狀況**<br/>工作階段<br/>users
[在 IIS 上安裝狀態監視器][redfield]<br/>[將 AI 延伸模組加入至 Azure VM 或 Web 應用程式][azure]|相依項目<br/>ServerContext<br/>推斷<br/>效能計數器
[將 Application Insights SDK 新增至 Java Web 應用程式][java]|ServerContext<br/>推斷<br/>要求<br/>工作階段<br/>users
[將 JavaScript SDK 新增至網頁][client]|ClientContext <br/>推斷<br/>Page<br/>用戶端效能
[將 SDK 新增至 Windows 市集應用程式][windows]|DeviceContext<br/>使用者<br/>當機資料
[定義預設屬性][apiproperties]|**屬性** 上所有的標準和自訂事件
[呼叫 TrackMetric][api]|數字值<br/>**屬性**
[呼叫追蹤*][api]|事件名稱<br/>**屬性**
[呼叫 TrackException][api]|**例外狀況**<br/>堆疊傾印<br/>**屬性**
SDK 無法收集資料。 例如： <br/> -無法存取效能計數器<br/> -遙測初始設定式中的例外狀況 | SDK 診斷
 

如 [適用於其他平台 Sdk][platforms], ，請參閱其文件。



#### 所收集資料的類別

收集的資料類別 | 包含 (不是詳盡的清單) 
---|---
**屬性**|**任何資料 - 取決於您的程式碼**
DeviceContext |識別碼、IP、地區設定、裝置型號、網路、網路類型、OEM 名稱、螢幕解析度、角色執行個體、角色名稱、裝置類型
ClientContext |作業系統、地區設定、語言、網路、視窗解析度
工作階段 | 工作階段識別碼
ServerContext |電腦名稱、地區設定、作業系統、裝置、使用者工作階段、使用者內容、作業 
推斷 |從 IP 位址的地區位置、時間戳記、作業系統、瀏覽器
度量 | 計量名稱和值
事件 | 事件名稱和值
PageViews | URL 和頁面名稱或螢幕名稱
用戶端效能 | URL/頁面名稱、瀏覽器載入時間
要求 |URL、持續時間、回應碼
相依項目|類型 (SQL、HTTP、...)、連接字串或 URI、同步/非同步、持續時間、成功、SQL 陳述式 (含狀態監視器)
**例外狀況** | 型別， **訊息**, 、 呼叫堆疊、 來源檔案和行號、 執行緒識別碼
損毀 | 處理序識別碼、 父處理序識別碼、 損毀執行緒識別碼。應用程式修補程式、 識別碼、 組建; 例外狀況類型、 位址、 原因;模糊的符號和暫存器、 二進位開始和結束位址、 二進位檔名稱和路徑，cpu 類型
追蹤 | **訊息** 和嚴重性層級
效能計數器 | 處理器時間、可用記憶體、要求率、例外狀況率、處理序私用位元組、IO 率、要求持續期間、要求佇列長度
Availability | Web 測試回應程式碼、每個測試步驟的持續時間、測試名稱、時間戳記、成功、回應時間、測試位置
SDK 診斷 | 追蹤訊息或例外狀況 

您可以 [關閉某些資料藉由編輯 ApplicationInsights.config][config]


## 感謝

此產品包含由 MaxMind，可從建立的 GeoLite2 資料 [http://www.maxmind.com](http://www.maxmind.com)。

## <a name="video"></a>影片

#### 簡介

> [AZURE.VIDEO application-insights-introduction]

#### 開始使用

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

