<properties 
    pageTitle="教學課程：Azure Active Directory 與 RunMyProcess 整合 | Microsoft Azure" 
    description="了解如何使用 RunMyProcess 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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


# 教學課程：Azure Active Directory 與 RunMyProcess 整合

本教學課程的目的是要示範 Azure 與 RunMyProcess 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   RunMyProcess 租用戶

完成本教學課程之後, 您已指派給 RunMyProcess 的 Azure AD 使用者將能夠登入位於您 RunMyProcess 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 RunMyProcess 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Scenario")
## 啟用 RunMyProcess 的應用程式整合

本節的目的是要說明如何啟用 RunMyProcess 的應用程式整合。

### 若要啟用 RunMyProcess 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]**** 中，輸入 [RunMyProcess]****。

    ![應用程式資源庫](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Application Gallery")

7.  在結果窗格中選取 [RunMyProcess]****，然後按一下 [完成]**** 來新增應用程式。

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 RunMyProcess 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **RunMyProcess** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 RunMyProcess** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **RunMyProcess 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*http://company.runmyprocess.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configure App URL")

4.  在 **設定單一登入在 RunMyProcess** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 RunMyProcess 公司網站。

6.  移至 **帳戶 \ > 組態**。

    ![帳戶](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Account")

7.  按一下 [驗證方法]**** 索引標籤。

8.  在 [驗證方法]**** 區段中，執行下列步驟：

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  在 [方法]****，選取 [使用 Samlv2 進行 SSO]****。
    2.  在 Azure 入口網站中的 [設定在 RunMyProcess 單一登入]**** 對話頁面上，複製 [SAML SSO URL]**** 值，然後將其貼至 [SSO 重新導向]**** 文字方塊中。
    3.  在 Azure 入口網站的 [設定在 RunMyProcess 單一登入]**** 對話頁面上，複製 [單一登出服務 URL]**** 值，然後將其貼至 [單一登出重新導向]**** 文字方塊中。
    4.  在 [名稱識別碼格式]**** 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**。
    5.  從您下載的憑證建立「Base-64 編碼」****檔案。
        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 Base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [憑證]**** 文字方塊中。
    7.  按一下 [儲存]****。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 RunMyProcess，必須將他們佈建到 RunMyProcess。  
RunMyProcess 需以手動的方式佈建。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **RunMyProcess** 公司網站。

2.  移至 **帳戶 \ > 使用者**, ，然後按一下 [ **新使用者**。

    ![新使用者](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "New User")

3.  在 [使用者設定]**** 區段中，執行下列步驟：

    ![設定檔](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profile")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [名稱]**** 與 [電子郵件]****。
    2.  選取 [IDE 語言]****、[語言]**** 和 [設定檔]****。
    3.  選取 [傳送帳戶建立電子郵件給我]****。
    4.  按一下 [儲存]****。

>[AZURE.NOTE]您可以使用任何其他的 RunMyProcess 使用者帳戶建立工具或 RunMyProcess 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 RunMyProcess，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **RunMyProcess * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




