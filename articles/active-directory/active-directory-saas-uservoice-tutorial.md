<properties 
    pageTitle="教學課程：Azure Active Directory 與 UserVoice 整合 | Microsoft Azure" 
    description="了解如何使用 UserVoice 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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


# 教學課程：Azure Active Directory 與 UserVoice 整合

本教學課程的目的是要示範 Azure 與 UserVoice 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   UserVoice 租用戶

完成本教學課程之後, 您已指派給 UserVoice 的 Azure AD 使用者將能夠登入您的 UserVoice 公司網站 (服務提供者起始登入)，在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 UserVoice 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## 啟用 UserVoice 的應用程式整合

本節的目的是要說明如何啟用 UserVoice 的應用程式整合。

### 若要啟用 UserVoice 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]**** 中，輸入 **UserVoice**。

    ![應用程式資源庫](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")

7.  在結果窗格中，選取 [UserVoice]****，然後按一下 [完成]**** 以新增應用程式。

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 UserVoice。  
設定 UserVoice 的單一登入需要您從憑證擷取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **UserVoice** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 UserVoice** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **UserVoice 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」 * https://\<tenant-name\>。UserVoice.com* 」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")

4.  在 **UserVoice 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\UserVoice.cer**。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 UserVoice 公司網站。

6.  在頂端工具列中，按一下 [設定]，然後從功能表選取 [Web 入口網站]。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")

7.  在 [Web 入口網站]**** 索引標籤的 [使用者驗證]**** 區段中，按一下 [編輯]****，以開啟 [編輯使用者驗證]**** 對話頁面。

    ![Web 入口網站](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")

8.  在 [編輯使用者驗證]**** 對話頁面上執行下列步驟：

    ![編輯使用者驗證](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")

    1.  按一下 [單一登入 (SSO)]****。
    2.  在 Azure 入口網站中的 [在 UserVoice 設定單一登入]**** 對話頁面上， 複製**遠端登入 URL** 值，然後將它貼至 [SSO 遠端登入]**** 文字方塊中。
    3.  在 Azure 入口網站中的 [在 UserVoice 設定單一登入]**** 對話頁面上， 複製**遠端登出 URL** 值，然後將它貼至 [SSO 遠端登出]**** 文字方塊中。
    4.  從匯出的憑證複製**指紋** 值，然後將它貼入 [目前憑證 SHA1 指紋]**** 文字方塊。
        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    5.  按一下 [儲存驗證設定]****。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## 設定使用者佈建

若要讓 Azure AD 使用者可以登入 UserVoice，則必須將他們佈建到 UserVoice。  
UserVoice 需以手動的方式佈建。

### 若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **UserVoice** 租用戶。

2.  移至 [設定]****。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")

3.  按一下 [一般]****。

4.  按一下 [代理程式和權限]****。

    ![代理程式和權限](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")

5.  按一下 [加入管理員]****。

    ![加入管理員](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")

6.  在 [邀請管理員]**** 對話方塊中，執行下列步驟：

    ![邀請管理員](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")

    1.  在 [電子郵件] 文字方塊中，輸入您要佈建之帳戶的電子郵件地址，然後按一下 [加入]****。
    2.  按一下 [邀請]****。

>[AZURE.NOTE] 您可以使用任何其他的 UserVoice 使用者帳戶建立工具或 UserVoice 提供的 API 來佈建 AAD 使用者帳戶。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 UserVoice，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **UserVoice * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。

    ![指派使用者](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




