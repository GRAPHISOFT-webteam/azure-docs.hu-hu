<properties 
    pageTitle="疑難排解 Java Web 專案中的 Application Insights" 
    description="疑難排解指南 - 使用 Application Insights 監視即時的 Java 應用程式。" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2015" 
    ms.author="awills"/>


# Application Insights for Java 的疑難排解和問答集

疑問或問題 [Java ][java]嗎? 以下是一些秘訣。


## 建置錯誤

*在 Eclipse 中，透過 Maven 或 Gradle 加入 Application Insights SDK 時，我收到建置或總和檢查碼驗證錯誤。*

* 如果相依性 <version> 項目使用具有萬用字元的模式 (例如 (Maven) `< > [1.0 版，) < / 版本 >` 或 (Gradle) `版本: '1.0.+'`)，請嘗試改為指定的特定版本，例如 `1.0.2`。請參閱 [版本資訊](app-insights-release-notes-java.md) 最新版本。

## 沒有資料

*我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。*

* 請稍等片刻，然後按一下 [重新整理]。 圖表會定期自行重新整理，但您也可以手動重新整理。 重新整理間隔取決於圖表的時間範圍。
* 檢查 ApplicationInsights.xml 檔案 (位於專案的 resources 資料夾) 中已定義檢測機碼
* 確認沒有任何 `true < DisableTelemetry > < / DisableTelemetry >` xml 檔案中的節點。
* 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。
* 在 Microsoft Azure 開始面板中，查看服務狀態對應。 如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
* 新增記錄至 IDE 主控台視窗中，以開啟 `< SDKLogger / >` ApplicationInsights.xml 檔案 (位於您的專案在 [資源] 資料夾) 和檢查做為 [錯誤] 開頭的項目中的根節點下的項目。
* 藉由查看主控台的輸出訊息「已成功找到組態檔」陳述式，確定 Java SDK 已成功載入正確的 ApplicationInsights.xml 檔案。
* 如果找不到組態檔，請檢查輸出訊息以查看在何處搜尋組態檔，並確定 ApplicationInsights.xml 位在這些搜尋位置中的其中一個位置。 根據經驗法則，您可以將組態檔置於 Application Insights SDK JAR 附近。 例如：在 Tomcat 中，這可能表示 WEB-INF/lib 資料夾。



#### 我曾經看到資料，但是已停止

* 檢查 [狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 即可查看。 若有達到配額，您可以升級您的方案，或付費取得額外容量。 請參閱 [定價機制](http://azure.microsoft.com/pricing/details/application-insights/)。



## 沒有使用狀況資料

*我看到要求和回應時間的相關資料，但沒有看到頁面檢視、瀏覽器或使用者資料的相關資料。*

您已成功設定應用程式從伺服器傳送遙測。 現在，您的下一個步驟是 [設定您的網頁來從網頁瀏覽器 ][usage]。

或者，如果您的用戶端中的應用程式 [電話或其他裝置 ][platforms], ，您可以從該處傳送遙測。

使用相同的檢測機碼來設定用戶端和伺服器遙測。 資料會出現在相同的 Application Insights 資源中，而且您可以相互關聯來自用戶端和伺服器的事件。



## 停用遙測

*如何停用遙測收集？*

在程式碼中：

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);

**或**

更新 ApplicationInsights.xml (位於專案的 resources 資料夾)。 在根節點下加入下列程式碼：

    <DisableTelemetry>true</DisableTelemetry>

如果使用 XML 方法，則必須在變更值時重新啟動應用程式。

## 變更目標

*如何變更我的專案將資料傳送到哪一個 Azure 資源？*

* [取得新資源的檢測金鑰。[] java][java]
* 如果您使用 Azure Toolkit for Eclipse 將 Application Insights 加入至專案，請以滑鼠右鍵按一下 Web 專案，依序選取 [Azure]**** 和 [設定 Application Insights]****，然後變更金鑰。
* 否則，請更新專案之 resources 資料夾的 ApplicationInsights.xml 中的機碼。


## Azure 開始畫面

* 我正在查看 [Azure 入口網站](http://portal.azure.com)。 沒有地圖是否告知資訊我的應用程式? *

否，它會顯示世界各地 Azure 伺服器的健全狀況。

*從 Azure 開始面板 (主畫面) 中，如何找到我應用程式的相關資料？*

假設您 [設定您的應用程式的 Application Insights ][java], ，按一下 [瀏覽、 選取 Application Insights]，然後選取您建立應用程式的應用程式資源。 日後若要更快速地從該處開始，您可以將應用程式釘選至開始面板。

## 內部網路伺服器

*可以在我的內部網路上監視伺服器嗎？*

是，前提是您的伺服器可以透過公用網際網路將遙測傳送至 Application Insights 入口網站。

在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。

## 資料保留

* 資料保留在入口網站的時間長度是? 是否安全? *

請參閱 [資料保留和隱私權 ][data]。

## 後續步驟

* 設定 Application Insights Java 伺服器應用程式。 我可以做什麼? *

* [監視 ][availability]
* [監視網頁使用狀況 ][usage]
* [追蹤使用狀況並診斷裝置應用程式 ][platforms]
* [撰寫程式碼來追蹤您的應用程式 ][track]
* [擷取診斷記錄 ][javalogs]


## 取得說明

* [堆疊溢位](http://stackoverflow.com/questions/tagged/ms-application-insights)




[availability]: app-insights-monitor-web-app-availability.md 
[data]: app-insights-data-retention-privacy.md 
[java]: app-insights-java-get-started.md 
[javalogs]: app-insights-java-trace-logs.md 
[platforms]: app-insights-platforms.md 
[track]: app-insights-api-custom-events-metrics.md 
[usage]: app-insights-web-track-usage.md 

