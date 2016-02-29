<properties 
    pageTitle="教學課程：Azure Active Directory 與 Greenhouse 整合 | Microsoft Azure" 
    description="了解如何使用 Greenhouse 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Greenhouse 整合
  
本教學課程的目的是要示範 Azure 與 Greenhouse 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Greenhouse 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Greenhouse 的 Azure AD 使用者將能夠登入位於您 Greenhouse 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Greenhouse 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")
##啟用 Greenhouse 的應用程式整合
  
本節的目的是要說明如何啟用 Greenhouse 的應用程式整合。

###若要啟用 Greenhouse 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **greenhouse**。

    ![應用程式庫](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Greenhouse**, ，然後按一下 [ **完成** 加入應用程式。

    ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Greenhouse。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Greenhouse** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Greenhouse** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.greenhouse.io*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configure App URL")

4.  在 **Greenhouse 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")

5.  將該中繼資料檔轉寄給 Greenhouse 支援小組。

    >[AZURE.NOTE] 單一登入必須由 Greenhouse 支援小組啟用。

6.  在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configure single sign-on")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Greenhouse，則必須將他們佈建至 Greenhouse。  
在 Greenhouse 的情況下，需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Greenhouse** 系統管理員身分的公司網站。

2.  在頂端功能表中，按一下 [ **設定**, ，然後按一下 [ **使用者**。

    ![使用者](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")

3.  按一下 [ **新使用者**。

    ![新使用者](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")

4.  在 **新增使用者** 區段中，執行下列步驟:

    ![新增使用者](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")

    1.  在 **輸入使用者電子郵件** 文字方塊中，輸入您想要佈建的有效 Azure Active Directory 帳戶的電子郵件地址。
    2.  按一下 [ **儲存**。
        
        >[AZURE.NOTE] Azure Active Directory 帳戶持有者將會收到包含連結就會生效，先確認其帳戶的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Greenhouse 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Greenhouse。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Greenhouse，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Greenhouse * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assign users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
