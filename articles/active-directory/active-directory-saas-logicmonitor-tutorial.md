<properties 
    pageTitle="教學課程：Azure Active Directory 與 LogicMonitor 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 LogicMonitor，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 LogicMonitor 整合

本教學課程的目的是要示範 Azure 與 LogicMonitor 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   LogicMonitor 租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 LogicMonitor 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")
## 啟用 LogicMonitor 的應用程式整合

本節的目的是要說明如何啟用 LogicMonitor 的應用程式整合。

### 若要啟用 LogicMonitor 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")

6.  在搜尋方塊****中，輸入 **logicmonitor**。

    ![應用程式資源庫](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")

7.  在結果窗格中，選取 [LogicMonitor]****，然後按一下 [完成]**** 以加入應用程式。

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 LogicMonitor 中進行驗證。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **LogicMonitor * * 應用程式整合頁面上，按一下 [* * 設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 LogicMonitor** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")

3.  上 **設定應用程式 URL** 頁面上，於 **登入 URL** 文字方塊中，輸入您的 URL，讓使用者中用來登入 LogicMonitor \ (電子、 g、: 「*http://company.logicmonitor.com*"\)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")

4.  在 **LogicMonitor 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後將它儲存在電腦上。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")

5.  以系統管理員身分登入您的 **LogicMonitor** 公司網站。

6.  在頂端的功能表中，按一下 [設定]****。

    ![設定](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")

7.  在左側導覽列中按一下 [單一登入]****

    ![單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  在 [單一登入 (SSO) 設定]**** 區段中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")

    1.  選取 [啟用單一登入]****。
    2.  在 [預設角色指派]**** 中選取 [唯讀]****。
    3.  在記事本中開啟下載的中繼資料檔，然後將檔案內容貼到 [識別提供者中繼資料]**** 文字方塊中。
    4.  按一下 [儲存變更]****。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 AAD 使用者能夠登入，必須使用他們的 Azure Active Directory 使用者名稱將他們佈建到 LogicMonitor 應用程式。

### 若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 LogicMonitor 公司網站。

2.  在頂端的功能表中，按一下 [設定]****，然後按一下 [角色與使用者]****。

    ![角色和使用者](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")

3.  按一下 [新增]****。

4.  在 [新增帳戶]**** 區段中，執行下列步驟：

    ![新增帳戶](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")

    1.  在相關的文字方塊中輸入您想要佈建之 Azure Active Directory 使用者的 [使用者名稱]****、[電子郵件]****、[密碼]**** 及 [重新輸入密碼]**** 值。
    2.  選取 [角色]****、[檢視權限]**** 及 [狀態]****。
    3.  按一下 [提交]****。

>[AZURE.NOTE]您可以使用任何其他的 LogicMonitor 使用者帳戶建立工具或 LogicMonitor 提供的 API，佈建 Azure Active Directory 使用者帳戶。

## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要指派使用者給 LogicMonitor，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **LogicMonitor** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。









