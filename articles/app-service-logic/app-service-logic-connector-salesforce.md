<properties
   pageTitle="在 Logic Apps 中使用 Salesforce 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Salesforce 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
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


# 開始使用 Salesforce 連接器並將它加入您的邏輯應用程式
連線至 Salesforce 並建立和修改如帳戶、組長等實體。 以下是涉及 Salesforce 的典型整合案例：

- Salesforce 和 ERP 系統 (例如 SAP 和 QuickBooks) 之間的帳戶同步處理
- 從 Salesforce 到 ERP 系統的「下單至收款」流程

屬於連接器封裝設定的一部分，使用者可以指定連接器可以管理的實體，而系統便會以動態方式填入動作、輸入和輸出參數。 以下是 Salesforce 連接器中可用的不同動作：

- 建立實體 - 使用此動作建立新的 Salesforce 實體，例如帳戶、案例或自訂物件
- 更新實體：使用此動作可更新現有的 Salesforce 實體
- 更新插入實體：使用此動作可更新現有的 Salesforce 實體，或建立一個 Salesforce 實體 ( 如果不存在)
- 刪除實體：使用此動作可刪除現有的 Salesforce 實體
- 執行查詢：使用此動作可執行以 Salesforce 物件查詢語言 (SOQL) 撰寫的 SELECT 查詢

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。 您可以將 Salesforce 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。 


## 建立 Salesforce 連接器 API 應用程式
1.  使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.  瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋 Salesforce。
3.  設定 Salesforce 連接器提供裝載方案及資源群組的詳細資料，並選取 API 應用程式的名稱 ︰  
![][15]
4. 在 [封裝設定] 中設定您有興趣讀取/寫入的 Salesforce 實體。

完成上述步驟後，您現在即可建立 Salesforce 連接器 API 應用程式。


## 建立邏輯應用程式
讓我們建立一個可在 Salesforce 中建立帳戶，並更新相同帳戶之帳單寄送地址詳細資料的簡單邏輯應用程式。

1.  登入 Azure 入口網站，然後按一下 [新增]-> [Web + 行動]-> [邏輯應用程式]:  
![][1]

2.  在 [建立邏輯應用程式] 頁面中，提供必要的詳細資料，例如名稱、 應用程式服務方案和位置 ︰  
![][2]

3.  按一下 [觸發程序和動作]。 邏輯應用程式編輯器隨即開啟 ︰  
![][3]

4.  選取 [手動執行此邏輯] 這表示，此邏輯應用程式可以叫用僅以手動方式 ︰  
![][4]

5.  展開資源庫中的 [此資源群組中的 API 應用程式] 來查看所有可用的 API 應用程式。 從圖庫中選取 [Salesforce]，[Salesforce 連接器] 會被新增至流程 ︰  
![][5]

8.  若要授權邏輯應用程式存取您的 Salesforce 帳戶，請按一下 [授權] 以提供 Salesforce 登入認證 ︰  
![][6]

9.  將您重新導向至 Salesforce 登入頁面，您可以使用 Salesforce 認證進行驗證 ︰  
![][7]  
![][8]

10. 授權完成後，會顯示所有的動作 ︰  
![][9]

11. 選取 [建立帳戶] 動作，而輸入的參數會顯示 ︰  
![][10]

12. 提供 [帳戶名稱]，然後按一下 ✓ ︰  
![][11]

13. 從資源庫的 [最近使用的] 區段中選取 [Salesforce 連接器]，即會新增 Salesforce 動作。

14. 從動作和更新帳戶動作會顯示輸入的參數的清單中選取 [更新帳戶]:  
![][12]

15. 按一下旁邊的 [+' ' 記錄識別碼] 中挑選識別碼值，從 [建立帳戶] 動作的輸出 ︰  
![][13]

16. 提供帳單寄送街道、 帳單寄送縣 （市）、 帳單狀態和帳單寄送郵遞區號值，然後按一下 ✓ ︰  
![][14]

17. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。 完成建立大約需要 30 秒的時間。

18. 瀏覽剛建立的邏輯應用程式，然後按一下 [執行] 以啟動該應用程式。

19. 您可以檢查 Salesforce 帳戶中是否已建立名為 'Contoso' 的新帳戶。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png


