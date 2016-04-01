<properties
   pageTitle="在 Logic Apps 中使用 Twitter 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Twitter 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# 開始使用 Twitter 連接器並將它加入您的邏輯應用程式
連接到 Twitter 摘要，以張貼推文，並從您 Twitter 帳戶的時間表、好友時間表和粉絲中取得推文。 在 Logic Apps 中，連接器可以在「工作流程」中用來擷取、處理或推送資料。 在工作流程中使用 Twitter 連接器時，您可以達到各種案例的目的。 例如，您可以：

- 取得與指定的關鍵字或文字相關聯的新推文。 擷取到新推文時，它會觸發工作流程的新執行個體，並將資料傳遞到工作流程中的下一個連接器。 例如，您可建立 Twitter 連接器，並使用 [搜尋新推文] 觸發程序監視 #peanutbutterandjelly。 只要 #peanutbutterandjelly 有新的推文，就會自動啟動您的工作流程 (也稱為邏輯應用程式)。
- 使用不同的動作 (例如 [搜尋推文])，您可以取得回應，並在您的工作流程內加以使用。 例如，您可以在推文中搜尋您公司的名稱。 找到時，您可以使用邏輯應用程式將此資料寫入 SQL Server 資料庫。 然後，使用 SQL Server 資料判斷有關貴公司的推文。 
- 使用在所有運算子 [Twitter 搜尋](https://twitter.com/search)。 選取 **運算子** 連結。 Twitter 連接器支援所有列出的運算子。


## 觸發程序和動作
*觸發程序* 所發生的事件。 例如，新的推文可以觸發或啟動工作流程或程序。  *動作* 是某項目的結果。 例如，您可以搜尋特定推文，並在找到時，傳送電子郵件或更新資料庫。 

Twitter 連接器可以在邏輯應用程式中用作觸發程序或動作，且支援 JSON 和 XML 格式的資料。 

Twitter 連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
搜尋新推文 | <ul><li>取得使用者時間軸</li><li>搜尋推文</li><li>推文</li><li>取得提及時間軸</li><li>取得首頁時間軸</li><li>取得粉絲</li><li>取得好友</li><li>取得使用者詳細資料</li><li>推文給使用者</li><li>傳送直接訊息</li></ul>

 **新推文** 觸發程序已封存。 目前，它仍然是進階作業，因此可以使用。  **轉** 動作移除，且不受支援。 如果您使用 [轉推] 動作，則會在執行階段失敗。 因此，請從邏輯應用程式中移除 [轉推] 動作。 


## 建立 Twitter 連接器

> [AZURE.IMPORTANT] 在建立 Twitter 連接器，您可以選擇如何向 Twitter 註冊您的應用程式，並使用 Twitter 連接器應用程式金鑰。  您可以免費註冊應用程式，在 [http://apps.twitter.com](http://apps.twitter.com)。  註冊時，請確定您提供一些回呼 URL。  一旦建立了您的 Twitter 連接器，稍後您就可以變更回呼 URL。  您必須建立 Twitter API 金鑰和密碼，才能建立連接器。

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：

1. [選用]建立免費的應用程式的 Twitter: [http://apps.twitter.com](http://apps.twitter.com)。
    * 註冊應用程式時，您可以放入網站的任何 URL。  指定任何回呼 URL (不要保留為空白)，您稍後可以更新它。
2. 在 Azure 開始面板中，選取 **Marketplace**。
3. 搜尋 「 Twitter 連接器 」，選取它，並選取 **建立**。
4. [選用] 按一下 [封裝設定]，並從 Twitter 應用程式將 [取用者金鑰] 複製到 [clientId] 欄位中。  從您 Twitter 應用程式消費者密碼貼入 'clientSecret' 欄位 ︰  
![][10]
5. 輸入有關連接器名稱、App Service 和 資源群組的其他必要設定。
6.  按一下 [ **建立**。

> [AZURE.NOTE] 如果您想要進一步保護您在重新導向 url Twitter 的 API，您可以查看詳細資料上如何進行這項 [這裡](app-service-logic-oauth-security.md)


## 在邏輯應用程式中使用 Twitter 連接器
建立 API 應用程式之後，您即可使用 Twitter 連接器做為 Logic Apps 的觸發程序或動作。 作法：

1.  建立新的邏輯應用程式，或開啟現有邏輯應用程式：  
![][2]
2.  開啟 **觸發程序和動作** 開啟邏輯應用程式的設計工具 ︰  
![][3]
3.  Twitter 連接器會列在右邊。 選取它，以自動將其加入邏輯應用程式：  
![][4]
4.  選取 **授權**, 、 輸入 Twitter 認證，然後選取 **授權應用程式**:  
![][5]


您現在可以設定 Twitter 連接器，以建置您的工作流程。 您可以在流程的其他動作中使用從 Twitter 觸發程序所擷取的推文：  
![][6]

您可以採用類似方式在工作流程中使用 Twitter 動作。 選取 Twitter 動作，並設定該動作的輸入：  
![][7]  
![][8]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png
[9]: ./media/app-service-logic-connector-twitter/settings.PNG
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png


