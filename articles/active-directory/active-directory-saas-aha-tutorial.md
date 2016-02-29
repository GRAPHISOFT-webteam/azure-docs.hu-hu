<properties 
    pageTitle="教學課程：Azure Active Directory 與 Aha! 整合 | Microsoft Azure" 
    description="了解如何使用 Aha! 使用 Azure Active Directory 來啟用單一登入、 自動化佈建和更多功能!" 
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

#教學課程：Azure Active Directory 與 Aha! 整合

本教學課程的目的是要示範 Azure 與 Aha! 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Aha! 啟用單一登入的訂閱

完成本教學課程之後, 的 Azure AD 使用者您已指派給 Aha! 都能夠使用單一登入應用程式位於您 Aha! 公司網站 (服務提供者起始登入)，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Aha! 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")
##啟用 Aha! 的應用程式整合

本節的目的是概述如何啟用 Aha! 的應用程式整合。

###若要啟用 Aha! 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-aha-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-aha-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Aha!**。

    ![應用程式庫](./media/active-directory-saas-aha-tutorial/IC798945.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Aha!**, ，然後按一下 [ **完成** 加入應用程式。

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##設定單一登入

本節目標在於簡述如何啟用使用者驗證至 Aha! 在 Azure AD SAML 通訊協定為基礎的同盟，使用其帳戶。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Aha!** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Aha!** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Aha!** 文字方塊中，輸入使用者用來登入 Aha! 應用程式 (例如: 「*https://company.aha.io/session/new*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-aha-tutorial/IC798948.png "Configure App URL")

4.  上 **設定單一登入在 Aha!** ] 頁面上，來下載中繼資料檔中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，登入您的 Aha! 以系統管理員身分的公司網站。

6.  在頂端功能表中，按一下 [ **設定**。

    ![設定](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")

7.  按一下 [ **帳戶**。

    ![設定檔](./media/active-directory-saas-aha-tutorial/IC798951.png "Profile")

8.  按一下 [ **安全性與單一登入**。

    ![安全性和單一登入](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")

9.  在 **單一登入** ] 區段中，為 **身分識別提供者**, ，請選取 **SAML2.0**。

    ![安全性和單一登入](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")

10. 在 **單一登入** 組態頁面上，執行下列步驟:

    ![單一登入](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")

    1.  在 **名稱** 文字方塊中，輸入您的組態名稱。
    2.  如 **設定使用**, ，請選取 **中繼資料檔案**。
    3.  若要上傳您下載的中繼資料檔案，請按一下 [ **瀏覽**。
    4.  按一下 [ **更新**。

11. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Aha!，必須將他們佈建到 Aha!。  
Aha! 的佈建是自動化工作。  
沒有您適用的動作項目。
  
在第一次單一登入嘗試時會自動建立使用者 (如有必要)。

>[AZURE.NOTE] 您可以使用任何其他 Aha! 使用者帳戶建立工具或 Api 提供的 Aha! 若要佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Aha!，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Aha! * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-aha-tutorial/IC798956.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

