<properties 
    pageTitle="教學課程：Azure AD 與 BlueJeans 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 BlueJeans，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure AD 與 BlueJeans 整合

本教學課程的目的是要示範 Azure 與 BlueJeans 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 BlueJeans 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 BlueJeans 的 Azure AD 使用者將能夠登入位於您 BlueJeans 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 BlueJeans 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")
## 啟用 BlueJeans 的應用程式整合

本節的目的是要說明如何啟用 BlueJeans 的應用程式整合。

### 若要啟用 BlueJeans 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **BlueJeans**。

    ![應用程式庫](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")

7.  在結果窗格中，選取 [BlueJeans]****，然後按一下 [完成]**** 以加入應用程式。

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 BlueJeans。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **BlueJeans** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 BlueJeans** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **BlueJeans 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.BlueJeans.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")

4.  在 **設定單一登入在 BlueJeans** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **BlueJeans** 公司網站。

6.  移至 **ADMIN \ > 群組設定 \ > 安全性**。

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  在 [安全性]**** 區段中，執行下列步驟：

    ![SAML 單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")

    1.  選取 [SAML 單一登入]****。
    2.  選取 [啟用自動佈建]****。

8.  繼續執行下列步驟：

    ![憑證路徑](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")

    1.  按一下 [選擇檔案]****，然後上傳下載的憑證。
    2.  在 Azure 入口網站的 [在 BlueJeans 設定單一登入]**** 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [登入 URL]**** 文字方塊中。
    3.  在 Azure 入口網站的 [在 BlueJeans 設定單一登入]**** 對話頁面上，複製**變更密碼 URL** 值，然後將它貼至 [密碼變更 URL]**** 文字方塊中。
    4.  在 Azure 入口網站的 [設定在 BlueJeans 單一登入]**** 對話頁面上，複製**遠端登出 URL** 值，然後將它貼至 [登出 URL]**** 文字方塊中。

9.  繼續執行下列步驟：

    ![儲存變更](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")

    1.  在 **使用者識別碼** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    2.  在 **電子郵件** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    3.  按一下 [儲存變更]****。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 BlueJeans，必須將他們佈建到 BlueJeans。  
BlueJeans 需以手動方式佈建。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **BlueJeans** 公司網站。

2.  移至 **ADMIN \ > 管理使用者 \ > 將使用者加入**。

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
    >[AZURE.IMPORTANT] 只有在未核取 [安全性]**** 索引標籤中的 [啟用自動佈建]**** 時，才能使用 [加入使用者]**** 索引標籤。

3.  在 [加入使用者]**** 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [BlueJeans 使用者名稱]****、[電子郵件地址]****、[BlueJeans 會議識別碼]****、[仲裁者密碼]****、[全名]****、[公司]****。
    2.  按一下 [加入使用者]****。

>[AZURE.NOTE] 您可以使用任何其他的 BlueJeans 使用者帳戶建立工具或 BlueJeans 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 BlueJeans，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **BlueJeans * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。





