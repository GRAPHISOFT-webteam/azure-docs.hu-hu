<properties 
    pageTitle="教學課程：Azure Active Directory 與 Egnyte 整合 | Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的 Egnyte，來啟用單一登入、 自動化佈建和更多功能!" 
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


# 教學課程：Azure Active Directory 與 Egnyte 整合

本教學課程的目的是要示範 Azure 與 Egnyte 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 Egnyte 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Egnyte 的 Azure AD 使用者將能夠單一登入應用程式在您的 Egnyte 公司網站 (服務提供者起始登入)，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Egnyte 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenario")
## 啟用 Egnyte 的應用程式整合

本節的目的是要說明如何啟用 Egnyte 的應用程式整合。

### 若要啟用 Egnyte 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **egnyte**。

    ![應用程式資源庫](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Application Gallery")

7.  在結果窗格中，選取 [Egnyte]****，然後按一下 [完成]**** 來加入應用程式。

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Egnyte。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Egnyte** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Egnyte** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Egnyte 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.egnyte.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configure App URL")

4.  在 **設定單一登入在 Egnyte** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Egnyte 公司網站。

6.  按一下 [設定]****。

    ![設定](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Settings")

7.  在功能表中按一下 [設定]****。

    ![設定](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Settings")

8.  按一下 [組態]**** 索引標籤，然後按一下 [安全性]****。

    ![安全性](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Security")

9.  在 [單一登入驗證]**** 區段中，執行下列步驟：

    ![單一登入驗證](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")

    1.  針對 [單一登入驗證]****，選取 **SAML 2.0**。
    2.  針對 [識別提供者]****，選取 [AzureAD]****。
    3.  在 Azure 網站中，在 **Egnyte 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 ** 身分識別提供者登入 URL ** 文字方塊。
    4.  在 Azure 入口網站中的 [設定在 Egnyte 單一登入]**** 對話頁面上，複製 [實體識別碼]**** 值，然後將其貼至 [識別提供者實體識別碼]**** 文字方塊中。
    5.  從您下載的憑證建立「Base-64 編碼」****檔案。
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [識別提供者憑證]**** 文字方塊中。
    7.  針對 [預設使用者對應]****，選取 [電子郵件地址]****。
    8.  針對 [使用網域指定的簽發者值]****，選取 [停用]****。
    9.  按一下 [儲存]****。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configure Single Sign-On")
## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 Egnyte，則必須將他們佈建到 Egnyte。  
Egnyte 需以手動的方式佈建。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Egnyte** 公司網站。

2.  移至 **設定 \ > 使用者和群組**。

3.  按一下 [新增使用者]****，然後選取您想要加入的使用者類型。

    ![使用者](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Users")

4.  在 [新的標準使用者]**** 區段中，執行下列步驟：

    ![新的標準使用者](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")

    1.  輸入您要佈建之有效 Azure Active Directory 帳戶的 [電子郵件]****、[使用者名稱]**** 以及其他詳細資料。
    2.  按一下 [儲存]****。

    >[AZURE.NOTE] Azure Active Directory 帳戶持有者將會收到電子郵件通知。

>[AZURE.NOTE] 您可以使用任何其他的 Egnyte 使用者帳戶建立工具或 Egnyte 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 Egnyte，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Egnyte * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




