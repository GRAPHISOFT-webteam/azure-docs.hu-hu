<properties 
    pageTitle="在 Logic Apps 中使用 Slack 連接器 | Microsoft Azure App Service"
    description="如何建立並設定 Slack 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
    authors="rajeshramabathiran" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="rajram"/>

# 開始使用 Slack 連接器並將它加入您的邏輯應用程式
連接至 Slack 通道，並將訊息張貼至您的小組。 連接器可在 Logic Apps 中用做「工作流程」的一部分，以執行不同的工作。 在工作流程中使用 Slack 連接器時，可以使用其他連接器來達到各種案例的目的。 例如，您可以使用 [Facebook 連接器](app-service-logic-connector-facebook.md) 在您的工作流程將訊息張貼至 Slack 通道。 

## 觸發程序和動作
*觸發程序* 所發生的事件。 例如，訂單更新時或加入新客戶時。  *動作* 是觸發程序的結果。 例如，當訂單更新時，傳送警示給銷售人員。 或者，當加入新客戶時，傳送歡迎電子郵件給新客戶。 

Slack 連接器可以在邏輯應用程式中用作動作，且支援 JSON 和 XML 格式的資料。 Slack 連接器目前沒有觸發程序。 

Slack 連接器提供下列觸發程序和動作： 

觸發程序 | 動作
--- | ---
None | 張貼訊息

## 建立 Slack 連接器
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器： 

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 選取 **API 應用程式** ，並搜尋 「 Slack 連接器 」。
3. 輸入名稱、App Service 方案和其他屬性：  
![][1] 

4. 按一下 [ **建立**。 

## 在邏輯應用程式中使用連接器做為動作

> [AZURE.IMPORTANT] 一定要在相同的資源群組中建立的連接器和邏輯應用程式。 

建立 Slack 連接器之後，即可將它當做動作加入邏輯應用程式中： 

1.  邏輯應用程式內開啟 **觸發程序和動作**。  
    [建立新的邏輯應用程式](app-service-logic-create-a-logic-app.md)

2.  Slack 連接器會列在右邊組件庫 ︰  
![][2]

3.  選取您建立的 Slack 連接器，以自動將它加入邏輯應用程式。 
4.  選取 **授權**。 登入 Slack 帳戶。 結束前，系統會要求您提供您連接器的權限，以存取您的 Slack 帳戶。 選取 **授權**:  
![][3]  
![][4]  
![][5]  
![][6]  
    
5.  您現在可以在流程中使用 Slack 連接器。 張貼訊息動作有 ︰  
![][7]


我們一起逐步體驗「張貼訊息」。 您可以使用這個動作，將訊息張貼至任何 Slack 通道：  
![][8]

設定 [張貼訊息] 動作的輸入屬性：

屬性 | 說明
--- | ---
文字 | 輸入要張貼的訊息文字。
通道名稱 | 輸入要在其中張貼此訊息的 Slack 通道。 如果未輸入通道，則會將訊息張貼至 #general。
進階屬性 | **Bot 使用者名稱**︰ 要用於此訊息的 bot 名稱。 如果這不輸入，會將訊息張貼為"Bot"。<p><p>**圖示 URL**︰ 要作為此訊息圖示的影像 URL。<p><p>**圖示 Emoji**︰ 要作為此訊息圖示的 Emoji。 這個屬性會覆寫圖示 URL 屬性。


Slack 連接器具有 REST API，因此您可以在邏輯應用程式外部使用連接器。 開啟 Slack 連接器，然後選取 **API 定義**:  
![][9]


## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG


