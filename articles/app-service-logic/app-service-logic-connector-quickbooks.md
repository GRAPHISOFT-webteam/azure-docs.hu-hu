<properties
   pageTitle="在 Logic Apps 中使用 QuickBooks 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 QuickBooks 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
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


# 開始使用 QuickBooks 連接器並將它加入您的邏輯應用程式
使用 QuickBooks 連接器可建立和修改不同的 QuickBooks 實體。 下表列出受支援的實體：

實體|說明
---|---
帳戶|Account 是組成 Chart Of Accounts 的元件，而且也是 Ledger 的一部分。 用來記錄配置給特定用途的貨幣總數
CreditMemo|CreditMemo 是代表退貨或信用卡付款，或已售出的商品或服務之部分付款的財務交易。
Customer|Customer 是您公司提供服務或產品的客戶。
Estimate|Estimate 代表公司對客戶提議之販售商品或服務的商業交易 (包括提議的售價)。
Invoice|Invoice 代表客戶之後必須針對產品或服務付款的銷售表單。 Invoice 資料模型的其他資訊可參考此處。
項目|Item 是您公司購買、銷售，或重新銷售的項目，例如產品、運送與處理費用、折扣及營業稅 (如果適用)。  Item 會在發票或其他銷售表單上列為一行。
SalesReceipt|此實體代表提供給客戶的銷售收據。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。 您可以將 QuickBooks 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。 

##QuickBooks 動作 ##
以下是 QuickBooks 連接器中可使用的不同動作。

動作|說明
---|---
讀取實體|讀取實體物件
建立或更新實體|建立或更新實體物件。 如果物件已存在會更新物件，若不存在則會建立新物件
刪除|此動作會從選取的實體刪除指定的物件
查詢|查詢作業是針對實體建立引導示查詢的方法。

##建立 QuickBooks 連接器 API 應用程式##
1.  使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.  瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「QuickBooks 連接器」。
3.  設定 QuickBooks 連接器，提供「主控方案」及資源群組的詳細資料，並選取 API 應用程式的名稱。

    ![][13]
4. 在 [封裝設定] 中設定您有興趣讀取/寫入的 QuickBooks 實體。

完成上述步驟後，您現在即可建立 QuickBooks 連接器 API 應用程式。


##建立邏輯應用程式##
讓我們建立一個簡單的邏輯應用程式，以便在 QuickBooks 中建立一個帳戶並更新該帳戶的 [類別類型]。

1.  登入 Azure 入口網站，並按一下 [新增] -> [Web + 行動] -> [邏輯應用程式]

    ![][1]

2.  在 [建立邏輯應用程式] 頁面中，請提供必要的詳細資料，例如名稱、應用程式服務方案和位置。

    ![][2]

3.  按一下 [觸發程序及動作]，邏輯應用程式編輯器畫面便會隨即出現。

    ![][3]

4.  選取 [手動執行此邏輯]，這表示僅能夠以手動方式叫用此邏輯應用程式。


5.  展開資源庫中的 [此資源群組中的 API 應用程式] 來查看所有可用的 API 應用程式。 從資源庫選取 [QuickBooks 連接器]，[QuickBooks 連接器] 就會加入至流程。


6.  如果 QuickBooks 在線上，您必須驗證和授權邏輯應用程式以代表您執行作業。 若要開始授權，請按一下 QuickBooks 連接器上的 [授權]。

    ![][4]

7.  按一下 [授權] 會開啟 QuickBooks 的驗證對話方塊。 提供您要執行作業之 QuickBooks 帳戶的登入詳細資料。

    ![][5]

8. 按一下同意對話方塊中的 [授權]，來授與邏輯應用程式存取您帳戶的權限，以代表您執行作業。

    ![][6]

9.  授權完成後，即會顯示所有的動作。

    ![][7]

10. 選取 [建立或更新帳戶] 動作，即會顯示輸入參數。

    ![][8]

11. 提供 [名稱] 和 [帳戶類型]，然後按 ✓。

    ![][9]

12. 從資源庫的 [最近使用過] 區段選取 [QuickBooks 連接器]，就會加入新的 QuickBooks 動作。

13. 從動作清單中選取 [建立或更新帳戶]，即會顯示動作的輸入參數。

    ![][10]

14. 按一下 [識別碼] 旁邊的 [+]，從 [建立帳戶] 動作的輸出中挑選識別碼值。

    ![][11]

15. 提供帳戶類型新的值，然後按一下 ✓。

    ![][12]

16. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。 完成建立大約需要 30 秒的時間。

17. 瀏覽剛建立的邏輯應用程式，然後按一下 [執行] 以啟動該應用程式。

18. 您可以在 QuickBooks 帳戶中查看是否已建立名為 'Contoso' 的新帳戶。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

