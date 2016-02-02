<properties 
    pageTitle="教學課程：Azure Active Directory 與 ThousandEyes 整合 | Microsoft Azure" 
    description="了解如何使用 ThousandEyes 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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


# 教學課程：Azure Active Directory 與 ThousandEyes 整合

本教學課程的目的是示範如何設定 Azure Active Directory (Azure AD) 與 ThousandEyes 之間的單一登入。

本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 ThousandEyes 單一登入的訂用帳戶

完成本教學課程之後，或是使用 AAD 存取面板，您指派給 ThousandEyes 存取的 AAD 使用者就能夠單一登入您 ThousandEyes 公司網站 (服務提供者起始登入) 的應用程式。

1.  啟用 ThousandEyes 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

## 啟用 ThousandEyes 的應用程式整合

本節的目的是要說明如何啟用 ThousandEyes 的應用程式整合。

### 若要啟用 ThousandEyes 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]**** 中，輸入 **ThousandEyes**。

    ![應用程式庫](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")

7.  在結果窗格中，選取 [ThousandEyes]****，然後按一下 [完成]**** 以新增應用程式。

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## 設定單一登入

本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶驗證至 ThousandEyes。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ThousandEyes** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")

2.  在 **您希望使用者如何登入 ThousandEyes** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")

3.  在 **設定應用程式 URL** 頁面上，於 **ThousandEyes 登入 URL** 文字方塊中，輸入 URL 的使用者用來登入 ThousandEyes 的應用程式 (例如: 「*https://app.thousandeyes.com/login/sso*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4.  在 **設定單一登入在 ThousandEyes** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將憑證檔案儲存在本機電腦。

    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **ThousandEyes** 公司網站。

6.  在頂端的功能表中，按一下 [設定]****。

    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7.  按一下 [帳戶]****

    ![帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

8.  按一下 [安全性與驗證]**** 索引標籤。

    ![安全性和驗證](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")

9.  在 [設定單一登入]**** 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")

    1.  選取 [啟用單一登入]****。
    2.  在 Microsoft Azure 入口網站上 **ThousandEyes 在設定單一登入** 頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入頁面 URL** 文字方塊。
    3.  在 Microsoft Azure 入口網站上 **ThousandEyes 在設定單一登入** 頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出頁面 URL** 文字方塊。
    4.  在 Microsoft Azure 入口網站上 **ThousandEyes 在設定單一登入** 頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **身分識別提供者簽發者** 文字方塊。
    5.  在 [識別提供者憑證]**** 按一下 [選擇檔案]****，然後上傳您已從 Microsoft Azure 入口網站下載的憑證。
    6.  按一下 [儲存]****。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 ThousandEyes，則必須將他們佈建到 ThousandEyes。  
ThousandEyes 需以手動的方式佈建。

### 若要佈建使用者帳戶到 ThousandEyes，請執行下列步驟：

1.  以系統管理員身分登入您的 ThousandEyes 公司網站。

2.  按一下 [設定]****。

    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3.  按一下 [帳戶]****。

    ![帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4.  按一下 [帳戶和使用者]**** 索引標籤。

    ![帳戶和使用者](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")

5.  在 [加入使用者和帳戶]**** 區段中，執行下列步驟：

    ![新增使用者帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")

    1.  在相關的文字方塊中，輸入您要佈建之有效 Active Directory 帳戶的 [名稱]****、[電子郵件]**** 及其他詳細資料。
    2.  按一下 [加入使用者至帳戶]**** 按鈕。
        >[AZURE.NOTE] AAD 帳戶的持有者會收到一封包含連結的電子郵件，以進行確認並啟用帳戶。

>[AZURE.NOTE] 您可以使用任何其他的 ThousandEyes 使用者帳戶建立工具或 ThousandEyes 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 ThousandEyes，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **ThousandEyes** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。





