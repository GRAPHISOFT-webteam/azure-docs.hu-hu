<properties 
    pageTitle="教學課程：Azure Active Directory 與 InsideView 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 InsideView，來啟用單一登入、 自動化佈建和更多功能!" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# 教學課程：Azure Active Directory 與 InsideView 整合

本教學課程的目的是要示範 Azure 與 InsideView 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   InsideView 租用戶

完成本教學課程之後, 您已指派給 InsideView 的 Azure AD 使用者將能夠登入位於您 InsideView 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 InsideView 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")
## 啟用 InsideView 的應用程式整合

本節的目的是要說明如何啟用 InsideView 的應用程式整合。

### 若要啟用 InsideView 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")

6.  在搜尋方塊****中，輸入 **InsideView**。

    ![應用程式資源庫](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")

7.  在結果窗格中，選取 [InsideView]****，然後按一下 [完成]**** 以加入應用程式。

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 InsideView 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **InsideView** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")

2.  在 **您希望使用者如何登入 InsideView** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")

3.  在 **設定應用程式 URL** 頁面上，於 **InsideView 回覆 URL** 文字方塊中，輸入您的 InsideView SSO URL (例如: `https://my.insideview.com/iv/<STS > /login.iv`)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")

4.  在 **設定單一登入在 InsideView** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 InsideView 公司網站。

6.  在頂端的工具列中，按一下 [系統管理員]****，[單一登入設定設定]****，然後按一下 [新增 SAML]****。

    ![SAML 單一登入設定](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")

7.  在 [加入新的 SAML]**** 區段中，執行下列步驟：

    ![加入新的 SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")

    1.  在 [STS 名稱]**** 文字方塊中，輸入您的組態名稱。
    2.  在 Azure 入口網站中的 [設定在 InsideView 單一登入]**** 對話方塊頁面，複製 [服務提供者 (SP) 啟始的端點]**** 值，然後貼到 [SamlP/WS-Fed 主動端點]**** 文字方塊。
    3.  從您下載的憑證建立「Base-64 編碼」****檔案。
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您的 Base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼至 [STS 憑證]**** 文字方塊中。
    5.  在 **Crm 使用者識別碼對應** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    6.  在 **Crm 電子郵件將對應** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    7.  在 **Crm 名字對應** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    8.  在 **Crm lastName 對應** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    9.  按一下 [儲存]****。

8.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 InsideView，則必須將他們佈建到 InsideView。  
在 InsideView 的情況下，佈建是手動工作。

若要取得使用者或連絡人在 InsideView 中建立，請連絡客戶成功管理員或電子郵件傳送給 **support@insideview.com**
>[AZURE.NOTE] 您可以使用任何其他的 InsideView 使用者帳戶建立工具或 InsideView 提供的 API 來佈建 Azure AD 使用者帳戶。

## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要指派使用者給 InsideView，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **InsideView * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




