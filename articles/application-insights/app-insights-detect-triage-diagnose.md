<properties
    pageTitle="偵測、分級、診斷"
    description="分析損毀並偵測及診斷效能問題，在您的應用程式"
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="11/06/2015"
    ms.author="awills"/>

# 使用 Application Insights 刪除、分級和診斷

*Application Insights 目前僅供預覽。*


Application Insights 幫助您瞭解您的 app 在作用中時如何執行和使用。 當發生問題時，它可讓您了解、協助您評估影響，以及協助您判斷原因。

以下是某個開發 Web 應用程式的小組的敘述：

* *「 幾天前，我們部署了次要的 hotfix。 我們沒有執行廣泛測試階段，但很不幸地，有些未預期的變更被合併到裝載中，造成前端與後端之間的不相容。 伺服器例外狀況隨即湧現，警報發出，我們便知道了這個情況。 在按幾下 Application Insights 入口網站之後，我們從例外狀況堆疊取得了足夠的資訊，而得以縮小問題。 我們立即復原並將損害降至最低。 Application Insights 使得這個部分的開發作業週期非常輕鬆且可行。 」*

我們來看看一般的 Web 開發小組如何使用 Application Insights 來監視效能。 我們會跟隨 Fabrikam 銀行的小組，開發線上銀行系統 (OBS)。

![銀行網站範例](./media/app-insights-detect-triage-diagnose/03-bank.png)

小組運作的循環如下：

![DevOps 循環](./media/app-insights-detect-triage-diagnose/00-devcycle.png)

送入其開發待處理項目 (工作清單) 的需求。 小組努力工作，經常交付可行的軟體 - 通常是對現有的應用程式進行改進和延伸。 上線的 app 則會經常更新新功能。 雖然 app 已經上線，但小組會透過 Application Insights 的協助監視其效能和使用情況。 這項分析會餵送回其開發待處理項目。

小組使用 Application Insights 密切監視上線 Web 應用程式的下列項目：
* 效能。 他們想要了解回應時間如何隨著要求計數變化；使用多少 CPU、網路、磁碟和其他資源；以及瓶頸所在。
* 失敗。 如果有例外狀況或失敗的要求，或如果效能計數器超出其舒適範圍，小組必須快速知道以便採取動作。
* 使用狀況。 當發行新功能時，小組想要知道其使用程度，以及使用者在使用上是否有任何問題。



接下來討論循環的回饋部分：

![偵測-分級-診斷](./media/app-insights-detect-triage-diagnose/01-pipe1.png)



## 偵測可用性不佳


Marcela Markova 是 OBS 小組的測試專員，主導線上效能方面的監視。 她設定數個 [web 測試][availability]:

* 應用程式中，主要登陸頁面的單一 URL 測試 http://fabrikambank.com/onlinebanking/。 她設定 HTTP 代碼 200 與文字「歡迎使用！」的準則。 如果此測試失敗，表示網路或伺服器發生嚴重錯誤，或可能有部署問題。 （或是有人變更歡迎使用 ！ 訊息不讓我們知道頁面）


* 更深入的多步驟測試將會記錄並取得目前帳戶清單，檢查每個頁面上的一些重要詳細資料。 此測試會驗證對帳戶資料庫的連結運作中。 她使用虛構的客戶識別碼：其中少數幾個保留供測試目的。


透過設定這些測試，Marcela 能確信若要任何中斷情況，小組將快速知道。  


失敗在 Web 測試圖表上會以紅點顯示：

![顯示對前一個期間執行的 Web 測試](./media/app-insights-detect-triage-diagnose/04-webtests.png)


但更重要的是，任何失敗的相關警示將以電子郵件方式寄送給開發小組。 以該方式，他們幾乎可在所有客戶之前便得知該情況。


## 監視效能度量


在 Application Insights 中的 [概觀] 頁面上沒有一個圖表會顯示各種 [金鑰度量][perf]。

![各種度量](./media/app-insights-detect-triage-diagnose/05-perfMetrics.png)

瀏覽器頁面載入時間是從網頁直接傳送的遙測所衍生。 伺服器回應時間、伺服器要求計數和失敗的要求計數，都是在 Web 伺服器中測量，然後從該處傳送到 Application Insights。


失敗的要求計數指出使用者看到錯誤的情況，通常是在程式碼中擲出例外狀況之後。 也許他們看到訊息指出「很抱歉，我們目前無法更新您的詳細資料」，或是在最遭的情況下，出自於 Web 伺服器，在使用者畫面上出現堆疊傾印。


