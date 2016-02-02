<properties 
    pageTitle="教學課程：Azure Active Directory 與 Envoy 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Envoy，來啟用單一登入、 自動化佈建和更多功能!" 
    services="active-directory" 
    authors="MarkusVi"  
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


# 教學課程：Azure Active Directory 與 Envoy 整合

本教學課程的目的是要示範 Azure 與 Envoy 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Envoy 租用戶

完成本教學課程之後, 您已指派給 Envoy 的 Azure AD 使用者將能夠登入位於您 Envoy 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Envoy 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")
## 啟用 Envoy 的應用程式整合

本節的目的是要說明如何啟用 Envoy 的應用程式整合。

### 若要啟用 Envoy 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **Envoy**。

    ![應用程式庫](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")

7.  在結果窗格中，選取 [Envoy]****，然後按一下 [完成]**** 以加入應用程式。

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Envoy。  
設定 Envoy 的單一登入需要您從憑證擷取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Envoy** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")

2.  在 **您希望使用者如何登入 Envoy** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Envoy 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」 * https://\<tenant-name\>。Envoy.com* 」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")

4.  在 **Envoy 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\Envoy.cer**。

    ![設定單一登入](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Envoy 公司網站。

6.  在頂端的工具列中，按一下 [設定]****。

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  按一下 [公司]****。

    ![公司](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")

8.  按一下 [SAML]****。

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  在 [SAML 驗證]**** 組態區段中，執行下列步驟：

    ![SAML 驗證](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")
    >[AZURE.NOTE] 總部位置識別碼的值是由應用程式自動產生。

    1.  從匯出的憑證複製**指紋**值，然後將它貼入 [指紋]**** 文字方塊。
        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    2.  在 Azure 入口網站的 [在 Envoy 設定單一登入]**** 對話頁面上，複製 **SAML SSO URL** 值，然後將它貼至 [識別提供者 HTTP SAML URL]**** 文字方塊中。
    3.  按一下 [儲存變更]****。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
## 設定使用者佈建

沒有動作項目可讓您設定 Envoy 使用者佈建。  
當指派的使用者透過存取面板嘗試登入 Envoy 時，Envoy 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Envoy 會自動予以建立。
## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 Envoy，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Envoy * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




