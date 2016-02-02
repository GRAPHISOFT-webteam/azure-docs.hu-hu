<properties 
    pageTitle="教學課程：Azure Active Directory 與 Coupa 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Coupa，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 Coupa 整合

本教學課程的目的是要示範 Azure 與 Coupa 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 Coupa 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Coupa 的 Azure AD 使用者將能夠登入應用程式使用的單一登入 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Coupa 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")
## 啟用 Coupa 的應用程式整合

本節的目的是要說明如何啟用 Coupa 的應用程式整合。

### 若要啟用 Coupa 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-coupa-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-coupa-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入**Coupa**。

    ![應用程式資源庫](./media/active-directory-saas-coupa-tutorial/IC791898.png "Application Gallery")

7.  在結果窗格中，選取 [Coupa]****，然後按一下 [完成]**** 來加入應用程式。

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Coupa。  
設定 Coupa 的單一登入需要您從憑證擷取憑證指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

### 若要設定單一登入，請執行下列步驟：

1.  以系統管理員身分登入您的 Coupa 公司網站。

2.  移至 **安裝 \ > 安全性控制**。

    ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")

3.  若要將 Coupa 中繼資料檔案下載至您的電腦，按一下 [下載和匯入預存程序的中繼資料]****。

    ![Coupa 預存程序中繼資料](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")

4.  在不同的瀏覽器視窗中，登入 Azure Active Directory 入口網站。

5.  在 **Coupa** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configure Single Sign-On")

6.  在 **您希望使用者如何登入 Coupa** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configure Single Sign-On")

7.  在 **設定應用程式 URL** 頁面上，執行下列步驟:

    ![設定應用程式 URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configure App URL")

    1.  在 **登入 URL** 文字方塊中，輸入 URL，讓使用者用來登入 Coupa 應用程式 (例如: 「*http://company.Coupa.com*」)。
    2.  開啟您已下載的 Coupa 中繼資料檔案，然後複製 **AssertionConsumerService index/URL**。
    3.  在 [Coupa 回覆 URL]**** 文字方塊中，貼上 **AssertionConsumerService index/URL** 值。
    4.  按 [下一步]****。

8.  在 **Coupa 在設定單一登入** ] 頁面上，來下載中繼資料檔中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的檔案。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configure Single Sign-On")

9.  在 Coupa 公司網站中，請移至 **安裝 \ > 安全性控制**。

    ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")

10. 在 [使用 Coupa 認證登入]**** 區段中，執行下列步驟：

    ![使用 Coupa 認證登入](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials")

    1.  選取 [使用 SAML 登入]****。
    2.  按一下 [瀏覽]**** 來上傳您下載的 Azure Active Directory 中繼資料檔。
    3.  按一下 [儲存]****。

11. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 Coupa，則必須將他們佈建到 Coupa。  
Coupa 需以手動的方式佈建。

### 若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Coupa** 公司網站。

2.  在頂端的功能表中，按一下 [設定]****，然後按一下 [使用者]****。

    ![使用者](./media/active-directory-saas-coupa-tutorial/IC791908.png "Users")

3.  按一下 [建立]****。

    ![建立使用者](./media/active-directory-saas-coupa-tutorial/IC791909.png "Create Users")

4.  在 [使用者建立]**** 區段中，執行下列步驟：

    ![使用者詳細資料](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")

    1.  在相關的文字方塊中，輸入您要佈建之有效 Active Directory 帳戶的 [登入]****、[名字]****、[姓氏]****、[單一登入識別碼]****、[電子郵件]**** 屬性。
    2.  按一下 [建立]****。

    >[AZURE.NOTE] Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[AZURE.NOTE] 您可以使用任何其他的 Coupa 使用者帳戶建立工具或 Coupa 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 Coupa，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Coupa * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-coupa-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。