Marcela 喜歡不時查看這些圖表。 沒有出現失敗的要求讓人開心，不過當她變更圖表來涵蓋過去一週的範圍時，偶爾會出現一些失敗。 這在忙碌的伺服器是可接受的程度。  但如果某些失敗，或是其他一些度量 (例如伺服器回應時間) 突然出現尖峰，Marcela 會想要立即得知細節。 它可能表示程式碼發行所造成而未在預料中的問題，或是相依性 (例如資料庫) 發生失敗，或也許是對要求的高負載不正常的反應。

#### Alerts

因此她設定兩個 [警示][metrics]︰ 一個針對回應時間大於一般閾值，而另一個針對大於目前背景的失敗要求的速率。


結合可用性警示，這些警示讓她能確信只要發生不尋常的問題，她便會得知。  


也可以對各種其他度量設定警示。 例如，您可以在例外狀況計數變高或可用記憶體變低，或用戶端要求中有尖峰時收到電子郵件。



![加入警示分頁](./media/app-insights-detect-triage-diagnose/07-alerts.png)




## 偵測例外狀況


利用最少的安裝程式， [例外狀況](app-insights-asp-net-exceptions.md) 自動報告至 Application Insights。 它們可以也會明確擷取呼叫 [trackexception （)](app-insights-api-custom-events-metrics.md#track-exception) 插入程式碼 ︰  

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


Fabrikam 銀行小組制定出一律在發生例外狀況時傳送遙測的作法，除非有明顯的恢復。  

事實上，其策略甚至比此更為廣泛：他們會在每個客戶對要做的事感到挫折時便傳送遙測，而不論是否與程式碼中的例外狀況對應。 例如，如果外部的銀行內傳送系統因為某此作業原因 (不是客戶的錯誤) 而傳回「無法完成此交易」訊息，他們會追蹤該事件。

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

TrackException 用來報告例外狀況，因為它會傳送堆疊的副本；TrackEvent 用來報告其他事件。 您可以在診斷中附加可能有幫助的任何屬性。

例外狀況和事件會顯示在 [診斷搜尋][diagnostic] 刀鋒視窗。 您可以深入探索，以查看額外屬性和堆疊追蹤。

![在「診斷搜尋」中，請使用篩選器來顯示特定類型的資料](./media/app-insights-detect-triage-diagnose/appinsights-333facets.png)

## 監視使用者活動

如果回應時間一直都不錯，只是有一些例外狀況，開發團隊可以思考如何改善使用者體驗，以及如何鼓勵更多使用者來達成想要的目標。


例如，許多使用者在瀏覽網站時都會發生一個清楚的「漏斗」歷程：許多客戶查看不同類型的貸款利率；其中一些客戶填寫報價單；而在獲得報價的客戶中，只有少數會繼續且獲得貸款。

![](./media/app-insights-detect-triage-diagnose/12-funnel.png)

透過找出最多客戶放棄的位置，企業可以思考如何讓更多使用者通過漏斗。 在某些情況下，可能是使用者體驗 (UX) 失敗 - 例如，很難找到 [下一步] 按鈕，或者指示不太明顯。 更有可能是因為重要的商業理由放棄：可能是貸款利率太高。

無論任何原因，資料都可協助團隊了解使用者在做什麼。 此時可以插入更多追蹤呼叫，了解更多細節。 TrackEvent() 可以用來計算任何使用者動作，小至個別的按鈕點擊，大到如付清貸款等重要成果。

團隊一直都有使用者活動的相關資訊。 只是現在，每當設計出一個新功能時，就要思考如何取得其使用方式的意見反應。 團隊如果從一開始就在功能中設計追蹤呼叫， 就可以使用意見反應在每個開發週期中改進功能。


## 主動監視  


Marcela 不會無所事事等候警示。 緊接著在每次重新部署之後，她會查看 [回應時間][perf] -整體數據和最慢的要求，以及例外狀況計數資料表。  



![回應時間圖及伺服器回應時間格線。](./media/app-insights-detect-triage-diagnose/09-dependencies.png)

她可以評估每個部署的效能影響，通常是將每週與前一週比較。 如果突然有變慢的情況，她會將該情況向相關的開發人員反應。


## 分級


分級 - 評估問題的嚴重性和程度 - 是偵測後的第一個步驟。 我們是否應該在半夜打電話給小組？ 或是問題可以留到累積的工作中下一次方便的時候？ 分級時有一些重要的問題。


發生多少次？ [概觀] 分頁的圖表可提供問題的某些觀點。 例如，Fabrikam 應用程式在一個晚上產生了四個 Web 測試警示。 小組在早上查看圖表，可以發現確實出現一些紅點，但多數的測試仍是綠色。 深入探索可用性圖表，很明顯地，這所有的間歇性問題都來自一個測試位置。 這顯然是僅影響一個路徑的網路問題，而且很可能可自行解決。  


相反地，例外狀況計數或回應時間圖表中明顯且穩定的上升則明顯有其他問題需要注意。


實用的分級策略是「自己動手做」。 如果您遇到相同問題，就會知道它是真的。


哪個部分的使用者受到影響？ 若要獲得約略的答案，請將失敗率除以工作階段計數。


![失敗的要求和工作階段的圖表](./media/app-insights-detect-triage-diagnose/10-failureRate.png)

在緩慢回應的情況中，將回應最緩慢的要求表與每個頁面的使用頻率相比較。


封鎖案例的重要性如何？ 如果這是功能性問題，封鎖了特定的使用者劇本，有很大影響嗎？ 如果客戶無法支付帳單，便很嚴重；如果客戶無法變更其畫面色彩喜好設定，也可以稍候再解決。 事件或例外狀況的詳細資料或緩慢頁面的身分識別，會告知您客戶發生問題的位置。


## 診斷


診斷與偵測不太一樣。 開始追蹤程式碼之前，您應該對問題的原因、位置和發生時機有約略的構念。


**發生時機為何？** 事件和度量圖表提供的歷程檢視可讓您輕鬆將影響與可能原因產生相互關聯。 如果回應時間或例外狀況率中有間歇性的尖峰，請查看要求計數：如果尖峰是在相同時間，則可能是資源問題。 您需要指派更多 CPU 或記憶體嗎？ 或者它是無法管理負載的相依性？


**問題在於我們嗎？**  如果特定類型的要求效能突然下降 - 例如客戶想要對帳單時 - 則可能是外部子系統而非 Web 應用程式有問題。 在「計量瀏覽器」中，選取相依性失敗率和相依性期間率，並將其過去幾個小時或幾天的歷程記錄與您偵測到的問題比較。 如果有相互關聯的變更，則外部子系統可能是原因所在。  


![相依性失敗和對相依性呼叫期間的圖表](./media/app-insights-detect-triage-diagnose/11-dependencies.png)

有些緩慢相依性的問題是地理位置問題。 Fabrikam 銀行使用 Azure virtual 機器，並發現他們誤將 Web 伺服器和帳戶伺服器放置在不同國家/地區。 透過移轉其中一部伺服器獲得明顯的改善。


**我們做了什麼？** 如果問題似乎不在於相依性，而且如果不是持續有問題，則可能是由於最近的變更而導致。 度量和事件圖表提供的歷史觀點讓您輕鬆將任何突然的變更與部署產生相互關聯。 它可縮小問題的搜尋範圍。


**發生什麼？** 有些問題很少發生，而且透過離線測試可能難以追蹤。 我們能做的是嘗試在問題發生時擷取錯誤。 您可以在例外狀況報告中檢查堆疊傾印。 此外，您可以使用喜好的記錄架構或使用 TrackTrace() 或 TrackEvent() 來編寫追蹤呼叫。  


Fabrikam 的帳戶間轉送發生間歇性問題，但只有某些帳戶類型有此情況。 為了更加了解發生的情況，他們在程式碼中的重要點插入了 TrackTrace() 呼叫，附加帳戶類型作為每個呼叫的內容。 那使得要在診斷搜尋中僅篩選掉這些追蹤更為輕鬆。 他們也將參數值附加為追蹤呼叫的屬性和測量。


## 處理


診斷問題之後，您可以製訂修正問題的計劃。 也許您需要復原最近的變更，或也許您可以繼續並修正它。 修正完成之後，Application Insights 將告知您是否成功。  


Fabrikam 銀行的開發小組對效能測量採取較使用 Application Insights 之前更具結構的方法。

* 他們會在 Application Insights 概觀頁面就特定度量設定效能目標。

* 他們從頭為應用程式設計效能度量，例如透過「漏斗」測量使用者進度的度量。  




## 使用量

Application Insights 也可以用來了解使用者在應用程式內執行的動作。 執行順暢時，小組會想要得知哪些功能最受歡迎、使用者喜歡或感到有困難的部份，以及使用者回來的頻率。 這些資訊有助於將他們近期的工作排定優先順序。 而他們可以計劃測量每個功能的成功度，作為開發週期的一部份。 [閱讀更多][usage]。

## 您的應用程式

以上就是一個小組如何使用 Application Insights 來不只是修正個別問題，還改善其開發週期的情況。 希望它有給予您一些 Application Insights 如何協助改善您自己的應用程式效能的概念。

## 影片

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[usage]: app-insights-web-track-usage.md
 


