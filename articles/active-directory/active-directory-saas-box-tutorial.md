<properties 
    pageTitle="教學課程：Azure Active Directory 與 Box 整合 | Microsoft Azure" 
    description="了解如何使用與 Azure Active Directory 的方塊，來啟用單一登入、 自動化佈建和更多功能!" 
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





# 教學課程：Azure Active Directory 與 Box 整合

本教學課程的目的是要示範 Azure 與 Box 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Box 中的測試租用戶

完成本教學課程之後, 您已指派給] 方塊中的 Azure AD 使用者將能夠單一登入應用程式在您的方塊公司網站 (服務提供者起始登入)，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Box 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")



## 啟用 Box 的應用程式整合

本節的目的是概述如何啟用 Box 的應用程式整合。

### 若要啟用 Box 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **Box**。

    ![應用程式庫](./media/active-directory-saas-box-tutorial/IC701023.png "Application gallery")

7.  在結果窗格中，選取 [Box]****，然後按一下 [完成]**** 以加入應用程式。

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



## 設定單一登入

本節的目標在於概述如何讓使用者透過他們的帳戶驗證至方塊 SAML 通訊協定為基礎的同盟，使用 Azure AD 中。 <br>
此程序的一部分，您必須將中繼資料上傳至 Box.com。

### 若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **方塊** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 ** 設定單一登入 ** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")

2.  在 **您希望使用者如何登入方塊** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Box 租用戶 URL** 文字方塊中，輸入您的 Box 租用戶 URL (例如: https://<mydomainname>。 box.com)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")

4.  在 **在 Box 設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，，然後在本機電腦上的資料檔案。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")

5.  將該中繼資料檔案轉寄給 Box 支援小組。 支援小組需要為您設定單一登入。

6.  選取單一登入設定確認，然後再按 **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
## 設定使用者佈建

本節的目的是要說明如何對 Box 啟用 Active Directory 使用者帳戶的佈建。

### 若要設定單一登入，請執行下列步驟：

1. 在 Azure 管理入口網站中，在 **方塊** 應用程式整合頁面上，按一下 [ **設定使用者佈建** 開啟 **設定使用者佈建** ] 對話方塊。<br> <br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")

2. 在 **啟用使用者佈建到方塊** 對話方塊頁面上，按一下 [ **啟用使用者佈建**。<br><br>  ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")

3. 在 **授權 Box 存取權登入** 頁面上，提供必要的認證，然後按一下 **授權**。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")

4. 按一下 [ **授權 Box 存取權** 授權這項作業，並返回 Azure 管理入口網站。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")

5. 若要完成組態，請按一下 [完成] 按鈕。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")



## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要指派使用者給 Box，請執行下列步驟：

1. 在 Azure AD 入口網站中建立測試帳戶。

2. 在 **方塊 * * 應用程式整合頁面上，按一下 [* * 將使用者指派**。<br><br> ![指派使用者](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。<br><br> ![Yes](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")


請等候 10 分鐘並確認帳戶已同步至 Box。

在第一個驗證步驟中，您可以在 Azure 管理入口網站的 Box 應用程式整合頁面上，按一下 [儀表板] 來檢查佈建狀態。

<br><br> ![儀表板](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

成功完成的使用者佈建週期會以相關狀態表示：

<br><br> ![整合狀態](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")


在 Box 租用戶中，已同步處理的使用者會列在 [管理主控台]**** 的 [管理使用者]**** 之下。

<br><br> ![整合狀態](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")


## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)




