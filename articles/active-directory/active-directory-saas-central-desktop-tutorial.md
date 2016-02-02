<properties 
    pageTitle="教學課程：Azure Active Directory 與 Central Desktop 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Central Desktop，來啟用單一登入、 自動化佈建和更多功能!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# 教學課程：Azure Active Directory 與 Central Desktop 整合

本教學課程的目的是要示範 Azure 與 Central Desktop 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Central Desktop 單一登入的訂用帳戶/Central Desktop 租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Central Desktop 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")
## 啟用 Central Desktop 的應用程式整合

本節的目的是要說明如何啟用 Central Desktop 的應用程式整合。

### 若要啟用 Central Desktop 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **Central Desktop**。

    ![應用程式資源庫](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")

7.  在結果窗格中，選取 [Central Desktop]****，然後按一下 [完成]**** 以加入應用程式。

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Central Desktop。  
在此程序中，您需要上傳 Base-64 編碼憑證到您的 Central Desktop 租用戶。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。



### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Central Desktop** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Central Desktop** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟，然後按 **下一步**:

    -   在 **中央桌面登入 URL** 文字方塊中，輸入您的 Central Desktop 租用戶 URL (例如: *http://contoso.centraldesktop.com*)。
    -   在 Central Desktop 回覆 URL] 文字方塊中輸入您的 Central Desktop AssertionConsumerService URL (例如: https://contoso.centraldesktop.com/saml2-assertion.php)。

    >[AZURE.NOTE] 您可以向 central desktop 中繼資料中的值 (例如: *http://contoso.centraldesktop.com*)。

    ![設定應用程式 URL](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")

4.  在 **在 Central Desktop 設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")

5.  登入您的 **Central Desktop** 租用戶。

6.  移至 [設定]****，按一下 [進階]****，然後按一下 [單一登入]****。

    ![設定 - 進階](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")

7.  在 [單一登入設定]**** 頁面上，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")

    1.  選取 [啟用 SAML v2 單一登入]****。
    2.  在 Azure AD 入口網站的 [在 Central Desktop 設定單一登入]**** 頁面上，複製 [簽發者 URL]**** 的值，然後將它貼到 [SSO URL]**** 文字方塊中。
    3.  在 Azure AD 入口網站的 [在 Central Desktop 設定單一登入]**** 頁面上，複製 [遠端登入 URL]**** 的值，然後將它貼到 [SSO 登入 URL]**** 文字方塊中。
    4.  在 Azure AD 入口網站的 [在 Central Desktop 設定單一登入]**** 頁面上，複製 [單一登出服務 URL]**** 的值，然後將它貼到 [SSO 登出 URL]**** 文字方塊中。

8.  在 [訊息簽章驗證方法]**** 區段中，執行下列步驟：

    ![訊息簽章驗證方法](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")

    1.  選取 [憑證]****。
    2.  從 [SSO 憑證]**** 清單中選取 [RSH SHA256]****。
    3.  從下載的憑證建立文字檔，複製文字檔的內容，然後將內容貼到 [SSO 憑證]**** 欄位中。
        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    4.  選取 [顯示 SAMLv2 登入頁面的連結]****。

9.  按一下 [更新]****。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
## 設定使用者佈建

AAD 使用者必須先佈建到 Central Desktop 應用程式，才可以登入。 本節說明如何在 Central Desktop 中建立 AAD 使用者帳戶。

### 若要將使用者帳戶佈建到 Central Desktop：

1.  登入您的 Central Desktop 租用戶。

2.  移至 **人 \ > 內部成員**。

3.  按一下 [加入內部成員]****。

    ![人員](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")

4.  在 [新成員的電子郵件地址]**** 文字方塊中，輸入您想要佈建的 AAD 帳戶，然後按 [下一步]****。

    ![新成員的電子郵件地址](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")

5.  按一下 [加入內部成員]****。

    ![加入內部成員](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
    >[AZURE.NOTE] 您加入的使用者會收到一封包含確認連結的電子郵件，使用者必須按一下才能啟用帳戶。

>[AZURE.NOTE] 您可以使用任何其他的 Central Desktop 使用者帳戶建立工具或 Central Desktop 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 Central Desktop，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Central Desktop** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。





