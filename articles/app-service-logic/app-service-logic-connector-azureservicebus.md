<properties
   pageTitle="在 Logic Apps 中使用 Azure 服務匯流排連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Azure 服務匯流排連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>


# 開始使用 Azure 服務匯流排連接器並將它加入您的邏輯應用程式 
連線至 Azure 服務匯流排可以將訊息傳送至「佇列和主題」，並接收來自「佇列和訂用帳戶」的訊息。 連接器在 Logic Apps 中是用作「工作流程」的一部分。 

## 觸發程序和動作
觸發程序是發生的事件。 例如，訂單更新時或加入新客戶時。 動作是觸發程序的結果。 例如，將訂單或新訊息放入佇列時，會傳送警示或訊息。  

Azure 服務匯流排連接器可以在邏輯應用程式中用作觸發程序或動作，且支援 JSON 和 XML 格式的資料。

Azure 服務匯流排連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
可用的郵件 | 傳送訊息

## 建立 Azure 服務匯流排連接器
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：  

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 搜尋 [Azure 服務匯流排連接器]、 選取它，並選取 **建立**。
3. 輸入名稱、App Service 方案和其他屬性：  
    ![][1]

4. 輸入下列封裝設定：

    名稱 |描述
--- | ---
連接字串 |Azure 服務匯流排連接字串。 例如，輸入 ︰ *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName = [名稱]。SharedAccessKey = [金鑰]*。
實體名稱 |輸入佇列或主題的名稱。
訂閱名稱 |輸入要從中接收訊息的訂用帳戶的名稱。

5. 按一下 [ **建立**。

## 在邏輯應用程式中使用服務匯流排連接器
建立連接器之後，即可使用 Azure 服務匯流排連接器做為邏輯應用程式的觸發程序或動作。 作法：

1.  建立新的邏輯應用程式，並選擇具有 Azure 服務匯流排連接器的相同資源群組 ︰  
    ![][2]

2.  開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具並設定您的工作流程 ︰  
    ![][3]

3. Azure 服務匯流排連接器會出現在右側資源庫中的 API 應用程式中此資源群組] 區段中 ︰  
    ![][4]

4. 您可以按一下 [Azure 服務匯流排連接器]，將 Azure 服務匯流排連接器置入編輯器。

5.  您現在可以在工作流程中使用 Azure 服務匯流排連接器。 您可以使用從 Azure 服務匯流排觸發程序 （[可用訊息]） 中的其他動作流程中擷取的訊息 ︰  
    ![][5]  

    ![][6]

您也可以使用 Azure 服務匯流排傳送訊息] 動作 ︰  
![][7]  

![][8]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG


