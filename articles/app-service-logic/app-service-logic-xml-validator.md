<properties
   pageTitle="在 Logic Apps 中使用 BizTalk XML 驗證器 | Microsoft Azure App Service"
   description="邏輯應用程式中使用 BizTalk XML 驗證器的結構描述進行驗證"
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


# BizTalk XML 驗證器

在您的應用程式中使用 BizTalk XML 驗證器連接器，可以根據預先定義的 XML 結構描述來驗證 XML 資料。 使用者可以使用現有的結構描述，或根據一般檔案執行個體、JSON 執行個體，或現有的連接器產生結構描述。

## 使用 BizTalk XML 驗證器

若要使用 BizTalk Xpath 驗證器，請先建立 BizTalk XML 驗證器 API 應用程式的執行個體。 這可以在建立邏輯應用程式時同時建立，或從 Azure Marketplace 選取 BizTalk XML 驗證器 API 應用程式來建立。

### 設定 BizTalk XML 驗證器

BizTalk XML 驗證器會將結構描述納入其組態中。 直接從 Azure 入口網站啟動 API 應用程式，或按兩下設計工具介面上的 API 應用程式，使用者即可啟動 API 應用程式組態刀鋒視窗：

![BizTalk XML 驗證器組態][1]

在 [API 應用程式] 刀鋒視窗中，使用者可以選取 [結構描述]** 來設定結構描述：

![BizTalk XML 驗證器結構描述組件][2]

使用者可以從磁碟上傳結構描述，或從一般檔案執行個體或 JSON 執行個體產生一個結構描述：

![BizTalk XML 驗證器結構描述][3]


### 在設計介面中使用 BizTalk 一般檔案編碼器

一旦設定，使用者可以選取 *->*，並從動作清單中選擇動作：

![BizTalk XML 驗證器組態][4]

#### 驗證 Xml

「驗證 Xml」動作會根據預先設定的結構描述，驗證指定的 xml 輸入。

![BizTalk XML 驗證器驗證 Xml][5]

 參數| 類型| 參數說明
---|---|---
 輸入 Xml| 字串| 輸入要驗證的 Xml

此動作會以物件形式傳回輸出。 輸出包含代表 XML 驗證器回應的模型。 它包含結果、結構描述名稱、根節點和錯誤描述。




[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG 
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG 
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG 
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG 
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG 

