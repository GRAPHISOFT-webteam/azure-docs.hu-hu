<properties 
    pageTitle="使用 Application Insights 監視 SharePoint 網站" 
    description="開始使用新的檢測金鑰監視新的應用程式" 
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

# 使用 Application Insights 監視 SharePoint 網站


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights 會監視應用程式的可用性、效能和使用情形。 您將在這裡深入了解如何針對 SharePoint 網站進行設定。


## 建立 Application Insights 資源


在 [Azure 入口網站](http://portal.azure.com), ，建立新的 Application Insights 資源。 選擇 ASP.NET 做為應用程式類型。

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-sharepoint/01-new.png)


開啟的刀鋒視窗是您要查看您的應用程式效能和使用量資料的位置。 若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。 或者按一下 [瀏覽] 以尋找它。
    


## 將我們的指令碼加入至您的網頁

在快速入門中，取得網頁指令碼：

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

在您想要追蹤之每一個頁面的 &lt;/head&gt; 標記之前插入指令碼。 如果您的網站有主版頁面，您可以那裡放入指令碼。 例如，在 ASP.NET MVC 專案中，可放在 View\Shared\_Layout.cshtml 中

指令碼包含檢測金鑰，會將遙測導向您的 Application Insights 資源。

### 將程式碼加入至網站頁面

#### 在主要頁面上

如果您可以編輯網站的主要頁面，將會提供網站中每一頁面的監視。

簽出主要頁面，並且使用 SharePoint Designer 或任何其他編輯器來編輯。

![](./media/app-insights-sharepoint/03-master.png)


之前加入程式碼 </head> 標籤中) 取代。 


![](./media/app-insights-sharepoint/04-code.png)

#### 或在個別的頁面上

若要監視一組有限的頁面，將指令碼個別加入至每個頁面。 

插入網頁組件並在其中嵌入程式碼片段。


![](./media/app-insights-sharepoint/05-page.png)


## 檢視應用程式相關的資料

重新部署您的應用程式。

返回您的應用程式刀鋒視窗中 [Azure 入口網站](http://portal.azure.com)。

前幾個事件將出現在搜尋中。 

![](./media/app-insights-sharepoint/09-search.png)

如果您預期有更多資料，請在幾秒之後按一下 [重新整理]。

從 [概觀] 分頁中，按一下 [ **流量分析** 可查看使用者、 工作階段和頁面檢視的圖表 ︰

![](./media/app-insights-sharepoint/06-usage.png)

按一下任何圖表以查看更多詳細資料 - 例如頁面檢視次數：

![](./media/app-insights-sharepoint/07-pages.png)

或使用者：


![](./media/app-insights-sharepoint/08-users.png)



## 後續步驟

* [Web 測試](app-insights-monitor-web-app-availability.md) 來監視您的網站的可用性。

* [Application Insights](app-insights-overview.md) 其他類型的應用程式。



<!--Link references-->


 

