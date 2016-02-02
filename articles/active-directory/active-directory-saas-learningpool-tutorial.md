<properties 
    pageTitle="教學課程：Azure Active Directory 與 Learningpool 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Learningpool，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 Learningpool 整合

本教學課程的目的是要示範 Azure 與 Learningpool 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Learningpool 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Learningpool 的 Azure AD 使用者將能夠登入位於您 Learningpool 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Learningpool 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")
## 啟用 Learningpool 的應用程式整合

本節的目的是要說明如何啟用 Learningpool 的應用程式整合。

### 若要啟用 Learningpool 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")

6.  在搜尋方塊****中，輸入 **Learningpool**。

    ![應用程式資源庫](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")

7.  在結果窗格中，選取 [Learningpool]****，然後按一下 [完成]**** 以加入應用程式。

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Learningpool 中進行驗證。

Learningpool 應用程式需要特定格式的 SAML 判斷提示，需要您將自訂屬性對應加入到您的 **saml token 屬性**設定。  
以下螢幕擷取畫面顯示上述的範例。

![SAML Token 屬性](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Learningpool** 應用程式整合頁面上，在最上層功能表中按一下 [ **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")

2.  若要加入必要的屬性對應，請執行下列步驟：

    ###

    | 屬性名稱| 屬性值|
    |------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221| User.userprincipalname
    |-------------------------------|--------------------------|
     urn:oid:2.5.4.42| (user.givenName -eq "value")
    | urn:oid:0.9.2342.19200300.100.1.3| User.mail
    | urn:oid:2.5.4.4| User.surname

    1.  針對上表中的每個資料列，按一下 [加入使用者屬性]****。
    2.  在 [屬性名稱]**** 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 [屬性值]**** 清單中，選取該資料列所顯示的屬性名稱。
    4.  按一下 [完成]****。

3.  按一下 [套用變更]****。

4.  在您的瀏覽器中，按一下 [返回]**** 以再次開啟 [快速啟動]**** 對話方塊。

5.  按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")

6.  在 **您希望使用者如何登入 Learningpool** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")

7.  在 **設定應用程式 URL** 頁面上，於 **Learningpool 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您的 Learningpool 應用程式 (例如: 
https://parliament.preview.learningpool.com/auth/shibboleth/index.php)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")

8.  在 **Learningpool 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")

9.  將該中繼資料檔轉寄給 Learningpool 支援小組。
    >[AZURE.NOTE]單一登入必須由 Learningpool 支援小組啟用。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Learningpool，則必須將他們佈建到 Learningpool。

沒有動作項目可讓您設定 Learningpool 使用者佈建。  
使用者必須由您的 Learningpool 支援小組建立。
>[AZURE.NOTE]您可以使用任何其他的 Learningpool 使用者帳戶建立工具或 Learningpool 提供的 API，佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要指派使用者給 Learningpool，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Learningpool * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




