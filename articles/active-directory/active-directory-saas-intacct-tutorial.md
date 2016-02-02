<properties 
    pageTitle="教學課程：Azure Active Directory 與 Intacct 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Intacct，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 Intacct 整合

本教學課程的目的是要示範 Azure 與 Intacct 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Intacct 租用戶

完成本教學課程之後, 您已指派給 Intacct 的 Azure AD 使用者將能夠登入位於您 Intacct 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Intacct 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")
## 啟用 Intacct 的應用程式整合

本節的目的是要說明如何啟用 Intacct 的應用程式整合。

### 若要啟用 Intacct 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749322.png "Add an application from gallerry")

6.  在搜尋方塊****中，輸入 **Intacct**。

    ![應用程式資源庫](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")

7.  在結果窗格中，選取 [Intacct]****，然後按一下 [完成]**** 以加入應用程式。

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Intacct 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Intacct** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Intacct** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Intacct 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://Intacct.com/company*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")

4.  在 **設定單一登入在 Intacct** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Intacct 公司網站。

6.  按一下 [公司]**** 索引標籤，然後按一下 [公司資訊]****。

    ![公司](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")

7.  按一下 [安全性]**** 索引標籤，然後按一下 [編輯]****。

    ![安全性](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")

8.  在 [單一登入 (SSO)]**** 區段中，執行下列步驟：

    ![單一登入](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")

    1.  選取 [啟用單一登入]****。
    2.  在 [身分識別提供者類型]****，選取 **SAML 2.0**。
    3.  在 Azure 入口網站中的 [設定在 Intacct 單一登入]**** 對話方塊頁面上，複製 [簽發者 URL]**** 值，然後將它貼到 [簽發者]**** 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 Intacct 單一登入]**** 對話方塊頁面上，複製 [遠端登入 URL]**** 值，然後將它貼至 [登入 URL]**** 文字方塊中。
    5.  從您下載的憑證建立「Base-64 編碼」****檔案。
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 Base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [憑證]**** 文字方塊中。
    7.  按一下 [儲存]****。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Intacct，則必須將他們佈建到 Intacct。  
在 Intacct 的情況下，佈建是手動工作。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **Intacct** 租用戶。

2.  按一下 [公司]**** 索引標籤，然後按一下 [使用者]****。

    ![使用者](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")

3.  按一下 [新增]**** 索引標籤

    ![新增](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")

4.  在 [使用者資訊]**** 區段中，執行下列步驟：

    ![使用者資訊](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

    1.  在 [使用者識別碼]****、[姓氏]****、[名字]****、[電子郵件地址]****、[職稱]**** 和 [電話]****，輸入要佈建的 Azure AD 帳戶所對應的資訊。
    2.  選取您要佈建之 Azure AD 帳戶的 [系統管理員權限]****。
    3.  按一下 [儲存]****。
        >[AZURE.NOTE] AAD 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

>[AZURE.NOTE] 您可以使用任何其他的 Intacct 使用者帳戶建立工具或 Intacct 提供的 API，來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要指派使用者給 Intacct，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Intacct * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




