<properties 
    pageTitle="教學課程：Azure Active Directory 與 Igloo Software 整合 | Microsoft Azure" 
    description="了解如何使用 Igloo Software 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="prasannas"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="12/04/2015" 
    ms.author="jeedes" />


# 教學課程：Azure Active Directory 與 Igloo Software 整合

本教學課程的目的是要示範 Azure 與 Igloo Software 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   [Igloo Software](http://www.igloosoftware.com/) 單一登入啟用的訂閱

完成本教學課程之後, 您已指派給 Igloo Software 的 Azure AD 使用者將能夠登入位於您 Igloo Software 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Igloo Software 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenario")
## 啟用 Igloo Software 的應用程式整合

本節的目的是要說明如何啟用 Igloo Software 的應用程式整合。

### 若要啟用 Igloo Software 的應用程式整合，請執行下列步驟：

1.  在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Add an application from gallerry")

6.  在搜尋方塊****中，輸入 **Igloo Software**。

    ![應用程式資源庫](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Application Gallery")

7.  在結果窗格中，選取 [Igloo Software]****，然後按一下 [完成]**** 以新增應用程式。

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Igloo Software 中進行驗證。  
在此程序中，您需要上傳 Base-64 編碼憑證到您的 Central Desktop 租用戶。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure 傳統入口網站上 **Igloo Software** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Igloo Software** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Igloo 軟體登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.igloocommunities.com/?signin*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configure App URL")

4.  在 **Igloo software 設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Igloo Software 公司網站。

6.  移至 [控制台]****。

    ![控制台](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Control Panel")

7.  在 [成員資格]**** 索引標籤中，按一下 [登入設定]****。

    ![登入設定](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Sign in Settings")

8.  在 [SAML 設定] 區段，按一下 [設定 SAML 驗證]****。

    ![SAML 組態](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML Configuration")

9.  在 [一般設定]**** 區段，執行下列步驟：

    ![一般設定](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "General Configuration")

    1.  在 [連接名稱]**** 文字方塊中，輸入組態的自訂名稱。
    2.  在 Azure 傳統入口網站中的 [設定在 Igloo Software 單一登入]**** 對話方塊頁面上，複製 [遠端登入 URL]**** 值，然後將它貼至 [IdP 登入 URL]**** 文字方塊中。
    3.  在 Azure 傳統入口網站中的 [設定在 Igloo Software 單一登入]**** 對話方塊頁面上，複製 [遠端登出 URL]**** 值，然後將它貼至 [IdP 登出 URL]**** 文字方塊中。
    4.  為 [登出回應與要求 HTTP 類型]**** 選取 [POST]****。
    5.  從下載的憑證建立文字檔。
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  從您文字檔版本的憑證中移除第一行和最後一行，複製剩下的憑證文字，然後貼到 [公開憑證]**** 文字方塊。

10. 在 [回應與驗證設定]****，執行以下步驟：

    ![回應與驗證組態](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Response and Authentication Configuration")

    1.  在 [身分識別提供者]****，選取 [Microsoft ADFS]****。
    2.  在 [識別元類型]****，選取 [電子郵件地址]****。
    3.  在 [電子郵件屬性]**** 文字方塊中，輸入**電子郵件地址**。
    4.  在 [名字屬性]**** 文字方塊中，輸入**名字**。
    5.  在 [姓氏屬性]**** 文字方塊中，輸入**姓氏**。

11. 執行以下步驟來完成設定：

    ![登入時建立使用者](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "User creation on Sign in")

    1.  在 [登入時建立使用者]****，選取 [使用者登入您的網站時建立新的使用者]****。
    2.  在 [登入設定]****，選取 [在「登入」畫面使用 SAML 按鈕]****。
    3.  按一下 [儲存]****。

12. 在 Azure 傳統入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configure Single Sign-On")
## 設定使用者佈建

沒有動作項目可讓您設定  Igloo Software 使用者佈建。  
當已指派的使用者透過存取面板嘗試登入 Igloo Software 時，Igloo Software 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Igloo Software 會自動予以建立。
## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要指派使用者給 Igloo Software，請執行下列步驟：

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在 **Igloo Software * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




