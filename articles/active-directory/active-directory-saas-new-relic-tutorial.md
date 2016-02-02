<properties 
    pageTitle="教學課程：Azure Active Directory 與 New Relic 整合 | Microsoft Azure" 
    description="了解如何使用 New Relic 與 Azure Active Directory，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 New Relic 整合

本教學課程的目的是要示範 Azure Active Directory 與 New Relic 的整合。

本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 New Relic 單一登入的訂閱

完成本教學課程之後，您指派給 New Relic 的 Azure Active Directory 使用者將能夠使用 [AAD 存取面板] 進行單一登入。

1.  啟用 New Relic 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")
## 啟用 New Relic 的應用程式整合

本節的目的是要說明如何啟用 New Relic 的應用程式整合。

### 若要啟用 New Relic 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中輸入 **New Relic**。

    ![應用程式資源庫](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")

7.  在結果窗格中，選取 [New Relic]****，然後按一下 [完成]**** 以新增應用程式。

    ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
## 設定單一登入

本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶在 New Relic 中進行驗證。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **New Relic** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 New Relic** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **新 Relic 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您的 New Relic 應用程式，然後按一下 **下一步**。

    應用程式 URL 是您的 New Relic 租用戶 URL (例如: *https://rpm.newrelic.com*):

    ![設定應用程式 URL](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")

4.  在 **設定單一登入在 New Relic** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將憑證檔案儲存在本機電腦。

    ![設定單一登入](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 **New Relic** 公司網站。

6.  在頂端的功能表中，按一下 [帳戶設定]****。

    ![帳戶設定](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")

7.  按一下 [安全性及驗證]**** 索引標籤，然後再按一下 [單一登入] **** 索引標籤。

    ![單一登入](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")

8.  在 SAML 對話方塊頁面上，執行下列步驟：

    ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  按一下 [選擇檔案]**** 上傳已下載的 Azure Active Directory 憑證。
    2.  在 Microsoft Azure 入口網站上 **設定單一登入在 New Relic** 頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **遠端登入 URL** 文字方塊。
    3.  在 Microsoft Azure 入口網站上 **設定單一登入在 New Relic** 頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出登陸 URL** 文字方塊。
    4.  按一下 [儲存我的變更]****。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
## 設定使用者佈建

為了讓 Azure Active Directory 使用者登入 New Relic，他們必須佈建到 New Relic 中。  
在 New Relic 的情況下，需以手動方式佈建。

### 若要將使用者帳戶佈建到 New Relic，請執行下列步驟：

1.  以系統管理員身分登入您的 **New Relic** 公司網站。

2.  在頂端的功能表中，按一下 [帳戶設定]****。

    ![帳戶設定](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")

3.  在左邊的**帳戶**窗格中按一下 [摘要]****，然後按一下 [新增使用者]****。

    ![帳戶設定](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")

4.  在 [作用中使用者]**** 對話方塊上，執行下列步驟：

    ![作用中使用者](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")

    1.  在 [電子郵件]**** 文字方塊中輸入您想要佈建的有效 Azure Active Directory 使用者電子郵件地址。
    2.  **角色**請選取 [使用者]****。
    3.  按一下 [新增此使用者]****。

>[AZURE.NOTE]您可以使用任何其他的 New Relic 使用者帳戶建立工具或 New Relic 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要將使用者指派給 New Relic，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **New Relic** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。









