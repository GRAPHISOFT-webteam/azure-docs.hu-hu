<properties
   pageTitle="在 Logic Apps 中使用 BizTalk XPath 擷取程式 | Microsoft Azure App Service"
   description="BIzTalk XPath 擷取程式"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/07/2015"
   ms.author="rajram"/>


# BIzTalk XPath 擷取程式

BizTalk XPath 擷取連接器可協助您的應用程式根據指定的 XPath 查閱 XML 內容並從中擷取資料。

## 使用 BizTalk XPath 擷取程式

1. 若要使用 BizTalk Xpath 擷取程式，您必須先建立 BizTalk Xpath 擷取程式 API 應用程式的執行個體。 在建立邏輯應用程式時進行內嵌，或從 Azure Marketplace 選取 BizTalk Xpath 擷取程式 API 應用程式，即可完成此作業。
    >[AZURE.NOTE] 沒有與 BizTalk Xpath 擷取程式相關聯的組態設定。
2. [建立新的邏輯應用程式]。 在邏輯應用程式中開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，來設定您的流程。
3. 設計工具上，右窗格會列出可用來建置流程的 API Apps。 尋找「BizTalk XPath 擷取程式」。 選取此項將新增「XPath 擷取程式」到您的流程，並佈建它的執行個體。
4. 佈建後，設計工具會顯示與 BizTalk XPath 擷取程式 API 應用程式相關聯的動作:  
    ![BizTalk XPath 擷取程式選擇動作][1]

5. 選擇 [使用 XPath 擷取]。 「 使用 XPath 擷取] 會評估指定的輸入 XML 中的輸入的 xpath 運算式:  
    ![BizTalk XPath 擷取程式輸入][2]

    參數| 類型| 參數說明
---|---|---
 XPath| 字串| 查詢 xml 內的路徑。
 輸入 Xml| 字串| 輸入 Xml 內容。


此動作會以字串形式傳回輸出 - 結果。 結果包含 XML 內查詢路徑的值。




[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG 
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG 
[create a new logic app]: app-service-logic-create-a-logic-app.md 

