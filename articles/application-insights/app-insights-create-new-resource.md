<properties 
    pageTitle="建立新 Application Insights 資源" 
    description="針對新的即時應用程式設定 Application Insights 監視。以 Web 為基礎的方法。" 
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
    ms.date="11/06/2015" 
    ms.author="awills"/>


# 建立新 Application Insights 資源

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights 會在 Microsoft Azure *資源*中顯示您應用程式的資料。 建立新的資源屬於因此 [設定 Application Insights 來監視新的應用程式 ][start]。 在許多情況下，這是可由 IDE 自動完成的工作，而且也建議盡量使用這種方式。 但在某些情況下，您必須以手動方式建立資源。

建立資源後，您會取得其檢測金鑰，並將該金鑰用來設定應用程式中的 SDK。 這麼做會將遙測資料傳送到資源。

## 註冊 Microsoft Azure

如果您還沒有 [Microsoft 帳戶，請立即申請](http://live.com)。 (如果您使用 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服務，就會有 Microsoft 帳戶)。

您也需要訂閱 [Microsoft Azure](http://azure.com)。 如果您的小組或組織擁有 Azure 訂用帳戶，則擁有者就可以使用您的 Windows Live ID 將您加入該訂用帳戶。

或者您可以建立新的訂用帳戶。 免費試用版可讓您試用 Azure 中的所有功能。 試用期過後，您可能會發現隨用隨付的訂用帳戶較適合您，因為不需要為免費服務付費。

當您有存取訂用帳戶，登入 Application Insights 在 [http://portal.azure.com](http://portal.azure.com), ，並使用您的 Live ID 登入。


## 建立 Application Insights 資源

在 [portal.azure.com](https://portal.azure.com), ，加入 Application Insights 資源:

![按一下 ](./media/app-insights-create-new-resource/01-new.png)


* **應用程式類型** 會影響您看到 [概觀] 分頁中可用的屬性等 [計量瀏覽器 ][metrics]。 如果沒有看到您的應用程式類型，請針對網頁選擇其中一個 Web 類型，針對其他裝置則選擇其中一個手機類型。
* **資源群組**可讓您輕鬆管理屬性，例如存取控制。 如果您已經建立其他 Azure 資源，可以選擇將這個新的資源放到同一個群組。
* **訂用帳戶**是您在 Azure 中的付款帳戶。
* **位置**是我們保留您資料的地方。 目前無法變更位置。
* **新增至開始面板**可將 Azure 首頁上的資源放到快速存取磚。 建議使用。

建立您的應用程式後，會開啟新的刀鋒視窗。 您會在其中看到應用程式的效能和使用情況資料。

若要在下次登入 Azure 時返回該應用程式，請在開始面板 (主畫面) 上尋找應用程式的快速啟動磚。 或按一下 [瀏覽] 以尋找它。


## 複製檢測金鑰

檢測金鑰會識別您所建立的資源。 您將需要它以提供給 SDK。

![按一下 ](./media/app-insights-create-new-resource/02-props.png)

## 在應用程式中安裝 SDK

在應用程式中安裝 Application Insights SDK 核心。 此步驟高度仰賴於應用程式的類型。

使用檢測金鑰來設定 [您在 ][start]。

SDK 包含不需撰寫任何程式碼，即可傳送遙測資料的標準模組。 若要追蹤使用者動作或診斷問題的詳細資料， [使用 API ][api] 傳送您自己的遙測。


## <a name="monitor"></a>查看遙測資料

關閉 [快速入門] 刀鋒視窗，返回 Azure 入口網站中的應用程式刀鋒視窗。

按一下 [搜尋] 磚，以查看 [診斷搜尋 ][diagnostic], ，其中會顯示前幾個事件。

如果您預期有更多資料，請在幾秒之後按一下 [重新整理]。

## 自動建立資源

您可以撰寫 [PowerShell 指令碼](app-insights-powershell-script-create-resource.md) 自動建立資源。







[api]: app-insights-api-custom-events-metrics.md 
[diagnostic]: app-insights-diagnostic-search.md 
[metrics]: app-insights-metrics-explorer.md 
[start]: app-insights-overview.md 

