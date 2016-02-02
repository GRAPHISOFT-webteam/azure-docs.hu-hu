<properties 
    pageTitle="教學課程：Azure Active Directory 與 TeamSeer 整合 | Microsoft Azure" 
    description="了解如何使用 TeamSeer 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# 教學課程：Azure Active Directory 與 TeamSeer 整合

本教學課程的目的是要示範 Azure 與 TeamSeer 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   TeamSeer 租用戶

完成本教學課程之後, 您已指派給 TeamSeer 的 Azure AD 使用者將能夠登入位於您 TeamSeer 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 TeamSeer 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

## 啟用 TeamSeer 的應用程式整合

本節的目的是要說明如何啟用 TeamSeer 的應用程式整合。

### 若要啟用 TeamSeer 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]**** 中，輸入 **TeamSeer**。

    ![應用程式資源庫](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Application Gallery")

7.  在結果窗格中，選取 [TeamSeer]****，然後按一下 [完成]**** 以加入應用程式。

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TeamSeer。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **TeamSeer** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 TeamSeer** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **TeamSeer 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*http://www.teamseer.com/companyid*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configure App URL")

4.  在 **設定單一登入在 TeamSeer** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 TeamSeer 公司網站。

6.  移至 [HR 管理]****。

    ![HR 管理](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")

7.  按一下 [設定]****。

    ![設定](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Setup")

8.  按一下 [設定 SAML 提供者詳細資料]****。

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")

9.  在 [SAML 提供者詳細資料] 區段中，執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")

    1.  在 Azure 入口網站中的 [在 TeamSeer 設定單一登入]**** 對話頁面上，複製**單一登入服務 URL** 值，然後將它貼至 [ URL]**** 文字方塊中。
    2.  從您下載的憑證建立「Base-64 編碼」****檔案。
        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    3.  在記事本中開啟 base-64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [IdP 公開憑證]**** 文字方塊中。

10. 若要完成 SAML 提供者的設定，請執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")

    1.  在 [測試電子郵件地址]**** 中輸入測試使用者的電子郵件地址。
    2.  在 [簽發者]**** 文字方塊中輸入服務提供者的簽發者 URL。
    3.  按一下 [儲存]****。

11. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

## 設定使用者佈建

為了讓 Azure AD 使用者登入 TeamSeer，他們必須佈建到 ShiftPlanning 中。  
TeamSeer 需以手動的方式佈建。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **TeamSeer** 公司網站。

2.  執行下列步驟：

    ![HR 管理](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")

    1.  移至 **HR 管理 \ > 使用者**。
    2.  按一下 [執行新增使用者精靈]****。

3.  在 [使用者詳細資料]**** 區段中，執行下列步驟：

    ![使用者詳細資料](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [名字]****、[姓氏]**** 和 [使用者名稱 (電子郵件地址)]****。
    2.  按 [下一步]****。

4.  請依照螢幕上的指示來加入新的使用者，然後按一下 [完成]****。

>[AZURE.NOTE] 您可以使用任何其他的 TeamSeer 使用者帳戶建立工具或 TeamSeer 提供的 API 來佈建 Azure AD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 TeamSeer，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **TeamSeer * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




