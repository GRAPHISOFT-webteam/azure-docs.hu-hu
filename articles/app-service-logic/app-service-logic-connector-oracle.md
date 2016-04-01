<properties
   pageTitle="在 Logic Apps 中使用 Oracle Database 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Oracle Database 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
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


# 開始使用 Oracle Database 連接器並將它加入您的邏輯應用程式
連接到內部部署 Oracle 資料庫伺服器來建立和變更資訊或資料。 在 Logic Apps 中，連接器可以在「工作流程」中用來擷取、處理或推送資料。 在工作流程中使用 Oracle 連接器時，您可以達到各種案例的目的。 例如，您可以：

- 使用 Web 或行動應用程式，公開位於 Oracle 資料庫中的部分資料。
- 將資料插入 Oracle 資料庫資料表以儲存。 例如，您可以輸入員工記錄、更新銷售訂單等等。
- 從 Oracle 取得資料，以供商務程序使用。 例如，您可以取得客戶記錄，並將這些客戶記錄放在 SalesForce 中。


## 觸發程序和動作
*觸發程序* 所發生的事件。 例如，訂單更新時或加入新客戶時。  *動作* 是觸發程序的結果。 例如，當訂單更新時，傳送警示給銷售人員。 或者，當加入新客戶時，傳送歡迎電子郵件給新客戶。

Oracle 資料庫連接器可以在邏輯應用程式中做為觸發程序或動作，且支援 JSON 和 XML 格式的資料。 對於封裝設定中包含的每個資料表 (本主題稍後詳細說明)，都有一組 JSON 動作和一組 XML 動作。 如果您使用 XML 觸發程序或動作，您可以使用 [轉換 API 應用程式](app-service-logic-transform-xml-documents.md) 將資料轉換成另一種 XML 資料格式。

Oracle 資料庫連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
輪詢資料 | <ul><li>插入到資料表</li><li>更新資料表</li><li>從資料表選取</li><li>從資料表刪除</li><li>呼叫預存程序</li>


## 建立 Oracle 資料庫連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 選取 **API 應用程式** ，並搜尋 「 Oracle 資料庫連接器 」。
3. 輸入名稱、App Service 方案和其他屬性。
4. 輸入下列封裝設定：

    名稱 |需要 | 描述
--- | --- | ---
資料來源 |[是 |已安裝 Oracle 用戶端電腦上 tnsnames.ora 檔案中指定的資料來源 （網路服務） 名稱。 資料來源名稱和 tnsnames.ora 的相關資訊，請參閱 [設定 Oracle 用戶端](http://msdn.microsoft.com/library/dd787872.aspx)。
使用者名稱 |[是 |輸入要連接到 Oracle 伺服器的使用者名稱。
密碼 |[是 |輸入使用者名稱密碼。
服務匯流排連接字串 |[是 |如果您要連線至內部部署上，輸入服務匯流排轉送連接字串。<br/><br/>[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)<br/>[服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/)
資料表 |否 |輸入可由連接器修改的資料庫中的資料表。 例如，輸入 *OrdersTable，EmployeeTable*。
預存程序 |否 |輸入可由連接器呼叫的資料庫中的預存程序。 例如，輸入 *IsEmployeeEligible，CalculateOrderDiscount*。
函式 |否 |輸入可由連接器呼叫的資料庫中的函式。 例如，輸入 *IsEmployeeEligible，CalculateOrderDiscount*。
封裝實體 |否 |輸入可由連接器呼叫的資料庫中的封裝。 例如，輸入 *packageorderprocessing.completeorder，Packageorderprocessing.generatebill*。
資料提供陳述式 |否 |輸入陳述式來判斷是否有任何資料可供輪詢。 例如，輸入 *選取 * from table_name*。
輪詢類型 |否 |輸入輪詢類型。 允許的值為「Select」、「Procedure」、「Function」、「Package」。
輪詢陳述式 |否 |輸入輪詢 Oracle Server 資料庫的陳述式。 例如，輸入 *選取 * from table_name*。
後輪詢陳述式 |否 |輸入輪詢後要執行的陳述式。 例如，輸入 *刪除 * from table_name*。

5. 完成時，[封裝設定看起來如下所示 ︰
<br/>
![][1]  


## 使用連接器做為觸發程序
讓我們以一個簡單的邏輯應用程式為例，它會輪詢 Oracle 資料表的資料、在另一個資料表中加入資料，以及更新資料。

### 加入觸發程序
1. 建立或編輯邏輯應用程式時，請選取您建立的 Oracle 連接器做為觸發程序。 這樣會列出可用的觸發程序 ︰ **輪詢資料 (JSON)** 和 **輪詢資料 (XML)**:
<br/>
![][5]

2. 選取 **輪詢資料 (JSON)** 觸發程序中，輸入頻率，然後按一下 ✓ ︰
<br/>
![][6]

3. 現在，觸發程序在邏輯應用程式中顯示為已設定。 觸發程序的輸出會顯示，並可用來當做輸入任何後續的動作 ︰
<br/>
![][7]

## 使用連接器做為動作
以我們簡單的邏輯應用程式為例，它會輪詢 Oracle 資料表的資料、在另一個資料表中加入資料，以及更新資料。

若要使用 Oracle 連接器做為動作，請輸入您建立 Oracle 連接器時所輸入的資料表及/或預存程序的名稱：

1. 從元件庫選取相同的 Oracle 連接器做為動作。 選取其中一個 「 插入 」 動作，例如 *插入到 TempEmployeeDetails (JSON)*:
<br/>
![][8]

2. 輸入輸入的值的記錄插入，然後按一下 ✓ ︰
<br/>
![][9]

3. 從資源庫選取您建立的同一個 Oracle 連接器。 做為動作中，選取 「 更新 」 動作的同一個資料表，例如 *Update TempEmployeeDetails*:
<br/>
![][11]

4. 輸入更新動作中，輸入的值，然後按一下 ✓ ︰
<br/>
![][12]

您可以在所輪詢的資料表中加入新記錄，以測試邏輯應用程式。

## 混合式組態

> [AZURE.NOTE] 在防火牆後方使用 Oracle 內部時，才需要此步驟。

App Service 使用混合式組態管理員來安全地連線到內部部署系統。 如果您的連接器使用內部部署 Oracle，則需要混合式連線管理員。

請參閱 [使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png


