<properties 
    pageTitle="教學課程：Azure Active Directory 與 Replicon 整合 | Microsoft Azure" 
    description="了解如何使用 Replicon 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Replicon 整合
  
本教學課程的目的是要示範 Azure 與 Replicon 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Replicon 租用戶
  
完成此教學課程之後, 您已指派給 Replicon 的 Azure AD 使用者將能夠登入您的 Replicon 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Replicon 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")
##啟用 Replicon 的應用程式整合
  
本節的目的是要說明如何啟用 Replicon 的應用程式整合。

###若要啟用 Replicon 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-replicon-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-replicon-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-replicon-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Replicon**。

    ![應用程式資源庫](./media/active-directory-saas-replicon-tutorial/IC777799.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Replicon**, ，然後按一下 [ **完成** 加入應用程式。

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Replicon。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Replicon** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Replicon** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟 ︰

    ![設定應用程式 URL](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configure app URL")

    1.  在 **Replicon 登入 URL** 文字方塊中，輸入您的 Replicon 租用戶 URL (例如 ︰ *https://na2.replicon.com/company/saml2/sp-sso/post*)。
    2.  在 **Replicon 回覆 URL** 文字方塊中，輸入您的 Replicon **AssertionConsumerService** URL (例如 ︰ *https://global.replicon.com/ ！ saml2/公司/sso/post/*)。  

        >[AZURE.NOTE] 您可以從 Replicon 中繼資料取得 URL:
        **https://global.replicon.com/ ！ / saml2/\< YourCompanyKey\ >**。

    3.  按一下 [ **下一步**

4.  在 **Replicon 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後儲存您的電腦上的 [中繼資料。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Replicon 公司網站。

6.  若要設定 SAML 2.0，請執行下列步驟：

    ![啟用 SAML 驗證](./media/active-directory-saas-replicon-tutorial/IC777805.png "Enable SAML authentication")

    1.  若要顯示 **EnableSAML Authentication2** ] 對話方塊中，下列附加至您的 URL，公司金鑰 ︰  
        **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
        下圖顯示完整的 URL 結構描述 ︰  
        **https://na2.replicon.com/ \<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  按一下 [ **+** 展開 **v20Configuration** 一節。
    3.  按一下 [ **+** 展開 **metaDataConfiguration** 一節。
    4.  按一下 [ **選擇檔案**, ，以選取身分識別提供者中繼資料 XML 檔案，並按一下 [ **提交**。

7.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configure single sign-on")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Replicon，則必須將他們佈建至 Replicon。  
在 Replicon 的情況下，需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Web 瀏覽器視窗中，以系統管理員身分登入您的 Replicon 公司網站。

2.  移至 **管理 \ > 使用者**。

    ![使用者](./media/active-directory-saas-replicon-tutorial/IC777806.png "Users")

3.  按一下 [ **+ 新增使用者**。

    ![新增使用者](./media/active-directory-saas-replicon-tutorial/IC777807.png "Add User")

4.  在 **使用者設定檔** 區段中，執行下列步驟 ︰

    ![使用者設定檔](./media/active-directory-saas-replicon-tutorial/IC777808.png "User profile")

    1.  在 **登入名稱** 文字方塊中，您想要佈建的 Azure AD 使用者輸入的 Azure AD 電子郵件地址。
    2.  做為 **驗證類型**, ，請選取 **SSO**。
    3.  在 **部門** 文字方塊中，輸入使用者的部門。
    4.  做為 **的 Employee 型別**, ，請選取 **管理員**。
    5.  按一下 [ **儲存使用者設定檔**。

>[AZURE.NOTE]您可以使用任何其他 Replicon 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Replicon。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Replicon，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Replicon * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-replicon-tutorial/IC777809.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-replicon-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

